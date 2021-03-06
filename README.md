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

В итоге все получилось ))

Запустил в одной из сессий `top`, далее `Ctrl+Z`

```bash
MiB Mem :  15957,0 total,  10405,2 free,   2760,9 used,   2790,9 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.  12840,2 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                         
   2227 root      20   0   24,2g  59608  36760 S   6,7   0,4   0:27.24 Xorg                                            
   2381 vk        20   0 4373892 345904 113452 S   6,7   2,1   0:34.92 gnome-shell                                     
   7855 vk        20   0   13232   4108   3228 R   6,7   0,0   0:00.01 top                                             
      1 root      20   0  168968  14184   8116 S   0,0   0,1   0:05.05 systemd                                         
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd                                        
    ...........................................................                                    
     43 root      25   5       0      0      0 S   0,0   0,0   0:00.00 ksmd                                            
     44 root      39  19       0      0      0 S   0,0   0,0   0:00.00 khugepaged                                      
[1]+  Stopped                 top

vk@vk-desktop:~$ ps -ef | grep top
avahi       1061       1  0 19:44 ?        00:00:00 avahi-daemon: running [vk-desktop.local]
root        1063       1  0 19:44 ?        00:00:00 /usr/sbin/atopacctd
root        1111       1  0 19:44 ?        00:00:00 /usr/bin/atop -R -w /var/log/atop/atop_20220714 600
vk          2922    2140  0 19:45 ?        00:00:00 /usr/libexec/xdg-desktop-portal
vk          2930    2140  0 19:45 ?        00:00:00 /usr/libexec/xdg-desktop-portal-gnome
vk          2947    2140  0 19:45 ?        00:00:00 /usr/libexec/xdg-desktop-portal-gtk
vk          7855    7142  0 20:09 pts/2    00:00:00 top
vk          7874    7142  0 20:10 pts/2    00:00:00 grep --color=auto top

[1]+  Stopped                 top
vk@vk-desktop:~$ disown 7855
bash: warning: deleting stopped job 1 with process group 7855
```
Далее,в другой сессии, после `sudo apt install reptyr` и редактирования `/etc/sysctl.d/10-ptrace.conf` (`kernel.yama.ptrace_scope = 0`) 
```
vk@vk-desktop:~$ screen
vk@vk-desktop:~$ reptyr 7855
MiB Mem :  15957,0 total,  10405,2 free,   2760,9 used,   2790,9 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.  12840,2 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                         
   2227 root      20   0   24,2g  59608  36760 S   6,7   0,4   0:27.24 Xorg                                            
   2381 vk        20   0 4373892 345904 113452 S   6,7   2,1   0:34.92 gnome-shell                                     
   7855 vk        20   0   13232   4108   3228 R   6,7   0,0   0:00.01 top                                             
      1 root      20   0  168968  14184   8116 S   0,0   0,1   0:05.05 systemd                                         
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd                                        
    ...........................................................                                    
     43 root      25   5       0      0      0 S   0,0   0,0   0:00.00 ksmd                                            
     44 root      39  19       0      0      0 S   0,0   0,0   0:00.00 khugepaged       
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

