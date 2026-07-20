# Ansible

Настройка и проверка Debian VM, поднятых через Vagrant.

## Проекты

```
Ansible/
├── debian-bootstrap/   ← настройка VM: пакеты, пользователь, безопасность
└── debian-verify/       ← проверка результата (ничего не меняет)
```

Подробности — в README каждого проекта.

## Порядок работы с новой машиной

```bash
cd debian-bootstrap
ansible-playbook playbooks/01_base.yml           # пакеты + user (vagrant)
ansible-playbook playbooks/02_remove_vagrant.yml  # удаление vagrant (user)
ansible-playbook playbooks/03_secure_root.yml     # закрытие root (user)

cd ../debian-verify
ansible-playbook verify.yml                        # проверка результата
```

## Общее для обоих проектов

- Пароли хранятся в `vars/vault.yml`, зашифровано (`ansible-vault`)
- `vault.pass` и `vars/vault.yml` не в git — создаются вручную на новой машине
- Подключение к VM: `vagrant` по ключу на первом шаге, дальше — `user` по паролю
