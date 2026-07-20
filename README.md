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

## Пошаговое использование

**1. Поднять машину(ы)**

```powershell
cd Vagrant\Debian 12
vagrant up
```

Добавь IP и параметры новой машины в массив `machines` в `Vagrantfile`, если разворачиваешь новую.

**2. Скопировать SSH-ключ машины**

```powershell
vagrant ssh-config <имя_машины>
```

Скопируй `IdentityFile` в `Ansible/debian-bootstrap/keys/<имя_машины>_key` (на управляющую Linux-машину, откуда запускается Ansible).

```bash
chmod 700 Ansible/debian-bootstrap/keys/
chmod 600 Ansible/debian-bootstrap/keys/<имя_машины>_key
```

**3. Добавить машину в inventory**

В `Ansible/debian-bootstrap/inventory.ini` и `Ansible/debian-verify/inventory.ini`:

```ini
<имя_машины> ansible_host=<IP> ansible_ssh_private_key_file=keys/<имя_машины>_key
```

**4. Настроить машину**

```bash
cd Ansible/debian-bootstrap
ansible-playbook playbooks/01_base.yml
ansible-playbook playbooks/02_remove_vagrant.yml
ansible-playbook playbooks/03_secure_root.yml
```

**5. Проверить результат**

```bash
cd ../debian-verify
ansible-playbook verify.yml
```

## Перед первым запуском

В `debian-bootstrap` и `debian-verify` нужно вручную создать `vault.pass` и `vars/vault.yml` — они не хранятся в git. Подробности в README каждого проекта.
