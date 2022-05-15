# Домашнее задание к занятию "5.5. Оркестрация кластером Docker контейнеров на примере Docker Swarm"

## Задача 1

Дайте письменые ответы на следующие вопросы:

* В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global? *Ответ: по сути это ограничитель развертывания реплик, имеющая два режима, глобальный режим - одна реплика, репликация - несколько.*
* Какой алгоритм выбора лидера используется в Docker Swarm кластере? *Ответ: алгоритм консенсуса Raft.*
* Что такое Overlay Network? *Ответ: Overlay-сети используются в контексте кластеров (Docker Swarm), где виртуальная сеть, которую используют контейнеры, связывает несколько физических хостов, на которых запущен Docker.*

## Задача 2

Создать ваш первый Docker Swarm кластер в Яндекс.Облаке

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:

```
docker node ls
```

*Решение:*

```
root@cadcixztkv:~# ssh centos@51.250.89.208
[centos@node01 ~]$ sudo -i
[root@node01 ~]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
3oef9oamwcxd48vmmqbamzdcd *   node01.netology.yc   Ready     Active         Leader           20.10.16
t9c7vhe84mq4hbo4ycxur8ytb     node02.netology.yc   Ready     Active         Reachable        20.10.16
ptltla16dvxojyejsy1u3tqf6     node03.netology.yc   Ready     Active         Reachable        20.10.16
kqj9d70x88nqov7o7t0j2ni9s     node04.netology.yc   Ready     Active                          20.10.16
mnrniqrj6ordl6grrlull88yx     node05.netology.yc   Ready     Active                          20.10.16
orioagdz9o7kway7qtlr97r6v     node06.netology.yc   Ready     Active                          20.10.16

```



## Задача 3

Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:

```
docker service ls
```

*Решение:*

```
[root@node01 ~]# docker service ls
ID             NAME                                MODE         REPLICAS   IMAGE                                          PORTS
sv0qjtdzmj8p   swarm_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0
zj7whoe2ez9i   swarm_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
0ewvs2cqhlnw   swarm_monitoring_cadvisor           global       6/6        google/cadvisor:latest     
jwssgg8xa84h   swarm_monitoring_dockerd-exporter   global       6/6        stefanprodan/caddy:latest  
nqboi573odgy   swarm_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4
quz10a0nnked   swarm_monitoring_node-exporter      global       6/6        stefanprodan/swarmprom-node-exporter:v0.16.0
86y3c1yejjmn   swarm_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0
cokd1y3bhxmo   swarm_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0    

```

## Задача 4 (*)

Выполнить на лидере Docker Swarm кластера команду (указанную ниже) и дать письменное описание её функционала, что она делает и зачем она нужна:

```
# см.документацию: https://docs.docker.com/engine/swarm/swarm_manager_locking/
docker swarm update --autolock=true
```

*Решение:*

```
[root@node01 ~]# docker swarm update --autolock=true
Swarm updated.
To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    SWMKEY-1-vdaA9t2WNyu/KGegBpkBgIeHh2ltZuUOl9VP12D1oAI

Please remember to store this key in a password manager, since without it you
will not be able to restart the manager.

```

Этой командой Docker может защитить общий ключ шифрования TLS и ключ, используемый для шифрования и расшифровки журналов Raft в состоянии покоя, позволяя вам стать владельцем этих ключей и требовать ручной разблокировки ваших менеджеров. Эта функция называется автоблокировкой (autolock). Когда Docker перезапустится, нужно будет сначала разблокировать swarm, используя ключ шифрования, сгенерированный Docker, когда swarm был заблокирован.
