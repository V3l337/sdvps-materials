# Что такое DevOps. СI/СD - Матвеев Валентин

## Задание 1
**Что нужно сделать:**
* Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
* Установите на машину с jenkins golang.
* Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
* Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта go test . и docker build ..

**Решение:**

1. Установил Java - `sudo apt install default-jre`

2. Установил Jenkins - <https://www.jenkins.io/doc/book/installing/linux/#debianubuntu>

`sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian/jenkins.io-2023.key`

`echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null`

`sudo apt-get update`

`sudo apt-get install jenkins`

3. Установил go - <https://go.dev/doc/install>
   
`wget https://go.dev/dl/go1.22.2.linux-amd64.tar.gz`

`sudo rm -rf /usr/local/go`

`sudo tar -C /usr/local -xzf go1.22.2.linux-amd64.tar.gz`

`export PATH=$PATH:/usr/local/go/bin`

Была ошибка, в сборке добавил пользователя в группу докер - `sudo usermod -aG docker jenkins`

4. В дженкинс подключил свою репу и настроил - Freestyle job

/usr/local/go/bin/go test .

docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER

**Вывод:**

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/1_задание_настройки_1.png)

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/1_задание_настройки_2.png)

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/1_задание_вывод.png)


## Задание 2

**Что нужно сделать:**

* Создайте новый проект pipeline.
* Перепишите сборку из задания 1 на declarative в виде кода.

Решение:

```python
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {
    git branch: 'main', 
    url: 'https://github.com/V3l337/sdvps-materials.git'}
  }
  stage('Test') {
   steps {
    sh '/usr/local/go/bin/go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'
   }
  }
 }
}
```
**Вывод:**

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/2_задание_настройки_1.png)

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/2_задание_вывод.png)


## Задание 3
**Что нужно сделать:**

* Установите на машину Nexus.
* Создайте raw-hosted репозиторий.
* Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
* Загрузите файл в репозиторий с помощью jenkins.

**Решение:**

1. Установка и запуск Nexus:
   
`docker pull sonatype/docker-nexus3`

`docker run -d -p 8081:8081 -p 8082:8082 --name nexus -e INSTALL4J_ADD_VM_PARAMS="-Xms1024m -Xmx1024m -XX:MaxDirectMemorySize=1024m" sonatype/docker-nexus3`

2. Конфиг: 

```python
pipeline {
 agent any
 stages {
  stage('Git') {
    steps {
      git branch: 'main', 
      url: 'https://github.com/V3l337/sdvps-materials.git'}
  }
  stage('Test') {
    steps {
      sh '/usr/local/go/bin/go test .'}
  }
  stage('Build') {
    steps {
      sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'}
  }
  stage('Push') {
    steps {
      sh 'docker login ubuntu-bionic:8082 -u admin -p admin123 && docker push ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER && docker logout' }
  }
 }
}
```
**Вывод:**

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/3_задание_настройки_1.png)

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/3_задание_вывод.png)

![alt text](https://github.com/v3l337/sdvps-materials/blob/main/decision/3_задание_Nexus.png)
