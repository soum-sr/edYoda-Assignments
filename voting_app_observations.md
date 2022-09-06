
# Observations

## Current Pod Status
```
[root@ip-172-31-1-177 ~]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-7lvhd        1/1     Running   0          13m
redis-868d64d78-bjh6j     1/1     Running   0          13m
result-5d57b59f4b-txpfp   1/1     Running   0          13m
vote-94849dc97-5cnr8      1/1     Running   0          13m
worker-dd46d7584-fn2k8    1/1     Running   0          13m
```

## Deleting Pods one by one
- Deleting vote pod: The app didn't crash, votes didn't change and the pod went back up immediately.
```
[root@ip-172-31-1-177 ~]# kubectl delete pod vote-94849dc97-5cnr8
pod "vote-94849dc97-5cnr8" deleted
[root@ip-172-31-1-177 ~]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-7lvhd        1/1     Running   0          18m
redis-868d64d78-bjh6j     1/1     Running   0          18m
result-5d57b59f4b-txpfp   1/1     Running   0          18m
vote-94849dc97-7x2pw      1/1     Running   0          8s
worker-dd46d7584-fn2k8    1/1     Running   0          18m
```

- Deleting worker pod: The app didn't crash, votes didn't change and the pod went back up immediately.
```
[root@ip-172-31-1-177 ~]# kubectl delete pod worker-dd46d7584-fn2k8
pod "worker-dd46d7584-fn2k8" deleted
[root@ip-172-31-1-177 ~]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-7lvhd        1/1     Running   0          20m
redis-868d64d78-bjh6j     1/1     Running   0          20m
result-5d57b59f4b-txpfp   1/1     Running   0          20m
vote-94849dc97-7x2pw      1/1     Running   0          2m42s
worker-dd46d7584-l65wj    1/1     Running   0          56s
```

- Deleting db pod: The app didn't crash, but now the app is stuck with the old votes and not updating on voting. A new db pod was created.
```
[root@ip-172-31-1-177 ~]# kubectl delete pod db-b54cd94f4-7lvhd
pod "db-b54cd94f4-7lvhd" deleted
[root@ip-172-31-1-177 ~]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vn6cd        1/1     Running   0          90s
redis-868d64d78-bjh6j     1/1     Running   0          24m
result-5d57b59f4b-txpfp   1/1     Running   0          24m
vote-94849dc97-7x2pw      1/1     Running   0          6m40s
worker-dd46d7584-l65wj    1/1     Running   1          4m54s
[root@ip-172-31-1-177 ~]#
```

## Making the app work

When the db pod was deleted the app lost its connection to the existing db, since the socket was not available. Deleting the result pod resolves this issue, as creation of a new instance of result pod a new connection with the db will be established. But when doing so the previous data stored in the db will be lost.

```
[root@ip-172-31-1-177 ~]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vn6cd        1/1     Running   0          12m
redis-868d64d78-bjh6j     1/1     Running   0          35m
result-5d57b59f4b-txpfp   1/1     Running   0          35m
vote-94849dc97-7x2pw      1/1     Running   0          17m
worker-dd46d7584-l65wj    1/1     Running   1          15m
[root@ip-172-31-1-177 ~]# kubectl delete pod
error: resource(s) were provided, but no name, label selector, or --all flag specified
[root@ip-172-31-1-177 ~]# kubectl delete pod result-5d57b59f4b-txpfp
pod "result-5d57b59f4b-txpfp" deleted
[root@ip-172-31-1-177 ~]#
```


