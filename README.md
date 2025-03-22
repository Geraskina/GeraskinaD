
![image](https://github.com/user-attachments/assets/b3fcaa30-fb71-49fc-8547-bf49ca82b064)


Отчет по виртуалке Перед началой установки, нужно установить Linux Oracle на VirtualBox, для этого нужно: Иметь образ Linux Выделить 2+ ядер. Выделать 4096+ МБ оперативы, и обязательно при установки мы выбираем английский язык. Пароль: 1234567 Имя: gerasikina

Мы переходим к установке docker с использованием grafana.
Вводим следующий код: Устанавливаем утилиту Wget
`sudo yum install wget`

По началу выдовалась ошибка того, что имени gerasikina не было в sudoers, внесла свое имя в эту папку чтобы у меня были права sudoс помощью команды
`sudo visudo`
Данная команда открывает файл в редакторе vi. Нам нужно отредактировать файл, нажимаем на `i` чтобы перейти в режим вставки и печати, используя стрелки для перемещения вводим строку которая нам нужна, в моем случае это, `gerasikina ALL=(ALL) ALL`. После нажимаем Esc чтобы выйти из этого режима, а для сохранения нажимаем комбинацию клавиш: `Esc + shift + :` и вводим `wq!` для сохранения.

Результат что все зароботало:


![image](https://github.com/user-attachments/assets/2901c759-6da6-485b-9c92-5a4e9a3ad6e8)

![image](https://github.com/user-attachments/assets/04cacbb8-a76c-4ca8-a995-247f3135e918)

Далее устанавливаем утилиту curl, она используется для передачи данных с использованием различных сетевых потоков.

`sudo yum install curl`

![image](https://github.com/user-attachments/assets/c4a00258-5783-4295-b333-9d8ac5139587)

Скачиваем файл репозитория с помощью команды `sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/e0b5a6f3-acb0-4296-803e-a224ca59382f)

Далее устанавливаю docker, с помощью команды 
`sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/81a6bc3a-e1ec-465d-9d3a-acda0fe5559f)

Запускаем docker и разрешаем автозапуск с помощью команды 
`sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/1c926247-698b-4302-ba92-55a84caad63f)

Получаем последнюю версию docker с помощью api github с помощью команды:
`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

![image](https://github.com/user-attachments/assets/66a002e4-7b9c-4185-9b95-3ab3c4f99440)

Загружаем и устанавливаем последнюю версию dosker
`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/568530e8-d5d7-4386-a50a-741da06272db)

Данная команда вводится для того чтобы файл docker стал исполняемым
`sudo chmod +x /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/ba2e273e-dd40-4bad-a60e-77834277bdee)

Делаем проверку с помощью `ls -l /usr/bin/docker-compose` и видим что файл исполняемый
`-rwxr-xr-x` это значит что файл имеет права на чтение, запись и исполнения 

![image](https://github.com/user-attachments/assets/64341fe6-720e-47dd-b6c3-3f8ea3efe4a9)

Смотрим данной командой какая версия у нас установлена dosker
`docker-compose --version`

![image](https://github.com/user-attachments/assets/560a0419-0058-4215-b597-3aba506ed45a)

Выполняем клонирование удаленного репозитория git, расположенного по указанному url с помощью команды:
`git clone https://github.com/skl256/grafana_stack_for_docker.git`Было отказано в разрешении поэтому пришлось выполнять клонирование репозитория так. Пришлось зайти через su.

![image](https://github.com/user-attachments/assets/da548d56-827e-4a90-97fc-14d6cf5cd5a3)


Данная команда используется для смены текущей директрии на указанную папку, то есть в нашем случае она переходит в директорию с именем grafana_stack_for_docker
`cd grafana_stack_for_docker`

![image](https://github.com/user-attachments/assets/f98524c8-7bae-4065-a168-c442d0d3ae6e)

Выполняем создание директории (папки) с указанным путем, создаёт полный путь, включая все необходимые промежуточные каталоги
`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

![image](https://github.com/user-attachments/assets/86e81bef-55d3-418f-9baf-cff4c5f152f0)

Создает несколько директорий внутри указанного пути
`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data}`

![image](https://github.com/user-attachments/assets/b4cb6452-4f8b-4169-b10b-d8016bcad6d6)

Все файлы и каталоги в указанных директориях будут переданы в собственность текущему пользователю.
`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

![image](https://github.com/user-attachments/assets/edb36ca8-4be5-41cf-82cb-a447de113e89)

Файл grafana.ini уже существует, команда обновит его временные метки.Если файл не существует, команда создаст новый пустой файл с указанным именем по указанному пути.
`touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

![image](https://github.com/user-attachments/assets/7fb3e35a-890d-41e1-bc28-dab237401b00)

Команда копирует все файлы и подкаталоги из директории config в директорию /mnt/common_volume/swarm/grafana/config/.
`cp config/* /mnt/common_volume/swarm/grafana/config/`

![image](https://github.com/user-attachments/assets/f38cafa2-568c-40fe-acec-38f59532059f)

Команда переименовывает файл grafana.yaml в docker-compose.yaml. Можно проверить с помощью команды `ls`
`mv grafana.yaml docker-compose.yaml`

![image](https://github.com/user-attachments/assets/01ebc6a6-60d1-45d8-9b1d-0eb93a2d1f01)

![image](https://github.com/user-attachments/assets/97724114-5742-43a8-be0f-698f22597ec0)

Команда создает и запускает контейнеры в фоновом режиме, используя конфигурацию из файла docker-compose.yaml, с правами суперпользователя.
`sudo docker compose up -d`

![image](https://github.com/user-attachments/assets/0adfe8cb-c8dd-44d8-bc50-8e5a8b8ee32b)

![image](https://github.com/user-attachments/assets/e344382a-9422-4d51-857a-f827342f0b28)

Получается зайти в Grafana. Ссылка: `http//localhost:3000/`
Пароль и логин: `admin`

![image](https://github.com/user-attachments/assets/a3b9c10c-cc20-42e1-ac0b-fb4add3ee1b6)

Зашли в Grafana

![image](https://github.com/user-attachments/assets/7e64f271-3a00-4da1-a7ca-7333927d10e7)

Добавляю к себе в GitHub папку с файлами. Файл `prometeus.yaml` называю сразу правильно `prometheus.yaml`

![image](https://github.com/user-attachments/assets/717963b7-4a65-48f0-87b9-42d2c35be0a0)

После того как мы закрыли VM открываем его по новой что бы запустить docker compose нужно ввести эти команды 
`cd grafana_stack_for_docker` и `sudo docker compose up -d`

Данная команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя, что позволит редактировать его содержимое.
`sudo vi docker-compose.yaml`

![image](https://github.com/user-attachments/assets/8b698a5e-2d98-4739-bee4-a3baec6bc58d)

Запускаем и создаем контейнеры если те не запущенны
`sudo docker compose up -d`

![image](https://github.com/user-attachments/assets/8c787f1e-7f6f-4517-b28e-982069601994)

Отсанавливаем запущенные контейнеры без удаления
`sudo docker compose stop`

![image](https://github.com/user-attachments/assets/6396e6c3-7010-4a94-a1b4-6262abe2a073)

Данная команда используется для оставновки и удаления контейнеров, сетей, томов если они созданы с помощью Docker Compose
`sudo docker compose down`

![image](https://github.com/user-attachments/assets/b537321c-13e2-49da-b8aa-434e9bebb5d4)

Работа с репозиторием, клонируем репозироторий 
`sudo git clone https://github.com/Geraskina/GeraskinaD.git`

![image](https://github.com/user-attachments/assets/b5cdea2b-de07-4c37-b8d8-cc87d0f4ad39)

Переходим в каталог GeraskinaD и смотрим файл

`cd Geraskina`, а после `ls`

`sudo rm README.md` открваем файл `README.md` с правами sudo

Перехожу в каталог `1` и смотрим его содержимое
`cd 1` и `ls`

Смотрим полный путь 
`pwd`

![image](https://github.com/user-attachments/assets/01065452-5ae1-4097-a01e-0cf0507f934d)

Копирование всех файлов GitHub
с помощью `sudo cp -r /home/gerasikina/GeraskinaD/1* /home/gerasikina/grafana_stack_for_docker`

![image](https://github.com/user-attachments/assets/0fd00c4e-4c9e-43cd-94f0-d207133f7ac0)

Перехожу в config 
`cd /mnt/common_volume/swarm/grafana/config`


![image](https://github.com/user-attachments/assets/6a17581f-9771-4004-98f3-dbf25329e410)


Cоздаеv копию файла prometheus.yaml с именем prometheus.yaml1.
`cp prometheus.yaml prometheus.yaml1

![image](https://github.com/user-attachments/assets/4a4daad8-d1ff-43ea-aebe-a994880075cf)


`sudo cp prometheus.yaml /mnt/common_volume/swarm/grafana/config`  выполняет копирование файла prometheus.yaml в указанную директорию с использованием sudo
Смотрим с помощью команды `ls`

![image](https://github.com/user-attachments/assets/3a4f471c-fa38-480e-8639-855c82c748e4)

![image](https://github.com/user-attachments/assets/4b4968ff-6424-4beb-911a-2cc9be509714)

Открываем файл `docker-compose.yaml` для проверки и редоктирования содержимого

![image](https://github.com/user-attachments/assets/1a1f770f-cbdb-4176-98df-d11c266ffb3b)

Открываем файл `prometheus.yaml` для исправления и проверки

![image](https://github.com/user-attachments/assets/defa77d8-fd5f-44af-91a8-cbfb4fef4cb0)

Переходим на `grafana_stack_for_docker` и запускаем Grafana с помощбю команды `sudo docker compose up -d`

![image](https://github.com/user-attachments/assets/a9fbde1b-d624-492a-98cf-e0f536a1daf0)

Работа с графаной

Переходим по ссылке `http://localhost:3000/`

Вводим и в логин и в пароль `admin`

выбираем вкладку Dashboards и создаем Dashboard

жмем кнопку +Add visualization, а после "Configure a new data source"

![image](https://github.com/user-attachments/assets/3758fdde-0874-455a-876d-77c8dda8f9f1)


выбираем Prometheus 

![image](https://github.com/user-attachments/assets/ec8cca0d-ede0-4b2c-acfe-0ae278a9661c)


Код Prometheus `http://prometheus:9090`

![image](https://github.com/user-attachments/assets/6d09d261-5338-4da0-bddf-66e86b3b3304)

Ставлю аунтификацию и url prometheus

![image](https://github.com/user-attachments/assets/c70784d3-04c3-4a24-91d1-d1ce094427cb)

Жму на save test и проверяю что все идет отлично

![image](https://github.com/user-attachments/assets/ab74c95a-5353-4248-8e39-0109447cc9d8)

Импортирую 1860 dashboard

![image](https://github.com/user-attachments/assets/e9973feb-c414-445a-a489-5ae64b372e8f)

Выбираю prometheus и импортирую

![image](https://github.com/user-attachments/assets/a28a00b7-1210-4721-95e0-e13ddc4ec24a)

Результат:

![image](https://github.com/user-attachments/assets/57dd4309-8b01-4dbf-a53b-0bd10c7b356f)

После завершения проверок, когда нам надо выключить компьютер мы выполняем следующие команды

`sudo docker compose stop`

![image](https://github.com/user-attachments/assets/1ef87b36-ccc2-4262-b41a-4252c3279802)


и после неё

`sudo docker compose down`

![image](https://github.com/user-attachments/assets/28ec6131-2cbe-44bb-ac8d-575de4259048)

Для того чтобы все работало при в следующем включении корректно

Перед тем как заходить в графану нужно ввести, чтобы она заработала

`cd grafana_stack_for_docker`

`sudo docker compose up -d`

от 20.03.2025г.
Столкнулась с проблемой перед тем как заходить в VictoriaMetrics
в файле docker-compose.yaml почему то не было VictoriaMetrics

Ввела в ручную:

![image](https://github.com/user-attachments/assets/f450d19e-06d6-4690-b09b-a01300ab28f1)

Данный код написан после prometheus

![image](https://github.com/user-attachments/assets/6fceb549-0d30-4bd9-b5be-374fc9501a22)

Но ошибки не прикратились, выдовалось предупреждение. Переводится как: служба «vmagent» ссылается на неопределённый том vmagentdata: недопустимый проект компоновки.
Ошибка означает, что в файле docker-compose.yml указан использованый том vmagentdata в сервисе vmagent, но не определен этот том в секции volumes нашего файла. Docker Compose не знает, что это за том и как его создать. Поэтому и выходит это предупреждение

![image](https://github.com/user-attachments/assets/9d878a6d-2597-403d-a090-2e2ad733b6cb)

Для решения данной проблемы нужно скопировать конец код docker-compose.yaml который находится у нас в GitHub

![image](https://github.com/user-attachments/assets/5a7b23eb-617a-4005-a14e-77eaa04d90ad)

И вставить его в самый конец кода

![image](https://github.com/user-attachments/assets/038870e1-c30d-490f-af76-54c0aa70fd96)

Смотрим что бы команда `sudo docker compose up -d` прошла

![image](https://github.com/user-attachments/assets/594f5add-7837-420c-ab77-dc028aee16f4)

Можем приступать к VictoriaMetrics

Вводим команду `echo -e "# TYPE light_metric1 gauge\nlight_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus`
она отправляет двоичные данные метрики в формате Prometheus, который прослушивает порт 8428

![image](https://github.com/user-attachments/assets/78944c6b-abe9-49ee-a59f-f24893766837)


Переходим в браузер по ссылке `http:/localhost:8428/`
Нам открывается меню в котором надо выбрать `vmui`

![image](https://github.com/user-attachments/assets/384ee258-8978-4647-a852-24e371271256)

В открывшееся окно мы вписываем `light_metric1`и жмем на `Execute Query`

![image](https://github.com/user-attachments/assets/2122685e-aa32-4951-8b85-5f7ee68c1b9e)

Вписываем:

Имя: `VikMetrics`

Полключение: http://victoriametrics:8428

![image](https://github.com/user-attachments/assets/b530e30c-6899-4948-a25c-e6be5d1e7bf5)

Вводим `light_metric1` и смотрим на панель с графиком

![image](https://github.com/user-attachments/assets/dcea3499-a358-4a18-b393-ba88f04ad288)

График:

![image](https://github.com/user-attachments/assets/5dbcd59a-2669-46ad-a464-d66dc9014d4f)

Открываем `http://localhost:8428 - vmui`
все работает

![image](https://github.com/user-attachments/assets/e7c19ed9-5c0b-45f9-bd90-fac766368f2f)


Копирование файлов, это не продолжение, этой мой косяк
1. переходим сюда `cd grafana_stack_for_docker`

2. Этой командой сотрим файлы`ls`

3. Эта команда копирует файл и дает ему название dockere_compose1 `sudo cp docker-compose.yaml docker-compose.yaml1`

4. Выполняем ещё раз команду `ls` смотрним что всее добавилось

![image](https://github.com/user-attachments/assets/3aa6705a-255b-4c04-bf94-7ab532fcab53)






