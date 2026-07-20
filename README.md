# Fast VM Deployment

Быстрое развёртывание и настройка Debian 12 VM для homelab: Vagrant поднимает машины, Ansible их настраивает.

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
