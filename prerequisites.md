Поехали?
========

Для более гладкой работы с Ruby on Rails, базами данных, а также просто закрепления материала по Linux'у мы воспользуемся виртуальной машиной, но будем ставить её не вручную, а упростим и убыстрим задачу — дадим всё сделать [Vagrant](https://www.vagrantup.com/docs/why-vagrant)'у.

И в итоге работа распределится следующим образом: на вашей основной машине вы редактируете код и смотрите, например, в браузере результат, а на виртуальной машине код будет исполняться.

Развёртывание виртуального окружения
====================================

1. Необходимо будет скачать и установить следующие программы или проверить, что установлены их последние версии:

  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  * [Vagrant](https://www.vagrantup.com/downloads.html)

2. По завершению установки нужно установить плагин:

  ```bash
  vagrant plugin install vagrant-vbguest
  ```

  > Для владельцев MacOS с Parallels: у вас будет плагин
  > ```bash
  > vagrant plugin install vagrant-parallels
  > ```

3. Нужно будет создать файл `Vagrantfile` в корне своего проекта. Поскольку проекта у вас сейчас ещё нет, создайте директорию на локальном компьютере и, например, инициализируйте в ней git-репозиторий (`git init`). В этой директории и должен лежать файл Vagrant'а. Создастся же он автоматически по следующей команде (если мы хотим 64-битную Ubuntu 16.04):

  ```bash
  vagrant init ubuntu/xenial64
  ```
  
  > Опять же, для Parallels на MacOS:
  > ```bash
  > vagrant init parallels/ubuntu-16.04
  > ```

4. Файл создан. Теперь можно установить и базово настроить ОС. Вот здесь-то и пригождается Vagrant — всё это делается одной лишь командой из любой консоли (но лучше не `cmd` для дальнейшей работы с ssh):

  ```bash
  vagrant up
  ```
  
  Образ нужной ОС сам скачается, разархивируется, установится и настроится. Конечно, всё будет на самом базовом уровне, но нам пока больше и не нужно.
  
  > А вообще, в `Vagrantfile` можно очень гибко задавать кучу настроек: например, автоматически установить нужные программы или объединить несколько виртуальных машин в одну сеть.
  
  Примерный вид того, что будет в консоли по мере отрабатывания `vagrant up`-а (у вас будут свои названия виртуальной машины, адреса):
  
  ```
  Bringing machine 'default' up with 'parallels' provider...
  ==> default: Registering VM image from the base box 'parallels/ubuntu-16.04'...
  ==> default: Cloning new virtual machine...
  ==> default: Unregistering the box VM image...
  ==> default: Setting the default configuration for VM...
  ==> default: Checking if box 'parallels/ubuntu-16.04' is up to date...
  ==> default: Setting the name of the VM: itmotest_default_1476030306132_19873
  ==> default: Preparing network interfaces based on configuration...
      default: Adapter 0: shared
  ==> default: Clearing any previously set network interfaces...
  ==> default: Booting VM...
  ==> default: Waiting for machine to boot. This may take a few minutes...
      default: SSH address: 10.211.55.5:22
      default: SSH username: vagrant
      default: SSH auth method: private key
      default: Warning: Connection refused. Retrying...
      default:
      default: Vagrant insecure key detected. Vagrant will automatically replace
      default: this with a newly generated keypair for better security.
      default:
      default: Inserting generated public key within guest...
      default: Removing insecure key from the guest if it's present...
      default: Key inserted! Disconnecting and reconnecting using new SSH key...
  ==> default: Machine booted and ready!
  ==> default: Checking for Parallels Tools installed on the VM...
  ==> default: Parallels Tools installed on this VM are outdated! In most cases
  ==> default: this is fine but in rare cases it can cause things such as shared
  ==> default: folders to not work properly. If you see shared folder errors,
  ==> default: please update Parallels Tools within the virtual machine and
  ==> default: reload your VM.
  ==> default: Mounting shared folders...
      default: /vagrant => /Users/denpatin/Repos/itmotest
  ```
  
  Обратите внимание на строки:
  * `SSH address` — по этому IP вы можете с локальной машины адресоваться к гостевой
  * `/vagrant => /Users/denpatin/Repos/itmotest` — у вас, конечно, будет другая директория на компьютере, но эта строка говорит о том, что в виртуальной машине файлы проекта с локальной машины вы можете найти в директории `vagrant`

Подключение к гостевой ОС
=========================

Самый простой способ получить доступ к консоли гостевой машины — это ssh. Vagrant предоставляет эту возможность в самом простом для нас виде (нужно ввести эту команду из директории, где расположен `Vagrantfile`):

```bash
vagrant ssh
```

Не нужно вводить никаких паролей — Vagrant позаботился об этом за нас.

> Единственный совет: очень не рекомендую использовать `cmd` для работы с Vagrant'ом. Как минимум просто потому, что вы не сможете корректно использовать ssh. Поставьте себе `cygwin`, например. Либо, если у вас установлен на Windows `git`, войдите через bash, поставляемый вместе с ним.

Установка необходимого ПО
=========================

1. Рекомендую выполнить базовую команду на чистой ОС:

  ```bash
  sudo apt-get update
  ```
  
  и установить утилиту `curl` (хотя она может оказаться уже установленной):
  
  ```bash
  sudo apt-get install curl
  ```

2. Далее установим RVM — менеджер версий Ruby:

  ```bash
  gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
  
  \curl -sSL https://get.rvm.io | bash -s stable
  ```
  
  Если установка прошла успешно, обратите внимание на сообщение, которое RVM напишет. Вам необходимо будет скопировать оттуда и выполнить команду, которая будет похожа на эту:

  ```bash
  source /home/vagrant/.rvm/scripts/rvm
  ```
  
  Проверить, всё ли корректно установилось, можно с помощью команды, например:
  
  ```bash
  rvm -v
  ```

3. Теперь установим сам Ruby. Последняя его стабильная версия на текущий момент — 2.3.1:

  ```bash
  rvm install 2.3.1
  ```
  
  Менеджер версий Ruby сообщит о том, нашёл он на сервере готовый бинарник под текущую ОС (тогда он просто его скачает и поставит, это очень быстро) или нет (тогда он скачает исходники и скомпилирует интерпретатор Ruby самостоятельно, в зависимости от выделенных ресурсов и железа эта процедура может занять много времени).
  
  Когда Ruby успешно будет установлен, надо будет сделать его версией по умолчанию:
  
  ```bash
  rvm use --default 2.3.1
  ```
  
  Проверить, что Ruby 2.3.1 — основной Ruby в системе, можно командой:
  
  ```bash
  ruby -v
  ```

4. Установим нужные для работы гемы:

  ```bash
  gem install bundler --no-ri --no-rdoc
  gem install json --no-ri --no-rdoc
  gem install rails
  ```

5. Для корректной работы с JavaScript нам понадобится NodeJS:

  ```bash
  sudo apt-get install -y nodejs
  ```

Проверка правильности установки ака первое мини-приложение
==========================================================

1. Перейдём в директорию нашего проекта (как уже выше мы выяснили, она залинковалась на директорию `/vagrant` на виртуальной машине):

  ```bash
  cd /vagrant
  ```

2. Создадим новое Ruby on Rails приложение:

  ```bash
  rails new .
  ```
  
  > Обычный синтаксис — `rails new application_name`, но т.к. мы собираемся создать приложение в той же директории, где и находимся сейчас, пишем вместо названия проекта `.`, что означает «текущая директория».
  
  В конце вы должны получить сообщение об успешном создании каркаса веб-приложения и установки всех нужных гемов:
  
  ```
  Bundle complete! 15 Gemfile dependencies, 62 gems now installed.
  Use `bundle show [gemname]` to see where a bundled gem is installed.
  ```

3. В нашем приложении пока ничего нет, но давайте его запустим и проверим, всё ли в рабочем состоянии?

  ```bash
  rails server --binding=0.0.0.0
  ```
  
  > Вообще, обычная команда для запуска сервера — `rails server` или просто `rails s`. Опция `--binding=0.0.0.0` нужна нам для того, чтобы позволить серверу «видеть» все IP-адреса, поскольку по умолчанию он «видит» только `localhost`. От этого назойливого параметра можно избавиться — мы это сделаем позже.
  
  Будет примерно такой текст в консоли:
  
  ```
  => Booting Puma
  => Rails 5.0.0.1 application starting in development on http://0.0.0.0:3000
  => Run `rails server -h` for more startup options
  Puma starting in single mode...
  * Version 3.6.0 (ruby 2.3.1-p112), codename: Sleepy Sunday Serenity
  * Min threads: 5, max threads: 5
  * Environment: development
  * Listening on tcp://0.0.0.0:3000
  Use Ctrl-C to stop
  ```
  
  Это значит, что все модули Ruby успешно подключились и сервер запустился!

4. Теперь давайте откроем этот веб-сайт. Помните, я просил обратить внимание на `SSH address`? Собственно, этот тот самый IP, по которому мы с локальной машины должны обратиться к серверу.

  > Если он потерялся или забылся, можно его найти. На виртуальной машине остановите запущенный Puma-сервер (`Ctrl+C`) и выполните команду:
  > ```bash
  > ifconfig | grep "inet addr"
  > ```
  > IP, который начинается с `10.`, нам и нужен (т.е. не тот, который `127.0.0.1`). Например, у меня это `10.211.55.5`.
  > Для дальнейших действий не забудьте запустить сервер — `rails s --binding=0.0.0.0`

5. На вашей основной машине откройте браузер и введите туда полученный с виртуальной машины IP и порт `3000` (в моём случае это так):

  ```
  http://10.211.55.5:3000/
  ```
  
  И voilà — `Yay! You’re on Rails!`!
  
  Поздравляю вас с первый Ruby on Rails-приложением! :smile:

Что дальше?
===========

Мы будем постепенно развивать наше приложение, одновременно знакомясь с разными аспектами мира веб-разработки: глубже залезем в Ruby, рассмотрим паттерн MVC, будем использовать базы данных, затронем различные технологии front-end'а — и в конце концов каждый сможет делать для себя полезные и интересные сайты и сервисы!
