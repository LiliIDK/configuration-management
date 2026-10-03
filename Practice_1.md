# Практическое занятие №1. Введение, основы работы в командной строке

П.Н. Советов, РТУ МИРЭА

Научиться выполнять простые действия с файлами и каталогами в Linux из командной строки.  
Сравнить работу в командной строке Windows и Linux.

## Задача 1

Вывести отсортированный в алфавитном порядке список имен пользователей в файле `passwd`.

Команда:

```bash
grep -o '^[^:]*' /etc/passwd | sort
```

Результат:

```console
DESKTOP-E8KSON6:/etc# grep -o '^[^:]*' passwd | sort
bin
cron
daemon
ftp
games
guest
halt
lp
mail
news
nobody
ntp
root
shutdown
sshd
sync
uucp
```
## Задача 2

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```  
Команда:

```
cat protocols | awk '{print $2, $1}' | sort -k1 -nr | head -5
```

Результат:

```
DESKTOP-E8KSON6:/etc# cat protocols | awk '{print $2, $1}' | sort -nr | head -5
262 mptcp
143 ethernet
142 rohc
141 wesp
140 shim6
```
## Задача 3

Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):
Решение:
```
#!/bin/bash

text="$1"

len=${#text}

printf "+"
for ((i=0; i<len+2; i++))
do
    printf "-"
done
printf "+\n"

echo "| $text |"

printf "+"
for ((i=0; i<len+2; i++))
do
    printf "-"
done
printf "+\n"
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# ./banner.sh "Aloha!"
+----------+
| Aloha! |
+----------+
```
## Задача 4
Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).

Пример для hello.c:

```
h hello include int main n printf return stdio void world
```
Решение:
```
DESKTOP-E8KSON6:~/practice1# ls
banner          hello.c         identifiers.sh  reg
DESKTOP-E8KSON6:~/practice1# cat identifiers.sh
#!/bin/bash

file=$1

grep -oE '[A-Za-z_][A-Za-z0-9_]*' "$file" | sort -u | tr '\n' ' '

echo
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# ./identifiers.sh hello.c
h hello include int main n printf return stdio void world
```
## Задача 5
Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin).

Например, пусть программа называется reg:
```
./reg banner
```
В результате для banner задаются правильные права доступа и сам banner копируется в /usr/local/bin.
Решение:
```
DESKTOP-E8KSON6:~/practice1# ls
banner          hello.c         identifiers.sh  reg
DESKTOP-E8KSON6:~/practice1# cat reg
#!/bin/bash

file=$1

chmod +x "$file"
cp "$file" /usr/local/bin/

echo "Команда $file зарегистрирована"
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# ./reg banner
Команда banner зарегистрирована
DESKTOP-E8KSON6:~/practice1# banner "Hello"
+-------+
| Hello |
+-------+
```
Проверим содержимое папки usr/local/bin:
```
DESKTOP-E8KSON6:/usr/local/bin# ls
banner         banner.sh      docker         wsl-bootstrap
DESKTOP-E8KSON6:/usr/local/bin#
```
## Задача 6
Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.
Решение:
```
DESKTOP-E8KSON6:~/practice1# cat check_comments
#!/bin/bash

for file in *.c *.js *.py
do
    if [ -f "$file" ]
    then
        first=$(head -n 1 "$file")

        if [[ "$first" == //* || "$first" == \#* || "$first" == "/*"* ]]
        then
            echo "$file: комментарий есть"
        else
            echo "$file: комментария нет"
        fi
    fi
done
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# chmod +x check_comments
DESKTOP-E8KSON6:~/practice1# ./check_comments
hello.c: комментария нет
```
## Задача 7
Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).
Решение:
```
DESKTOP-E8KSON6:~/practice1# cat duplicates.sh
#!/bin/bash

dir=$1

find "$dir" -type f -exec md5sum {} \; | sort | awk '
{
    if ($1 == prev) {
        if (!shown) print prevline
        print
        shown=1
    } else {
        shown=0
    }

    prev=$1
    prevline=$0
}'
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# cp hello.c hello_copy.c
DESKTOP-E8KSON6:~/practice1# ls
banner          check_comments  duplicates.sh   hello.c         hello_copy.c    identifiers.sh  reg
DESKTOP-E8KSON6:~/practice1# ./duplicates.sh ~/practice1
eecab6d8fd8da8498d123b2f729d07f8  /root/practice1/hello.c
eecab6d8fd8da8498d123b2f729d07f8  /root/practice1/hello_copy.c
```
## Задача 8
Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.
Решение:
```
DESKTOP-E8KSON6:~/practice1# cat archive_ext.sh
#!/bin/bash

ext=$1

find . -type f -name "*.$ext" > files.txt
tar -cf files.tar -T files.txt
rm files.txt
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# ./archive_ext.sh c
DESKTOP-E8KSON6:~/practice1# ls
archive_ext.sh  check_comments  files.tar       hello_copy.c    reg
banner          duplicates.sh   hello.c         identifiers.sh
DESKTOP-E8KSON6:~/practice1# tar -tf files.tar
./hello_copy.c
./hello.c
```
## Задача 9
Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.
Решение:
```
DESKTOP-E8KSON6:~/practice1# cat spaces_to_tabs.sh
#!/bin/bash

input=$1
output=$2

sed 's/    /\t/g' "$input" > "$output"
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# ./spaces_to_tabs.sh input.txt output.txt
DESKTOP-E8KSON6:~/practice1# cat output.txt
1       2
no      tabs    was     here
DESKTOP-E8KSON6:~/practice1# cat -t output.txt
1^I2
no^Itabs^Iwas^Ihere
```
## Задача 10
Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром.
Решение:
```
DESKTOP-E8KSON6:~/practice1# cat empty_files.sh
#!/bin/bash

dir=$1

find "$dir" -type f -empty -name "*.txt"
```
Результат:
```
DESKTOP-E8KSON6:~/practice1# touch empty1.txt
DESKTOP-E8KSON6:~/practice1# ./empty_files.sh ~/practice1
/root/practice1/empty1.txt
```
## Вывод по работе
В ходе практической работы были изучены основные команды Linux и основы написания простых Bash-скриптов. Были выполнены задачи по работе с файлами и каталогами, поиску и сортировке данных, обработке текстовых файлов, поиску дубликатов, архивированию файлов, проверке комментариев и регистрации пользовательских команд.