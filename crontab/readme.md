```
GNU nano 2.3.1                                                     File: /etc/crontab                                                                                                                 
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin:/bin/false:/usr/local/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

PYTHONPATH=/caminho/da/path/class:/outro/caminho/da/path/class

# executar processo hora a hora
0 */1 * * * root /caminho/da/venv/bin/python3.8 /home/task/hora_hora_atualizar.py

# executar processo em uma hora especifica
10 6 * * * root /caminho/da/venv/bin/python3.8 /home/task/dia_dia_atualizar.py

0 1 * * * root /caminho/da/venv/bin/python3.8 /home/task/dia_dia_telegram_deletar.py

# executar processo em um dia especifico
0 3 1 * * root /caminho/da/venv/bin/python3.8 /home/task/mes_mes_atualizar.py

```
