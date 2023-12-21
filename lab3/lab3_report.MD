
University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4113c  
Author: Smirnov Georgy 
Lab: Lab3  
Date of create: 21.12.2023  
Date of finished: 21.12.2023
---
# Сертификаты и "секреты" в Minikube, безопасное хранение данных
## Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.
## Ход работы
### 1. Cоздание configMap с переменными: REACT_APP_USERNAME, REACT_APP_COMPANY_NAME
Переменные REACT_APP_USERNAME, REACT_APP_COMPANY_NAME передаются в Deployment

Создаем `ConfigMap` с переменными `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`
![ConfigMap](screens/ConfigMap.png)

![cmd](screens/created_configMap.png)

### 2. Создание ReplicaSet с 2 репликами контейнера и Service
Прописываем `envForm`, чтобы переменные были взяты из `ConfigMap`.

![ConfigMap](screens/ReplicaSet.png)

![cmd](screens/created_replicaSet.png)

Создаем Service

![ConfigMap](screens/service.png)

![cmd](screens/service_created.png)

### 3. Генерация TLS сертификата
Сгенерируем TLS сертификат

![generate_key](screens/generate_key.png)
![generate_cert](screens/generate_cert.png)


Далее создаем секрет, добавляя сертификат в `minikube` с помощью команды:
```
kubectl create secret tls tls-secret -o yaml --key=tls.key --cert=tls.crt > secret.yaml
```

### 4. Создание Ingress
Чтобы подключить `ingress`, необходимо выполнить команду:
```
minikube addons enable ingress
```

Далее необходимо написать манифест для `ingress`

![ingress](screens/ingress.png)

### 5. Создание Secret
```yml
apiVersion: v1
kind: Secret
metadata:
  name: lab3-tls
type: kubernetes.io/tls
stringData:
  tls.crt: |
    -----BEGIN CERTIFICATE-----

    -----END CERTIFICATE-----
  tls.key: |
    -----BEGIN PRIVATE KEY-----

    -----END PRIVATE KEY-----
```

### 6. Запуск деплоймента
Для применения всего написанного в манифесте выполним команду:
```
kubectl apply -f deployment.yaml
```

### 6. Перейдем в браузер
Для начала необходимо добавить запись в файл `hosts`, чтобы связать ip-адрес и доменное имя:

![hosts](pic/pic4.png)

Подключаемся к Ingress командой с помощью команды:
```
minikube tunnel
```

Открываем страницу с адресом  `https://danya1234.com/`

![open_site](pic/pic1.png)

### 7. Проверка наличия сертификата
Ниже представленны данные сертификата:

![view_certificate](pic/pic2.png)

## Схема

![scheme](pic/pic5.png)