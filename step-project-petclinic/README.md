Step-проєкт: PetClinic (Vagrant + Ansible + MySQL)

Розгортання Spring PetClinic на двох віртуальних машинах через Vagrant та Ansible.

DB_VM (192.168.56.20) — MySQL, провіжниться через provision/db.yml
APP_VM (192.168.56.10) — застосунок PetClinic, провіжниться через provision/app.yml

Секрети передаються через ENV хоста (extra_vars) і в репозиторії не зберігаються.
Репозиторій із кодом проєкту: https://gitlab.com/DalonnaArt/petclinic-deploy

1. Vagrantfile
Повний Vagrantfile описує обидві VM (db_vm та app_vm), приватну мережу
192.168.56.0/24 та проброс порту 8080 для APP_VM. Код — у файлі Vagrantfile.

2. Git-репозиторій з проектом PetClinic
Робота організована у два merge request за підзавданнями (DB_VM та APP_VM),
історія лінійна.

3. Підключення з APP_VM до бази даних на DB_VM
З APP_VM (192.168.56.10) виконано підключення до MySQL на DB_VM
(192.168.56.20) через mysql-client; видно дані з таблиці owners.

4. Робота застосунку на порту 8080 + додавання своїх даних.
Застосунок доступний за 192.168.56.10:8080. Додано власника Dalonna Art
(Postova 14, Kyiv) та вихованця — кота Robin. Дані збережено в MySQL на DB_VM.

5. Сайт з існуючими й доданими даними
Список власників містить стандартні записи разом із доданим власником
Dalonna Art та її вихованцем — підтверджує запис у базу та читання зі
спільної таблиці. 

Технічні деталі реалізації

- Provisioning повністю через Ansible (provision/db.yml, provision/app.yml).
- DB_VM: MySQL слухає приватний інтерфейс (bind-address), користувач створюється
з доступом лише з приватної підмережі 192.168.56.% (принцип найменших привілеїв).
- APP_VM: clone PetClinic через SSH agent forwarding, білд через Maven Wrapper.
- Запуск застосунку через systemd-сервіс (команда java -jar від користувача
appuser, з автозапуском enabled та автовідновленням Restart=always).
- Секрети БД — через окремий EnvironmentFile (/etc/petclinic/db.env, права 0640),
не в тілі systemd-юніта і не в репозиторії.
- Перевірено vagrant destroy app_vm + vagrant up app_vm з нуля — провіжн
проходить повністю автоматично (failed=0).
