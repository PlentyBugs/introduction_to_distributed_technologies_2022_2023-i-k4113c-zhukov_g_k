University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2022/2023  
Group: K4113c  
Author: Zhukov Georgii Konstantinovich  
Lab: Lab2  
Date of create: 02.12.2022  
Date of finished: 02.12.2022  

# Ход работы
1) Скачивание образа  

Скачиваем образ контейнера с указанного в лабораторной работе сайта.  

 ![docker-run.png](screenshots/docker-run.png)  
 
 2) Манифест  
 
 Создадим deployment манифест.    
 ```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab2
  labels:
    app: lab2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab2
  template:
    metadata:
      labels:
        app: lab2
    spec:
      containers:
        - name: lab2
          image: ifilyaninitmo/itdt-contained-frontend:master
          ports:
            - containerPort: 3000
          env:
            - name: REACT_APP_USERNAME
              value: "Georgii"
            - name: REACT_APP_COMPANY_NAME
              value: "ITMO"
```
 ![apply-manifest.png](screenshots/apply-manifest.png) 
 
Теперь создадим манифест для сервиса.  
 ```yaml
apiVersion: v1
kind: Service
metadata:
  name: lab2-service
spec:
  selector:
    app: lab2
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 3000
```  
![apply-service.png](screenshots/apply-service.png) 

3) Страница сервера

![react-app.png](screenshots/react-app.png)  

4) Логи

![logs.png](screenshots/logs.png)  

В ходе выполнения лабораторной работы были выполнены все задачи.  

5) Схема

![scheme.png](screenshots/scheme.png) 