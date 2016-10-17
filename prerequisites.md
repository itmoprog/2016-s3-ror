Поехали?
========

Для более гладкой работы с Ruby on Rails, базами данных, а также просто закрепления материала по Linux'у мы воспользуемся виртуальной машиной, но будем ставить её не вручную, а упростим и убыстрим задачу — дадим всё сделать [Vagrant](https://www.vagrantup.com/docs/why-vagrant)'у.

И в итоге работа распределится следующим образом: на вашей основной машине вы редактируете код и смотрите, например, в браузере результат, а на виртуальной машине код будет исполняться.

Установка необходимого ПО
=========================

1. Необходимо будет скачать и установить следующие программы или проверить, что установлены их последние версии:

  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) — система виртуализации для операционных систем
  * [Vagrant](https://www.vagrantup.com/downloads.html) — система конфигурирования виртуальных машин
  * [Git](https://git-scm.com/downloads) — система контроля версий

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

  Тем не менее, для наших занятий я заранее заготовил настройки будущей виртуальной машины, поэтому вы можете заменить содержимое создавшегося файла на следующее:
  ```ruby
Vagrant.configure('2') do |config|
  config.vbguest.auto_update = false
  config.vm.box_check_update = false
  config.vm.box = 'ubuntu/xenial64'
  config.vm.network 'forwarded_port', guest: 3000, host: 3000
  config.vm.hostname = 'itmo'
  config.vm.provision 'shell', path: 'setup.sh'
  config.vm.provider 'virtualbox' do |vm|
    vm.name = 'itmoprog'
    vm.memory = 512
    vm.cpus = 1
  end
  config.vm.post_up_message = <<-WELCOME
All prerequisites and preparations are fulfiled! Congratulations!
= Welcome to the ITMO Advanced Maths Department Testing Ground! =
Local directory: /vagrant
Accessible over: http://localhost:3000
  WELCOME
end
  ```

4. Помимо `Vagrantfile` в той же директории должен находиться и файл `setup.sh`, отвечающий за установку дополнительного ПО. Создайте его:

  ```bash
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get -y install nodejs curl
command curl -sSL https://rvm.io/mpapis.asc | gpg --import -
\curl -sSL https://get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
bash --login
rvm install 2.3.1
rvm use --default 2.3.1
gem install bundler --no-ri --no-rdoc
gem install rails
  ```

5. Файлы созданы. Теперь можно установить и базово настроить ОС, а также установить и настроить ПО. Вот здесь-то и пригождается Vagrant — всё это делается одной лишь командой из любой консоли (но лучше не `cmd` для дальнейшей работы с ssh):

  ```bash
  vagrant up
  ```

Подключение к гостевой ОС
=========================

Самый простой способ получить доступ к консоли гостевой машины — это ssh. Vagrant предоставляет эту возможность в самом простом для нас виде (нужно ввести эту команду из директории, где расположен `Vagrantfile`):

```bash
vagrant ssh
```

Не нужно вводить никаких паролей — Vagrant позаботился об этом за нас.

> Единственный совет: очень не рекомендую использовать `cmd` для работы с Vagrant'ом. Как минимум просто потому, что вы не сможете корректно использовать ssh. В папке (на Windows), куда установился Git, — например, `C:\Program Files\Git\` — найдите бинарник `git-bash.exe` и через него работайте с `vagrant ssh`.
> Нужно будет перейти в директорию, где находится на локальном компьютере `Vagrantfile`, но простые команды типа `cd C:\` тут не работают. Вместо этого у вас будет смонтированная подсистема, которая проецируется на привычную вам следующим образом: какое-нибудь `C:\Users\denpatin\testitmo` станет `/c/Users/denpatin/testitmo` — и именно в такой вариант надо будет делать команду `cd`.

Создание и запуск приложения
============================

1. Перейдите в директорию `/vagrant`. Напомню, эта директория синхронизируется с той, где у вас на основной машине находится `Vagrantfile`.

2. Создайте новое Rails-приложение:

  ```bash
  rails new .
  ```
  
  > В данном случае `.` означает то, что мы в директории, в которой находимся, хотим создать приложение. Если вместо `.` написать какое-нибудь название, будет создана новая директория, и в ней — приложение.

3. Запустите приложение и проверьте:

  ```bash
  rails s --binding=0.0.0.0
  ```
  
  > Вообще, обычная команда для запуска сервера — rails server или просто rails s. Опция --binding=0.0.0.0 нужна нам для того, чтобы позволить серверу «видеть» все IP-адреса, поскольку по умолчанию он «видит» только localhost. От этого назойливого параметра можно избавиться — мы это сделаем позже.
  
  В консоли появится нечто подобное:
  
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
  
  Проверить работоспособность можно в браузере на локальной машине по адресу: `http://localhost:3000`
