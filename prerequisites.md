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
