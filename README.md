# devops-DZ3.2

# Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"

1. Какого типа команда `cd`? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
### Ответ
```bash
vagrant@vagrant:~$ type cd
cd is a shell builtin
```
`cd` должен быть встроенной командой, потому что сам Bash должен изменить свой текущий рабочий каталог, а не дочерний подпроцесс.

2. Какая альтернатива без pipe команде `grep <some_string> <some_file> | wc -l`? `man grep` поможет в ответе на этот вопрос. Ознакомьтесь с [документом](http://www.smallo.ruhr.de/award.html) о других подобных некорректных вариантах использования pipe. 
### Ответ
```bash
grep -c <some_string> <some_file>
```
3. Какой процесс с PID `1` является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?
### Ответ
```bash
vagrant@vagrant:~$ ps -f 1
UID          PID    PPID  C STIME TTY      STAT   TIME CMD
root           1       0  6 16:28 ?        Ss     0:13 /sbin/init
```

4. Как будет выглядеть команда, которая перенаправит вывод stderr `ls` на другую сессию терминала?
### Ответ
Из сессии **0** отправляем вывод в сессию **1**
```bash
vagrant@vagrant:/$ tty
/dev/pts/0
vagrant@vagrant:/$ sudo ls -l > /dev/pts/1
```
```bash
vagrant@vagrant:~$ tty
/dev/pts/1
vagrant@vagrant:~$ total 2009164
lrwxrwxrwx   1 root    root             7 Aug 24  2021 bin -> usr/bin
drwxr-xr-x   4 root    root          4096 Dec 19  2021 boot
drwxr-xr-x   2 root    root          4096 Dec 19  2021 cdrom
drwxr-xr-x  19 root    root          3980 Jun 24 16:29 dev
drwxr-xr-x  98 root    root          4096 Jun 24 16:29 etc
drwxr-xr-x   3 root    root          4096 Dec 19  2021 home
lrwxrwxrwx   1 root    root             7 Aug 24  2021 lib -> usr/lib
lrwxrwxrwx   1 root    root             9 Aug 24  2021 lib32 -> usr/lib32
lrwxrwxrwx   1 root    root             9 Aug 24  2021 lib64 -> usr/lib64
lrwxrwxrwx   1 root    root            10 Aug 24  2021 libx32 -> usr/libx32
drwx------   2 root    root         16384 Dec 19  2021 lost+found
drwxr-xr-x   2 root    root          4096 Aug 24  2021 media
drwxr-xr-x   2 root    root          4096 Aug 24  2021 mnt
drwxr-xr-x   3 root    root          4096 Dec 19  2021 opt
dr-xr-xr-x 165 root    root             0 Jun 24 16:29 proc
drwx------   4 root    root          4096 Dec 19  2021 root
drwxr-xr-x  28 root    root           860 Jun 24 16:35 run
lrwxrwxrwx   1 root    root             8 Aug 24  2021 sbin -> usr/sbin
drwxr-xr-x   7 root    root          4096 Jun 23 17:58 snap
drwxr-xr-x   2 root    root          4096 Aug 24  2021 srv
-rw-------   1 root    root    2057306112 Dec 19  2021 swap.img
dr-xr-xr-x  13 root    root             0 Jun 24 16:28 sys
drwxrwxrwt  10 root    root          4096 Jun 24 16:35 tmp
drwxr-xr-x  15 root    root          4096 Aug 24  2021 usr
drwxrwxrwx   1 vagrant vagrant       4096 Jun 19 05:25 vagrant
drwxr-xr-x  13 root    root          4096 Aug 24  2021 var
```
5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.
### Ответ
Получится. Пример такой
```bash
vagrant@vagrant:~$ echo 'This is normal string' > in.txt
vagrant@vagrant:~$ rev < in.txt > out.txt
vagrant@vagrant:~$ cat out.txt
gnirts lamron si sihT
```
6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?
### Ответ
7. Выполните команду `bash 5>&1`. К чему она приведет? Что будет, если вы выполните `echo netology > /proc/$$/fd/5`? Почему так происходит?
### Ответ

Таким способом мы запустили процесс `bash`, в котором обьявили новый файловый дескриптор с номером 5 и перенаправили его на `stdout`. И отправляя какую-либо информацию на `FD 5`, мы на самом деле направляем ее на `stdout` и , соотвественно, видим ее на экране.
```bash
vagrant@vagrant:~$ bash 5>&1
vagrant@vagrant:~$ echo netology > /proc/$$/fd/5
netology
```
9. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от `|` на stdin команды справа.
Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.
### Ответ

Пример с промежуточным дескриптором. В итоге получаем перевернутую через pipe строку из `stderr`. При этом `stdout` тоже видим
```bash
vagrant@vagrant:~$ (echo 'To stderr' && ls -wrong_param) 2>&1 1>&5 5>&2 | rev
To stderr
’marap_gnor‘ :htdiw enil dilavni :sl
```
10. Что выведет команда `cat /proc/$$/environ`? Как еще можно получить аналогичный по содержанию вывод?
### Ответ
Команда выведет список переменных окружения и их значения
Более удобный аналог - встроенная команда `set`
*set [--abefhkmnptuvxBCEHPT] [-o option-name] [arg ...]
       set [+abefhkmnptuvxBCEHPT] [+o option-name] [arg ...]
              Without  options,  the name and value of each shell variable are displayed in a format that can be reused as input for
              setting or resetting the currently-set variables.*
	      
11. Используя `man`, опишите что доступно по адресам `/proc/<PID>/cmdline`, `/proc/<PID>/exe`.
### Ответ
```bash
```
12. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью `/proc/cpuinfo`.
### Ответ
```bash
```
13. При открытии нового окна терминала и `vagrant ssh` создается новая сессия и выделяется pty. Это можно подтвердить командой `tty`, которая упоминалась в лекции 3.2. Однако:

    ```bash
	vagrant@netology1:~$ ssh localhost 'tty'
	not a tty
    ```

	Почитайте, почему так происходит, и как изменить поведение.
### Ответ
14. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись `reptyr`. Например, так можно перенести в `screen` процесс, который вы запустили по ошибке в обычной SSH-сессии.
15. `sudo echo string > /root/new_file` не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без `sudo` под вашим пользователем. Для решения данной проблемы можно использовать конструкцию `echo string | sudo tee /root/new_file`. Узнайте что делает команда `tee` и почему в отличие от `sudo echo` команда с `sudo tee` будет работать.
