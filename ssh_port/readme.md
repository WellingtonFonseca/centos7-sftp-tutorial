```
# cat /etc/redhat-release

CentOS Linux release 7.8.2003 (Core)
```

**Importante!** Caso seu acesso não seja o `root` direto, sempre adicionar `sudo` no início do comando

Caso queira instalar o nano para seguir o tutorial abaixo: `$ yum install -y nano`

Vamos editar o arquivo `/etc/ssh/sshd_config`. Mas antes de qualquer coisa, sempre criar uma cópia de segurança.
```
$ cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
$ nano /etc/ssh/sshd_config
```
Localize a linha: `#Port 22`

Abaixo dela adicione: `Port 8123` 8123 foi a porta que escolhi para este exemplo, mas pode escolher outra.

Salve a inclusão, no nano `ctrl x` **→** `y` (yes para confirmar a edição) **→** `enter`

por padrão a porta 22 é quem fica habilitada

```
$ semanage port -l | grep ssh
ssh_port_t                     tcp      22
```

vamos alterar, adicionando a nossa porta

```
$ sudo semanage port -a -t ssh_port_t -p tcp 8123
```
```
$ semanage port -l | grep ssh
ssh_port_t                     tcp      8123, 22
```

agora vamos liberar a porta no firewall:

```
$ sudo firewall-cmd --add-port=8123/tcp --permanent
$ sudo firewall-cmd --reload
```

**caso não tenha o firewalld instalado**
```
$ sudo yum -y install firewalld
$ sudo systemctl enable --now firewalld
$ sudo firewall-cmd --add-port=8123/tcp --permanent
$ sudo firewall-cmd --reload
```

agora remova o service ssh do firewall:
```
$ sudo firewall-cmd --remove-service=ssh --permanent
$ sudo firewall-cmd --reload
```

reinicie o sshd:
```
$ sudo systemctl restart sshd
```

verifique a porta, se estiver aparecendo a porta que criamos está tudo OK:
```
$ netstat -tunl | grep 8123
tcp        0      0 0.0.0.0:8123           0.0.0.0:*               LISTEN     
tcp6       0      0 :::8123                :::*                    LISTEN  
```
