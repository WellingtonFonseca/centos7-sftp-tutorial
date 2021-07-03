`$ semanage port -l | grep ssh`

se aparecer: `-bash: semanage: command not found`

`yum provides /usr/sbin/semanage`

```
01 Loaded plugins: fastestmirror
02 Loading mirror speeds from cached hostfile
03  * base: us.mirror.nsec.pt
04  * extras: mirror.ette.biz
05  * updates: packages.oit.ncsu.edu
06 policycoreutils-python-2.5-34.el7.x86_64 : SELinux policy core python utilities
07 Repo        : base
08 Matched from:
09 Filename    : /usr/sbin/semanage
10 ...
```

aqui pra mim apareceu como necessidade `policycoreutils-python-2.5-34.el7.x86_64`
então para instalação vou utilizar `yum install policycoreutils-python`

digo que pra mim, pois para outros pode aparecer `policycoreutils-python-utils-2.9-3.el8.noarch`
analise como está no seu e instale, e por fim, tente novamente: `$ semanage port -l | grep ssh`
