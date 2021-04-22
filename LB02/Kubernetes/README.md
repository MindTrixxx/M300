## 1. Einleitung

Als Erstes habe ich mit meinem eigenen Computer versucht, diversen Befehlen von Kubectl durchzuführen. Es hat jedoch nicht funktioniert, nun habe ich mittels Visual Studio Code mit SSH auf meinem Server remote verbunden und arbeite nun von dort aus.
<img src="/Bilder/Bild7.jpg" alt="Check"/>

## 2. VWie funktioniert Kubernetes? 
<img src="/Bilder/Bild8.jpg" alt="Check"/>

### Deplyoment erstellen
Es wurde ein deployment.yaml File erstellt, welches folgenden Code beinhaltet:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: m300deployment
  labels:
    app: m300
spec:
  replicas: 5
  selector:
    matchLabels:
      app: m300
  template:
    metadata:
      labels:
        app: m300
    spec:
      containers:
      - name: m300-web
        image: hallo987123hallo/test-cloud-image:test
        imagePullPolicy: Always
        ports:
        - containerPort: 80
```
Dannach muss man das File "applyen", damit die "Container" welche im .yaml File definiert sind erstellt werden.
File Applyn:
`kubectl apply -f Name.yaml`

Mit `kubectl get pods` Kann man die Pods anzeigen lassen:


### Service 
Ein File .yaml erstellt, das ein Loadbalancer (Service) macht:
```
apiVersion: v1
kind: Service
metadata:
  name: m300service
  annotations:
    service.beta.kubernetes.io/linode-loadbalancer-throttle: "4"
  labels:
    app: m300service
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: m300
  sessionAffinity: None
```
Wie man in dem Deployment und dem Serice File sieht steht bei app überall das gleiche. Der Loadbalancer verwendet alle "Contauner", welche bei app das gleiche Keyword haben.

Service ausführen:
`kubectl apply -f Name.yaml`

Service anzeigen:
`kubectl get services`



Meine Erfahrungen
```
Das Arbeiten mit Kubernetes war nicht einfach für mich. Es fehlt mir noch viel Wissen, damit ich ein richtiges Projekt machen kann. Jedoch verglichen mit zuvor kenne ich Kubernetes einiges besser und kann auch mit einfachen Befehlen, Pods, Services deployment machen. Das Prinzip und die Vorteile von Kubernetes sind enorm. Das ist sehr zukunftsorientiert und ich kann mir vorstellen, dass Firmen später vermehrt auf Cloud Computing wechselt.

```