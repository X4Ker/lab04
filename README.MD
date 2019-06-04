## Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```ShellSession
$ open https://travis-ci.org
```

## Tasks

- [x] 1. Авторизоваться на сервисе **Travis CI** с использованием **GitHub** аккаунта
- [x] 2. Создать публичный репозиторий с названием **lab04** на сервисе **GitHub**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Включить интеграцию сервиса **Travis CI** с созданным репозиторием
- [x] 5. Получить токен для **Travis CLI** с правами **repo** и **user**
- [x] 6. Получить фрагмент вставки значка сервиса **Travis CI** в формате **Markdown**
- [x] 7. Выполнить инструкцию учебного материала
- [x] 8. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>  //присваиваем переменной значение
$ export GITHUB_TOKEN=<полученный_токен> //внвлогично
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace //переходим в папку
$ pushd . //сохраняем директорию 
$ source scripts/activate //выполняем команжы из файла
```

```ShellSession
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles  // обновляем или устанавливаем rvm
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate //записываем текст в файл
$ . scripts/activate  
$ rvm autolibs disable // отключает autolib
$ rvm install ruby-2.4.2 //устанавливаем данную версию ruby
$ rvm use 2.4.2 --default // используем данную версию по умолчанию
$ gem install travis  // устанавливаем travis
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04 //клонируем репозиторий
$ cd projects/lab04  // переходим в директорию 
$ git remote remove origin //удаляем с origin прошлый репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04  // и устанавливаем новый
```

```ShellSession
$ cat > .travis.yml <<EOF  //записываем в файл, настраиваем travis
language: cpp
EOF
```

```ShellSession
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

```ShellSession
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```

```ShellSession
$ travis login --github-token ${GITHUB_TOKEN}  //вход в аккаунт travis
```

```ShellSession
$ travis lint // предупреждения для tavis.yml
```

```ShellSession
$ ex -sc '1i|<фрагмент_вставки_значка>' -cx README.md  //вставляем значок прохождения успешного теста
```

```ShellSession
$ git add .travis.yml  //добавлям файлы и отправляем их на удаленный репозиторий этой и слеующими комаедами
$ git add README.md
$ git commit -m"added CI"
$ git push origin master
```

```ShellSession
$ travis lint //предкпреждения
$ travis accounts //аккаунты
$ travis sync //новая синхронизация
$ travis repos //репозитории
$ travis enable //вкл проект
$ travis whatsup //последний тест
$ travis branches //ветки
$ travis history //история сборок
$ travis show ,,результаты
```

## Report

```ShellSession
$ popd  //возвращаемся в сохраненную папку 
$ export LAB_NUMBER=04 //присваивание переменной  значения 04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER} //клонирование репозитория в 2 папки
$ mkdir reports/lab${LAB_NUMBER} //создание директории
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md //копирование файла во 2 директорию
$ cd reports/lab${LAB_NUMBER} //переходим в директорию
$ edit REPORT.md //редактируем отчёт 
$ gistup -m "lab${LAB_NUMBER}" //отправляем на gist
```

## Homework

Вы продолжаете проходить стажировку в "Formatter Inc." (см [подробности](https://github.com/tp-labs/lab03#Homework)).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы **CMake**.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в [прошлый раз](https://github.com/tp-labs/lab03#Homework). Настройте сборочные процедуры на различных платформах:
* используйте [TravisCI](https://travis-ci.com/) для сборки на операционной системе **Linux** с использованием компиляторов **gcc** и **clang**;
* используйте [AppVeyor](https://www.appveyor.com/) для сборки на операционной системе **Windows**.

.travis.yml:
```yaml
# Используемый язык
language: cpp
# Используеме компиляторы
compiler:
  - gcc
  - clang

# Операционная система на которой будет происходить компиляция
os:
 - linux

# Секция где мы загружаем и устанавливаем соответствующий софт
addons:
  apt:
    sources:
      - ubuntu-toolchain-r-test
      - llvm-toolchain-precise-3.6
    packages:
      - g++-7
      - clang-3.6

# Сам скрипт
script:
# Выбор и создание системы сборки. В данном случае файлы сборки разместятся
# в папке с CMakeLists`ом, но можно и указать другое место
- cmake CMakeLists.txt
# Собрать "все"
- cmake --build .
```
### Appveyor
Содержимое appveyor.yml:
```yaml
# Сборка будет происходить на VS 2015
image: Visual Studio 2015

# Секция инициализации. Возможно строка в ней необязательна, но она есть в
# официальной документации
init:
  - git config --global core.autocrlf input

# Папка, куда будет клонироваться проект
clone_folder: c:\projects\my-prj

# Сборка будет происходить под x64
platform:
  - x64

# Собираться будет debug версия
configuration:
  - Debug

# Задание условий среды
environment:
  matrix:
    - TOOLCHAIN: msvc15

# Cам скрипт
build_script:
- cmake CMakeLists.txt
- cmake --build .
```

## Links

- [Travis Client](https://github.com/travis-ci/travis.rb)
- [AppVeyour](https://www.appveyor.com/)
- [GitLab CI](https://about.gitlab.com/gitlab-ci/)

```
Copyright (c) 2015-2019 The ISC Authors
```
