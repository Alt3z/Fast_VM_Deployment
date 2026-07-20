# Vagrant

Развёртывание VM Debian 12 

## Стек

Данный стек использовался, так как уже все это было на моем ПК.

- Windows 10, Vagrant 2.4.7, VirtualBox 7.1.12
- Box: `debian/bookworm64`

## Плагины

```powershell
vagrant plugin install vagrant-disksize
vagrant plugin install vagrant-reload
vagrant plugin install vagrant-vbguest
```

## Структура

```
Vagrant/
└── Debian 12/
    └── Vagrantfile
```

Машины описаны массивом в начале `Vagrantfile` — добавление новой сводится к одной строке.

## Сеть

`public_network` (bridge) — VM получают адрес напрямую в физической LAN хоста. Имя bridge-адаптера:

- `Чтобы была bridge сеть`: node.vm.network "public_network", ip: m[:ip], bridge: "eth0"
- `Чтобы была HostOnly`: node.vm.network "private_network", ip: m[:ip]

## Особенности конфига

- `config.ssh.insert_key = false` — все машины используют общий `insecure_private_key` вместо уникального ключа на каждую
- `vbguest.auto_update = false` — отключено автообновление Guest Additions (иначе падает на несуществующих `linux-headers-*`)


## Команды

```powershell
vagrant up               # поднять все машины
vagrant up <имя>          # поднять конкретную
vagrant ssh-config <имя>   # параметры подключения (IP, порт, ключ)
vagrant destroy -f          # удалить все машины
```

## Дальше

После `vagrant up` машины передаются в `Ansible/debian-bootstrap` для настройки.
