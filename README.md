
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

Получается зайти в Grafana. Ссылка: http//localhost:3000/
Пароль и логин: admin

![image](https://github.com/user-attachments/assets/a3b9c10c-cc20-42e1-ac0b-fb4add3ee1b6)

Зашли в Grafana

![image](https://github.com/user-attachments/assets/7e64f271-3a00-4da1-a7ca-7333927d10e7)




