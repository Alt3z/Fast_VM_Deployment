# debian-bootstrap

Настройка Debian VM после Vagrant: пакеты, пользователь, безопасность.

## Структура

```
debian-bootstrap/
├── ansible.cfg
├── inventory.ini
├── vault.pass                    ← не в git, нужно создать самому
├── vault.pass.exaple             ← содержит информация для создания файла vault.pass
├── vars/
│   ├── vault.yml                   ← не в git, зашифрованные пароли
│   └── vault.yml.example           ← шаблон структуры vault.yml
└── playbooks/
    ├── 01_base.yml                 ← пакеты + создание user (sudo, home)
    ├── 02_remove_vagrant.yml       ← удаление vagrant и его ключей
    └── 03_secure_root.yml           ← смена root-пароля, запрет root по SSH
```

## Файлы, которых нет в репозитории (`.gitignore`)

Создать вручную перед первым запуском:

- **`vault.pass`** — мастер-пароль для ansible-vault (любая строка)
- **`vars/vault.yml`** — по шаблону `vars/vault.yml.example`:
  ```bash
  ansible-vault create vars/vault.yml
  ```
  Содержимое:
  ```yaml
  new_user_password_hash: "$6$..."   # mkpasswd --method=sha-512
  root_password_hash: "$6$..."
  user_ssh_password: "qwe"            # обычный пароль, не хеш!
  ```
- **`keys/<имя_vm>_key`** — приватный SSH-ключ ВМ (берётся из `vagrant ssh-config`)

## Права на keys/

SSH отказывается использовать ключи, если права слишком открытые:

```bash
chmod 700 keys/
chmod 600 keys/*_key
```

## Порядок запуска

```bash
ansible-playbook playbooks/01_base.yml
ansible-playbook playbooks/02_remove_vagrant.yml
ansible-playbook playbooks/03_secure_root.yml
```

## Зависимости на управляющей машине

```bash
sudo apt install -y sshpass whois
```
