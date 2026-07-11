## steps to crete an application on k8s studentapp 

1. Step1 : create an cluster on aws eks using clusrt.md
2. step2 : configure sequrity greoup in eks
![eks-sg](./images/eks-sg.png)

## in SG enable application ports whaich are used in both the cluser and node sg  
3. step3 : create an database instance in RDS use MySQL as a database engin
![mysql](./images/mysql.png)
## in mysql make shure you are usng in eks vpc for connection and also configure SG of rds enabaling application ports 

4. step4 : ssh into Worker node instance to crete an database in mysql-rds database
```
yum update
yum install mysql-client -y
sudo yum install mariadb105-server -y
mysql -h <database-endpoint> -u <username> -p<password>
CREATE DATABASE student_db;

```
5. step5 : back to eks-access server now clone application repository
`git clone https://github.com/mukundDeo9325/student-app-k8s.git` <--- repo is cloned

install docker 
```
sudo apt update -y
sudo apt install docker.io  -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
docker --version
```

get in application repo
```
cd student-app-k8s
cd backend
vim src/main/resources/application.properties  ## update the database credentials
```
```
docker build -t username/backendrepo .
```
## docker login and push the images to docker hub 
```
docker login
docker push username/backendrepo:tag
```
6. step6 : run ingress if using | update backend.yaml to loadbalancer
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml` -- run to apply ingress 
## also apply ingress.yaml
```ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-svc
            port:
              number: 80

      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend-svc
            port:
              number: 8080
```
## this will crete and backend and frontend path with ingress 

8. step7 : update docker images in backend.yaml file and run backend deployemdnt file
```
kubectl apply -f backend.yaml
```





