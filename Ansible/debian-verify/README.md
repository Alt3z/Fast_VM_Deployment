# debian-verify

Проверка состояния Debian VM после `debian-bootstrap` — ничего не меняет, только читает.

## Структура

```
debian-verify/
├── ansible.cfg
├── inventory.ini
├── vault.pass              ← не в git, нужно создать самому
├── vault.pass.exaple       ← содержит информация для создания файла vault.pass
├── vars/
│   └── vault.yml             ← не в git, зашифрованный пароль user
│   └── vault.yml.example     ← шаблон структуры vault.yml
└── verify.yml
```

## Файлы, которых нет в репозитории (`.gitignore`)

Создать вручную перед первым запуском:

- **`vault.pass`** — мастер-пароль для ansible-vault (любая строка)
- **`vars/vault.yml`**:
  ```bash
  ansible-vault create vars/vault.yml
  ```
  Содержимое:
  ```yaml
  user_ssh_password: "qwe"   # обычный пароль, не хеш!
  ```

## inventory.ini

Подключение идёт под `user` (не `vagrant`), паролем — ключи здесь не нужны:

```ini
[debian_vms]
matrix ansible_host=192.168.56.150
test   ansible_host=192.168.56.151

[debian_vms:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=user
```

## Что проверяет

- удалён ли пользователь `vagrant` и его домашняя директория
- существует ли пользователь `user` и состоит ли он в группе `sudo`
- `PermitRootLogin` и `PasswordAuthentication` в `sshd_config`

## Запуск

```bash
ansible debian_vms -m ping
ansible-playbook verify.yml
```

## Зависимости на управляющей машине

```bash
sudo apt install -y sshpass
```
