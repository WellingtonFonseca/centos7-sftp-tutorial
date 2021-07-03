# CentOS SFTP Tutorial Pessoal

```
$ cat /etc/redhat-release

CentOS Linux release 7.8.2003 (Core)
```

**Importante!** Caso seu acesso não seja o `root` direto, sempre adicionar `sudo` no início do comando

Caso queira instalar o nano para seguir o tutorial abaixo: `yum install -y nano`

Antes de iniciar qualquer procedimento é necessário fazer alteração no arquivo `/etc/shells`. Utilizando um editor da sua preferência
(~~que no meu caso é o nano~~), faça o seguinte:

`$ nano /etc/shells`

Adicione a seguinte linha ao final do arquivo:

`/bin/false`

Salve a inclusão, no nano `ctrl x` **→** `y` (yes para confirmar a edição) **→** `enter`

## Criando o SFTP Grupo
Será criado um grupo especialmente para os usuários SFTP, desta forma quando editarmos o `.config` ao invés de declararmos usuário
por usuário, faremos a declaração do grupo todo.

`$ groupadd sftponly`

## Criando um usuário dentro do grupo
Substitua o `username` pelo o nome do usuário que quer criar

`$ useradd -g sftponly -s /bin/false -m -d /home/username username`

**descrição:**

`useradd`  adicionar/criar um usuário

`-g sftponly`  adicionar este novo usuário dentro do grupo `sftponly`

`-s /bin/false`  informar que este usuário não terá acesso via ssh

`-m`  criar um diretório home

`-d /home/username`  informando o caminho onde este diretório home será criado

`username`  nome do usuário de acesso

Agora vamos criar uma senha para este usuário, será solicitado duas vezes

`$ passwd username`

**Observação:** Se o seu caso seja de já obter um usuário e queira apenas migrá-lo para dentro do grupo `sftponly`:

`$ usermod -g sftponly -s /bin/false username`

## Manipulando os diretórios

Esta é uma parte muito **importante**, que me fez criar este tutorial pessoal. O **diretório raiz** até onde entendi, necessita de ser 
propriedade do `root` e somente o **subdiretório** é quem deve ser do `username`. Desta forma quando um usuário realizar o login
e houver outros diretórios dentro da `home`, ele poderá vê-los, ou seja, o acesso `/` será diretamente a `/home` e não a pasta `/home/username`. 
O que mudará é que a permissão de acesso, escrita, leitura e execução será somente na pasta `/home/username`.

Pesquisei muitos tutoriais, sempre o mesmo resultado, ~~formatei tanto o meu server pensando que estava fazendo errado que gastei toda 
a minha cota de vezes que poderia formatar~~.

Recapitulando, ao acesso o `sftp` o usuário poderá ver todos os diretórios da `/home` porém ele só terá acesso, escrita, leitura e execução
somente dentro da `/home/username`, caso ele tente acessar outro diretório, um erro será apresentado

```
$ cd /home
$ [home] ls
$ username
```
Notem que dentro da nossa pasta `/home` teremos o diretório do nosso `username`. Para fins de teste vamos fazer o seguinte antes de continuar:
```
$ [home] mkdir testando
$ [home] ls
$ username testando
```
Agora teremos dois diretórios dentro da `/home`, vamos continuar o processo, mais tarde vou explicar a diretório `testando`:
```
$ [home] chown root: /home
$ [home] chown username:sftponly /home/username
$ [home] chmod 700 /home/username
$ [home] chmod 700 /home/testando
```

**descrição:**

`chown`  declarando que é o proprietário do diretório

`chmod 700` declarando que a permissão de leitura, escrita e execução é de somente do dono do diretório

## Editando .config

Não desista, estamos no final do processo. Agora vamos editar o arquivo `/etc/ssh/sshd_config`. Mas antes de qualquer coisa,
sempre criar uma cópia de segurança.
```
$ cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
$ nano /etc/ssh/sshd_config
```
Localize a linha: `Subsystem     sftp    /usr/libexec/openssh/sftp-server`

Altere para: `Subsystem sftp internal-sftp`

E adicione ao final do arquivo:
```
Match Group sftponly
  ChrootDirectory /home
  ForceCommand internal-sftp
  AllowTcpForwarding no
  X11Forwarding no
  PasswordAuthentication yes
  PermitTunnel no
  AllowAgentForwarding no
```

Salve a inclusão, no nano `ctrl x` **→** `y` (yes para confirmar a edição) **→** `enter`

Reinicie o serviço: `$ systemctl restart sshd`

Pronto! Vamos ao teste, deslogue do server e tente novamente acessar via ssh, agora com o usuário que criamos: 
```
$ ssh username@ip.do.servidor
username@ip.do.servidor's password:
This service allows sftp connections only.
Connection to 147.135.124.113 closed.
```
Se apresentou a mensagem acima, perfeito, o seu sftp foi habilitado com sucesso.

## Diretório testando
Agora para que entenda, todos os tutoriais indicam para que seja feito o seguinte comando `chmod 755 /home/username`, porém este
comando da permissão para outros usuários `sftp` (que caso crie), possam ver, ler e não editar todos os arquivos que 
estiverem dentro do diretório `username`. Porém, com o `chmod 700` não, ele pode até ver o diretório `testando`, mas se tentar 
acessar será negado.

Não entendeu? Use seu client (exemplo: Filezilla), acesse o server e tente entrar no diretório testando.

Depois de tentar, mude a permissão `$ chmod 755 /home/testando`, acesse novamente via client e tente entrar no diretório. Pronto,
desta forma ficará mais fácil de entender!

Toda a vez que criar um usuário sempre procure utilizar o processo abaixo, evitando que outro usuário espione arquivos de
outros usuários. Claro, se você criar um sftp para diversos usuários diferentes e individualmente.
```
$ chown novousername:sftponly /home/novousername$
$ chmod 700 /home/novousername
```
