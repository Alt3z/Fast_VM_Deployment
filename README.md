# Fast VM Deployment

Быстрое развёртывание и настройка VM Debian 12: Vagrant поднимает машины, Ansible их настраивает.

В данном проекте настройка виртуалок НЕ БЕЗОПАСНА. Безопасная настройка будет в проекте Linux_Hardening.

## Структура

```
Fast_VM_Deployment/
├── Vagrant/
│   └── Debian 12/
│       └── Vagrantfile      ← создание VM
│
└── Ansible/
    ├── debian-bootstrap/     ← настройка VM (пакеты, пользователь, безопасность)
    └── debian-verify/          ← проверка результата
```

Подробности — в README каждой папки.

## Скачать и зайти в проект

```bash
git clone https://github.com/Alt3z/Fast_VM_Deployment
cd Fast_VM_Deployment/
```

## Пошаговое использование

**1. Поднять машину(ы)**

```powershell
cd "Vagrant\Debian 12"
vagrant up
```

Добавьте IP и параметры новой машины в массив `machines` в `Vagrantfile`.

**2. Скопировать SSH-ключ машины**

```powershell
vagrant ssh-config <имя_машины>
```

Скопируйте `IdentityFile` в `Ansible/keys/<имя_машины>_key` (на управляющую Linux-машину, откуда запускается Ansible).

```bash
chmod 700 Ansible/keys/
chmod 600 Ansible/keys/<имя_машины>_key
```

**3. Добавить машину в inventory**

В `Ansible/debian-bootstrap/inventory.ini` и `Ansible/debian-verify/inventory.ini`:

```ini
<имя_машины> ansible_host=<IP> ansible_ssh_private_key_file=/home/user/Fast_VM_Deployment/Ansible/keys/<имя_машины>_key
```

**4. Создать недостающие файлы**

Если необходимо задать базовый редактор:
export EDITOR=nano

Захэшировать пароли
sudo apt install -y whois
mkpasswd --method=sha-512 <ПАРОЛЬ>

Задать секреты для хранилища паролей
nano Ansible/debian-bootstrap/vault.pass
nano Ansible/debian-verify/vault.pass

Создать хранилище
Файл создается через ansible-vault create vars/vault.yml
Редактирование через ansible-vault edit vars/vault.yml
Просмотреть файл через ansible-vault view vars/vault.yml

В хранилище для debian-bootstrap вставить данные типа:
new_user_password_hash: "<ХЭШ> (пример $6$GlrgIzngwoMvgvGL$JGB4ZDMos8Dx59qupTvlDHoE98CFHpc29lET1w7FoqpULBlTpeZPRYy46UOsr98SCGY13KXmf0mCQQhP4ZKRu0)"
root_password_hash: "<ХЭШ>"
user_ssh_password: "<ПАРОЛЬ> (просто пароль в открытом виде)"

В хранилище для debian-verify вставить данные типа:
user_ssh_password: "<ПАРОЛЬ> (просто пароль в открытом виде)"

**5. Настроить машину**

```bash
cd Ansible/debian-bootstrap
ansible-playbook playbooks/01_base.yml
ansible-playbook playbooks/02_remove_vagrant.yml
ansible-playbook playbooks/03_secure_root.yml
```

**6. Проверить результат**

```bash
cd ../debian-verify
ansible-playbook verify.yml
```
