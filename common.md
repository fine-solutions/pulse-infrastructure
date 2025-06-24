# Типовая настройка

- Добавить пользователя с правами администратора (на примере `user`):

  ```bash
  # RedHat based
  useradd user             # Создание пользователя
  grep user /etc/passwd    # Проверка наличия пользователя
  passwd user              # Создание пароля для пользователя
  su - user                # Проверка авторизации
  ww                       # Проверка правильности авторизации
  pwd                      # Проверка корректности домашней директории
  exit                     # Выход и переход к пользователю root
  grep wheel /etc/sudoers  # Проверка настроек для админов
  usermod -aG wheel user   # Добавление пользователя в группу админов
  grep wheel /etc/group    # Проверка состава группы админов

  # Debian based
  useradd -m -s /bin/bash user # Создание пользователя
  passwd user                  # Создание пароля для пользователя
  usermod -a -G sudo user      # Добавление пользователя в группу админов
  ```

- Убедиться, что настройки SSHD соответствуют следующему шагу:

  ```bash
  sudo vi /etc/ssh/sshd_config  # Редактирование конфигурации sshd

  # Установить следующие значения:
  # PasswordAuthentication yes
  # KbdInteractiveAuthentication yes
  # UsePAM yes
  ```

- Добавить публичный ключ пользователя на сервер (на примере `user`):

  ```bash
  ssh-keygen -t ed25519               # Создание ключа на новых системах
  ssh-keygen -t rsa -b 4096           # Создание ключа на старых системах
  ssh-copy-id user@server_ip_address  # Копирование ключа на сервер
  ```

- Настроить доступ через SSH только по ключу (на примере `user`):

  ```bash
  sudo vi /etc/ssh/sshd_config  # Редактирование конфигурации sshd

  # Установить следующие значения:
  # PermitRootLogin no
  # PasswordAuthentication no
  # UsePAM no
  # ClientAliveInterval 3600
  # ClientAliveCountMax 10
  
  sudo systemctl restart sshd  # Перезагрузка службы

  ssh user@server_ip_address   # В отдельном окне терминала зайти под пользователем user для проверки
  ssh root@server_ip_address   # Выйти командой exit и проверить отсутствие доступа для root

  # Если что-то пошло не так, можно проверить файлы и права для них на сервере
  cat /home/user/.ssh/authorized_keys
  chmod 700 /home/user/.ssh
  chmod 600 /home/user/.ssh/authorized_keys
  restorecon -r -vv /home/user/.ssh/authorized_keys
  ```

- Добавить пользователя с правами на развёртывание приложений и сгенерировать пару ключей для авторизации:

  ```bash
  # RedHat based
  useradd deploy                  # Создание пользователя

  # Debian based
  useradd -m -s /bin/bash deploy  # Создание пользователя

  # Common comands
  grep deploy /etc/passwd                      # Проверка наличия пользователя
  su deploy                                    # Зайти под пользователем
  ssh-keygen -t rsa -b 4096                    # Сгенерировать пару ключей
  exit                                         # Выйти
  sudo su                                      # Зайти под администратором
  cd /home/deploy/.ssh                         # Перейти в домашний каталог
  cp id_rsa.pub authorized_keys                # Добавить в ключи авторизации
  chown deploy:deploy authorized_keys          # Выставить владельца
  chmod 600 /home/deploy/.ssh/authorized_keys  # Выставить права
  exit                                         # Выйти
  ```

- Обновить пакеты и установить самое необходимое:

  ```bash
  # RedHat based
  sudo dnf -y update
  sudo dnf install -y dnf-utils zip unzip curl wget git rsync mc emacs tmux nano vim wget

  # Debian based
  sudo apt update
  sudo apt install -y zip unzip curl wget git rsync mc emacs tmux nano vim wget
  ```
