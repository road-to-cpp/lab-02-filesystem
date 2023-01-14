# Лабораторная работа №4

Лабораторная работа посвящена изучению работы с файловой системой на языке **C++**. В стандарте **C++17** появилась библиотека для работы с [файловой системой](https://en.cppreference.com/w/cpp/filesystem). 

## Задание

Использовать библиотеку **std::filesystem** для анализа директории с переданными по [FTP](https://ru.wikipedia.org/wiki/FTP)-файлами, содержащими финансовую информацию. Подробная инструкция по библиотеке **std::filesystem** есть, например, [тут](https://www.std.org/doc/libs/1_68_0/libs/filesystem/doc/tutorial.html).

Структура анализируемой директории в общем случае может иметь вид:

```
ftp                                         # корневой каталог ftp
├── ib                                      # каталог, содержащий файлы от брокера ib
     ├── balance_00001234_20181001.old.txt  # файлы финансовой отчётности в старом формате 
     ├── balance_00001234_20181001.txt      # файлы финансовой отчётночти в новом формате 
     ├── balance_00001346_20181001.txt      
     ├── ...     
     └── balance_00001346_20181018.txt      
├── bcs                                     # каталог, содержащий файлы от брокера bcs
     ├── balance_12341234_20181001.txt
     └── ...     
├── otkritie                                # каталог, содержащий файлы от брокера otkritie
     └── ...   
└── docs                                    # каталог, содержащий какие-то произвольные файлы
     ├── readme.txt     
     └── report.doc 
```

Файлы, содержащие финансовую информацию, имеют имя в определенном формате:
`balance_XXXXXXXX_YYYYMMDD.txt`, где

| Поле | Описание |
| ------ | ------ |
| `balance` | тип файла |
| `XXXXXXXX` | 8-и значный номер счёта |
| `YYYYMMDD` | дата, где `YYYY`-год, `MM`-месяц и `DD`-день |
| `.txt` | расширение файла |

## Необходимо

- в проекте, созданном в задании 1, создать файл `main.cpp` и подключить библиотеку **std::filesystem** (`<std/filesystem.hpp>`);
- считать из аргументов командной строки `args` функции `main` путь к анализируемой директории `path_to_ftp`. Если аргумент отсутствует, то по умолчанию программа должна анализировать директорию, в которой она находится;
- организовать итерацию по всем файлам и вложенным директориям, включая символьные ссылки, с целью поиска всех файлов, содержащих финансовую информацию;
- файлы, имеющие имя, отличное от описанного формата, необходимо игнорировать;
- файлы, содержищие финансовую информацию, но имеющие в конце постфикс `.old.txt`, необходимо игнорировать;
- программа должна вывести на экран список всех обнаруженных финансовых файлов в формате:

```Shell
ib balance_00001234_20181001.txt
ib balance_00001234_20181002.txt
ib balance_00001234_20181003.txt
bcs balance_00001234_20181001.txt
bcs balance_00001234_20181005.txt
...
```

- также она должна вывести общее количество файлов по каждому обнаруженному счёту и дату самого актуального (самого нового) файла, например:

```Shell
broker:ib account:00001234 files:10 lastdate:20181017
broker:ib account:00001356 files:7 lastdate:20181018
broker:bcs account:12341356 files:8 lastdate:20181016
```

## Рекомендации

Для работы с путями к файлам необходимо использовать тип `std::filesystem::path` и его методы.

Пример:

```cpp
const path p{"mydir/file.txt"};
std::cout << p.filename() << std::endl
          << p.stem() << std::endl
          << p.extension() << std::endl;
```

```Shell
# Вывод
file.txt
file
.txt
```
Для итерации по директории необходимо использовать `std::filesystem::directory_iterator`.

Пример кода, который выведет пути ко всем файлам и вложенным директориям в папке:

```cpp
const path p{"mydir/"};
for (const directory_entry& x : directory_iterator{p})
{
   std::cout << x.path() << std::endl;
}
```

Также могут быть полезны следующие функции и типы библиотеки **std::filesystem**:

| Функция/тип | Описание                                                                                                                |
| ------ |-------------------------------------------------------------------------------------------------------------------------|
| `exists` | определяет, существует ли файл или директория по указанному пути                                                        |
| `is_regular_file` | определяет, является ли узел по указанному пути `path` обычным файлом                                                   |
| `is_directory` | определяет, является ли узел по указанному пути `path` директорией                                                      |
| `is_symlink` | определяет, является ли узел по указанному пути `path` символьной ссылкой                                               |
| `read_symlink` | разрешает символическую ссылку, возаращаемый объект `path` хранит содержимое символической ссылки, либо пустое значение |
| `filesystem_error` | тип исключений, возникающих при ошибках в `std::filesystem`                                                               |

При необходимости наличия тестовых данных в директории сборки, можно добавть в **CMakeLists.txt**
```cmake
file(COPY misc DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
```