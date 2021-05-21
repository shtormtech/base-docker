# Introduction 
Как запустить robot тесты в контейнере docker.

# Getting Started
Докер образ взят почти без изменений (о них ниже) вот отсюда:
https://github.com/ppodgorsek/docker-robot-framework

    Robot Framework 3.2
    Robot Framework DatabaseLibrary 1.2
    Robot Framework Faker 5.0.0
    Robot Framework FTPLibrary 1.9
    Robot Framework IMAPLibrary 2 0.3.6
    Robot Framework Pabot 1.8.0
    Robot Framework Requests 0.7.0
    Robot Framework SeleniumLibrary 4.3.0
    Robot Framework SSHLibrary 3.4.0
    lib-pam 1.2.1-r0 https://pkgs.alpinelinux.org/package/v3.4/main/x86/linux-pam
    krb5 1.18.2-r0 https://pkgs.alpinelinux.org/package/edge/main/x86_64/krb5
    Firefox ESR 78
    Chromium 83.0

Изменения в dockerfile:
1. Добавлена секция установки kerberos утилит (krb5, linux-pam);
2. Добавлена секция преобразования (chmod +x) запускаемых sh скриптов для ручного и автоматического старта;
3. Добавлена проверка имени пользователя от имени которого будут запускаться скрипты тестов (USER root, whoami).

# Build and Test
1. Нужно настроить и сформировать krb5.conf файл, для успешного использования kerberos авторизации:
```
[libdefaults]
        default_realm = MYDOMAIN.RU
...
[realms]
        MYDOMAIN.RU = {                
                kdc = 1.1.1.2 1.1.1.3               
                admin_server = 1.1.1.2
        }
```
2. Создать (если отсутствует) папку /etc/chromium/policies/recommended/ и файл kerberos.json с содержимым:
```
{
    "AuthServerWhitelist": "*.mydomain.ru"
}
```
3. Запуск тестов подразумевает их наличие (тестирование проводилось на тесте test.robot);
4. Билд образа делаем как обычно (docker build .), в папке обязательно должны быть папки bin и tests (tests можно прокинуть со своей тачки, bin - обязателен!);
5. Запуск тестов производим следующим образом через терминал:

```
sudo docker run \
-v /home/axiro/reports:/opt/robotframework/reports:Z \
-v /home/axiro/tests:/opt/robotframework/tests:Z \
-v /etc/krb5.conf:/etc/krb5.conf
-e BROWSER=headlesschrome \
$your_id_of_builded_image_or_name_of_image
```
Где ключи -v означают папку reports, tests - там лежат тесты, в папку reports лягут отчёты;
Файл krb5.conf, который располагается на локальной машине (настроен для kerberos авторизации).
Ключ -e означает выбор браузера (можно выбрать между chromium и firefox).
Последней строкой указывается id образа сбилженного в шаге 4.

Такой запуск позволит запустить тесты автоматически (что не будет работать, так как не будет получен kerberos ticket). Сейчас ведётся работа по устранению этой проблемы.
Руками можно запустить тесты следующим образом:
1. сбилдить образ;
2. запустить образ следующей командой:
```
sudo docker run \
-v /home/axiro/reports:/opt/robotframework/reports:Z \
-v /home/axiro/tests:/opt/robotframework/tests:Z \
-v /etc/krb5.conf:/etc/krb5.conf
-e BROWSER=headlesschrome \
-it $your_id_of_builded_image_or_name_of_image /bin/sh
```
Команда выше позволит позвать shell из самого контейнера.

4. Запустить утилиту kinit username@MYDOMAIN.RU;
5. Ввести пароль от доменной/служебной УЗ;
6. Проверить получение тикета kerberos командой klist
5. Запустить тесты командой из папки bin докер образа:
```
sh run-tests-in-virtual-screen.sh
```
В терминале будут по шагам пройдены все тесты и в папку reports (локально) сложены скрины и файлы отчётов.

# Contribute
Если появятся дополнения/изменения - смело комитьте!