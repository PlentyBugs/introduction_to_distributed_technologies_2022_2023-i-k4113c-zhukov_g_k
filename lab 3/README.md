University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2022/2023  
Group: K4113c  
Author: Zhukov Georgii Konstantinovich  
Lab: Lab3    
Date of create: 10.12.2022  
Date of finished: 10.12.2022  

# Ход работы
1) Создание сертификата  

Используя статью с [медиума](https://medium.com/avmconsulting-blog/how-to-secure-applications-on-kubernetes-ssl-tls-certificates-8f7f5751d788) создадим сертификат на линуксе.

 ![get-cert.png](screenshots/get-cert.png)  
 
 Следующей командой мы добавляем секрет в minikube
 ![create-secret.png](screenshots/create-secret.png)  
 
 2) Добавление аддонов  
 
 Для того, чтобы всё работало надо добавить аддоны с ingress  
 ![addon.png](screenshots/addon.png)
 
 3) Создание манифестов  
 
 Далее нужно создать и применить все нужные нам манифесты - config, deployment, service и ingress

![apply-configs.png](screenshots/apply-configs.png)  
 
Config:  
 ```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  REACT_APP_USERNAME: Georgii
  REACT_APP_COMPANY_NAME: ITMO
```  

Deployment:  
 ```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab3
  labels:
    app: lab3
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab3
  template:
    metadata:
      labels:
        app: lab3
    spec:
      containers:
        - name: lab3
          image: ifilyaninitmo/itdt-contained-frontend:master
          ports:
            - containerPort: 3000
          envFrom:
            - configMapRef:
                name: my-config
```  

Service:  
 ```yaml
apiVersion: v1
kind: Service
metadata:
  name: lab3-service
spec:
  selector:
    app: lab3
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 3000
```  

Ingress:  
 ```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: lab3-ingress
spec:
  rules:
    - host: localhost-plentybugs.com
      http:
        paths:
          - backend:
              service:
                name: lab3-service
                port:
                  number: 8080
            path: /
            pathType: Prefix
  tls:
    - hosts:
        - localhost-plentybugs.com
      secretName: my-secret
```  

4) Запускаем  

С помощью команды minikube tunnel запускаем сервис  

![main-page.png](screenshots/main-page.png)  
Как мы видим имя домейна поменялось  

![certificate.png](screenshots/certificate.png)  
Также есть данные о сертификате  

![scheme.png](screenshots/scheme.png)

В ходе выполнения лабораторной работы были выполнены все задачи

