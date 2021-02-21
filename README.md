Docker Swarm概念
====


[![hackmd-github-sync-badge](https://hackmd.io/XcvuSEGBSZq7XNcpLE0dQQ/badge)](https://hackmd.io/XcvuSEGBSZq7XNcpLE0dQQ)

### Index

1. [Docker Swarm集合概念](#docker_swarm) 
2. [Docker Swarm如何運作](#docker_work)

----
<a name="docker_swarm"/>

![](https://i.imgur.com/AFYOKlR.png)

### 1.Docker Swarm集合概念

1. [Node](#node)
2. [Stack](#stack)
3. [Service](#service)
4. [Container](#container)
5. [Image](#image)

<a name="node"/>

#### 1. Node: 即為不同的Host，by docker swarm指令做串接指定Manager/Worker
```
# 起始一個IP作Leader(Manager) Node
docker swarm init --advertise-addr want_be_manager_IP

# 在Leader Node查詢欲作的Manager Token
docker swarm join-token -q manager

# 將其他Node串接成Manager(將xxx-x-xxx換成上面查詢的Token)
docker swarm join --token xxx-x-xxx Manager_IP:2377

# 在Leader Node查詢欲作的Worker Token
docker swarm join-token -q worker

# 將其他Node串接成Worker(將xxx-x-xxx換成上面查詢的Token)
docker swarm join --token xxx-x-xxx Manager:2377
```

#### Docker Node Command

![](https://i.imgur.com/buomGvs.png)

```
# 確認目前Swarm Cluster狀況
docker node ls
```

![](https://i.imgur.com/xAUJ7R4.png)
![](https://i.imgur.com/EM8SEy3.png)


```
# 確認node中的詳細資訊
docker node inspect --pretty leon-linux
```

![](https://i.imgur.com/bj5XvQb.png)
![](https://i.imgur.com/ZOve6Sp.png)

```
# 確認Node中使用過的service
docker node ps localhost.150
```

![](https://i.imgur.com/KsJ6yrY.png)


---

<a name="stack"/>

#### 2. Stack: 可創建內網連結不同的Node已達到`Fail Over`，並且一個Stack含多個Service

#### Docker Stack Command
![](https://i.imgur.com/LeHkAVN.png)


```
# 查詢目前有的Stack list
docker stack ls 
```

![](https://i.imgur.com/Fx4odRv.png)
![](https://i.imgur.com/sspElQ8.png)

```
# 查詢指定Stack中的Service內容
docker stack ps nginx_cluster
```
![](https://i.imgur.com/AOT0qG3.png)
![](https://i.imgur.com/4Uv8vOk.png)

---

<a name="service"/>

#### 3. Service: 可創多個Replicate，作為Container服務Backup輪流運作已達到`Loading Balance`

#### Docker Service Command
![](https://i.imgur.com/8KZughf.png)


```
# 確認目前已服務的Docker Service
docker service ls
```

![](https://i.imgur.com/nJF4fCl.png)
![](https://i.imgur.com/18d5yrH.png)

```
# Check Service細項內容
docker service inspect nginx_cluster_nginxloading
```

![](https://i.imgur.com/EX4FR1A.png)

```
# Check service status
docker service ps nginx_cluster_nginx1
```
![](https://i.imgur.com/weX2z9L.png)
![](https://i.imgur.com/hN3L97W.png)

```
# 將Docker Service's replicate擴展到N個
docker service scale nginx_cluster_nginx1=3
```
![](https://i.imgur.com/kbrNCw6.png)
![](https://i.imgur.com/BJFxKTg.png)

```
# 將目前Service使用的Image作更新
docker service update --image new_image_name:tag nginx_cluster_nginx1
```

![](https://i.imgur.com/RnopoLU.png)


```
# 查詢指定Service Log內容
docker service logs -f nginx_cluster_nginx1
```

![](https://i.imgur.com/FvaPUgT.png)
![](https://i.imgur.com/bqRpMfa.png)

---

<a name="container"/>

#### 4. Container: 此即為一般執行Docker服務

* 服務皆可藉由Portainer作簡單操作
![](https://i.imgur.com/fcIsTJb.png)
![](https://i.imgur.com/TtiZ1cU.png)


<a name="image"/>

#### 5. Image: 此即為Docker最厲害的地方，將安裝繁瑣的軟體像料理真空包一樣做成Image

----

<a name="docker_work"/>

### 2.Docker Swarm如何運作

![](https://i.imgur.com/ByHkmC0.png)
![](https://i.imgur.com/2B4cC4c.png)

* APP Service若集中在同一Worker(如上圖的`App2 Service`)，其只能說有`Loading Balance`

* APP Service若集中在不同Worker(如上圖的`App1 Service`)，就真正做到`Fail Over`及`Loading Balance

* 擴增Node方便,請參考[上面寫的Node](#node)
`
