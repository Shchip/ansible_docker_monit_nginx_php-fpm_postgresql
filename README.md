# ansible_docker_monit_nginx_php-fpm_postgresql

**Тестировано на ubuntu 16.04 LTS**
###Что делает данный playbook:


  Так как в Ubuntu 16.04 предустановлен толь python3.5, а для выполнения некоторых модулей Ansible нужен python2.7, то первым делом – устанавливается пакет python-minimal.
  
  Далее выполняются роли:

######1. docker_install
Устанавливается docker-engenre и все необходимые пакеты

######2. monit_install
Устанавливается система мониторинга monit, копируются конфигурационные файлы, которые лежат в папке проекта files/monit
Мониторинг можно посмотреть по адресу:

`http://target_host:2812`
target_host – адрес удаленного хоста на котором выполняется проект (login: "admin"; pass: "monit")

######3. pgsql_container
Создается Docker container pgsql_v1, в нем отключается ipv6, устанавливается  Postgresql 9.3, создается база данных test и пользователь test с паролем test. При запуске контейнер пробрасывает порт 5432 на хост машину.

######4. nginx_php-fpm_container
Создается Docker container nginx_v1, в нем отключается ipv6, устанавливается  nginx, php-fpm php-pgsql php-cgi phppgadmin. Копируются  конфиг phppgadmin, тестовый сайт и его настройки . При запуске контейнер пробрасывает порт 80 на хост машину и создает линк с pgsql_v1 контейнером.
Доступность Web - сервера можно по адресу:

`htpp://target_host` - test page php

`htpp://target_host/index.html` - test page html

`htpp://target_host/phpphadmin/` phppgadmin подключен к pgsql_v1 (login and pass: "test")

Если нужно вручную запустить или перезапустить собранные контейнеры, то первым запускается pgsql_v1, вторым nginx_v1 - так как при запуске он линкует pgsql_v1 и является зависимым от него. 

###Запуск playbook.yml из директории проекта:

Перед тем, как запустить Playbook, `~/.ssh/known_hosts` файл должен иметь запись для каждого из хостов , указанных в hosts. Для это подключаемся по ssh к ко всем удаленным хосам


Если пользователь под которым выполняется задача есть на удаленном хосте (пароль для ssh и sudo нужно будет ввести в консоли) то hosts будет выглядить так:
```
[docker]
192.168.0.230
......
```
Выполнение Plabook
`$ansible-playbook -i hosts playbook.yml -kK`

Явно указываем под кем будем подключаться к удаленному хосту:

hosts
```
[docker]
192.168.0.230 ansible_connection=ssh ansible_ssh_user=USER_NAME
......
```
Выполнение Plabook
`$ansible-playbook -i hosts playbook.yml -kK`

Указываем логин и пароль для ssh и sudo в hosts
```
[docker]
192.168.0.230 ansible_connection=ssh ansible_ssh_user=USER_NAME ansible_ssh_pass=PASSWORD  ansible_sudo_pass=PASSWORD
......
```
Выполнение Plabook
`$ansible-playbook -i hosts playbook.yml``
