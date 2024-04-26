#Что такое DevOps. СI/СD - Матвеев Валентин

##Задание 1
Что нужно сделать:

Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
Установите на машину с jenkins golang.
Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта go test . и docker build ..

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Решение: 

Установил Java - sudo apt install default-jre
Установил Jenkins - https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

Установил go - https://go.dev/doc/install
wget https://go.dev/dl/go1.22.2.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.22.2.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> /etc/profile

Пароль для web - cat /var/lib/jenkins/secrets/initialAdminPassword
Сменил пароль

Была ошибка, в сборке добавил пользователя в группу докер - sudo usermod -aG docker jenkins

В дженкинс подключил свою репу и настроил - Freestyle job
/usr/local/go/bin/go test .
docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER
