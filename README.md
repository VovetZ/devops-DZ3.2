# devops-DZ3.2

# Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"

>1. Какого типа команда `cd`? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
### Ответ
```bash
vagrant@vagrant:~$ type cd
cd is a shell builtin
```
`cd` должен быть встроенной командой, потому что сам Bash должен изменить свой текущий рабочий каталог, а не дочерний подпроцесс.

>2. Какая альтернатива без pipe команде `grep <some_string> <some_file> | wc -l`? `man grep` поможет в ответе на этот вопрос. Ознакомьтесь с [документом](http://www.smallo.ruhr.de/award.html) о других подобных некорректных вариантах использования pipe. 
### Ответ
```bash
grep -c <some_string> <some_file>
```
>3. Какой процесс с PID `1` является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?
### Ответ
```bash
vagrant@vagrant:~$ ps -f 1
UID          PID    PPID  C STIME TTY      STAT   TIME CMD
root           1       0  6 16:28 ?        Ss     0:13 /sbin/init
```

>4. Как будет выглядеть команда, которая перенаправит вывод stderr `ls` на другую сессию терминала?
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
>5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.
### Ответ
Получится. Пример такой
```bash
vagrant@vagrant:~$ echo 'This is normal string' > in.txt
vagrant@vagrant:~$ rev < in.txt > out.txt
vagrant@vagrant:~$ cat out.txt
gnirts lamron si sihT
```
>6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?
### Ответ

Получится
```bash
vagrant@vagrant:~$ echo 'Hello!' > /dev/tty1
```
В другой сессии видим
```bash
vagrant@vagrant:~$ tty
/dev/tty1
vagrant@vagrant:~$ Hello!
```

>7. Выполните команду `bash 5>&1`. К чему она приведет? Что будет, если вы выполните `echo netology > /proc/$$/fd/5`? Почему так происходит?
### Ответ

Таким способом мы запустили процесс `bash`, в котором обьявили новый файловый дескриптор с номером 5 и перенаправили его на `stdout`. И отправляя какую-либо информацию на `FD 5`, мы на самом деле направляем ее на `stdout` и , соотвественно, видим ее на экране.
```bash
vagrant@vagrant:~$ bash 5>&1
vagrant@vagrant:~$ echo netology > /proc/$$/fd/5
netology
```
>8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от `|` на stdin команды справа.
Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.
### Ответ

Пример с промежуточным дескриптором. В итоге получаем перевернутую через pipe строку из `stderr`. При этом `stdout` тоже видим
```bash
vagrant@vagrant:~$ (echo 'To stderr' && ls -wrong_param) 2>&1 1>&5 5>&2 | rev
To stderr
’marap_gnor‘ :htdiw enil dilavni :sl
```
>9. Что выведет команда `cat /proc/$$/environ`? Как еще можно получить аналогичный по содержанию вывод?
### Ответ
Команда выведет список переменных окружения и их значения
Более удобный аналог - встроенная команда `set`
	      
>10. Используя `man`, опишите что доступно по адресам `/proc/<PID>/cmdline`, `/proc/<PID>/exe`.
### Ответ
```bash
man 5 proc
```
`/proc/[pid]/cmdline`
Этот файл, доступный только для чтения, содержит полную командную строку для процесса, если только процесс не является зомби. В последнем случае, в этом файле ничего нет: то есть чтение этого файла вернет 0 символов.
`/proc/<PID>/exe`
этот файл представляет собой символическую ссылку, содержащую фактический путь к выполняемой команде. Эта
символическая ссылка может быть разыменована в обычном режиме; попытка открыть ее приведет к открытию исполняемого файла.

>11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью `/proc/cpuinfo`.
### Ответ

SSE 4.2

```bash
vagrant@vagrant:~$ grep -oh 'sse[[:alnum:]|[:punct:]]*' /proc/cpuinfo | sort | uniq
sse
sse2
sse3
sse4_1
sse4_2
```
>12. При открытии нового окна терминала и `vagrant ssh` создается новая сессия и выделяется pty. Это можно подтвердить командой `tty`, которая упоминалась в лекции 3.2. Однако:

    ```bash
	vagrant@netology1:~$ ssh localhost 'tty'
	not a tty
    ```

	Почитайте, почему так происходит, и как изменить поведение.
### Ответ 
Нужно запускать с опцией принудительного выделения псевдо-терминала (-t), используется для запуска программ на удаленном хосте (а не получения удаленного сеанса оболочки

```bash
vagrant@vagrant:~$ ssh localhost 'tty'
vagrant@localhost's password:
not a tty
vagrant@vagrant:~$ ssh -t localhost 'tty'
vagrant@localhost's password:
/dev/pts/2
Connection to localhost closed.
```
>13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись `reptyr`. Например, так можно перенести в `screen` процесс, который вы запустили по ошибке в обычной SSH-сессии.
### Ответ
Запустил в одной из сессий ping
```bash
vk@vk-desktop:/etc/netplan$ ping yandex.ru
PING yandex.ru (5.255.255.80) 56(84) bytes of data.
64 bytes from yandex.ru (5.255.255.80): icmp_seq=1 ttl=250 time=4.97 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=2 ttl=250 time=4.99 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=3 ttl=250 time=4.95 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=4 ttl=250 time=5.03 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=5 ttl=250 time=5.05 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=6 ttl=250 time=4.96 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=7 ttl=250 time=4.91 ms
64 bytes from yandex.ru (5.255.255.80): icmp_seq=8 ttl=250 time=4.96 ms
```
Далее,в другой сессии, после `sudo apt install reptyr` и редактирования /etc/sysctl.d/10-ptrace.conf (kernel.yama.ptrace_scope исправить на 0 )
Так и не удалось добиться, чтобы `reptyr` заработал (((
```bash
vk@vk-desktop:~$ ps -ef | grep ping
vk          2418    2059  0 июл10 ?     00:00:01 /usr/libexec/gsd-housekeeping
vk         27863   20447  0 19:40 pts/1    00:00:00 ping yandex.ru
vk         28059   27865  0 19:41 pts/2    00:00:00 grep --color=auto ping

vagrant@vagrant:~$ reptyr -T 1049
Unable to attach to pid 1225: Operation not permitted
```
>14. `sudo echo string > /root/new_file` не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без `sudo` под вашим пользователем. Для решения данной проблемы можно использовать конструкцию `echo string | sudo tee /root/new_file`. Узнайте что делает команда `tee` и почему в отличие от `sudo echo` команда с `sudo tee` будет работать.
### Ответ
`tee` - считывание со стандартного ввода и запись в стандартный вывод и файлы. Это НЕ built-in команда, в отличие от `echo`. Поэтому `sudo` и будет работать, т.к. будет запущен отдельный процесс
```bash
vagrant@vagrant:~$ type tee
tee is /usr/bin/tee
vagrant@vagrant:~$ type echo
echo is a shell builtin
```

