# 01-kuber

# Задача 1

У меня уже есть кластер kubernetes установленый с помощью kubeadm
В нем уже присудствуют настроенные контейнеры для работы моих проектов.
В ходе работы был установлен dashboard с помощью helm-chart и выведен наружу с помощью ingress поднятом на metallb

## Установка:

```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
"kubernetes-dashboard" has been added to your repositories

helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
Release "kubernetes-dashboard" does not exist. Installing it now.
NAME: kubernetes-dashboard
LAST DEPLOYED: Wed Jan 22 11:55:36 2025
NAMESPACE: kubernetes-dashboard
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
*************************************************************************************************
*** PLEASE BE PATIENT: Kubernetes Dashboard may need a few minutes to get up and become ready ***
*************************************************************************************************

Congratulations! You have just installed Kubernetes Dashboard in your cluster.

To access Dashboard run:
  kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443

NOTE: In case port-forward command does not work, make sure that kong service name is correct.
      Check the services in Kubernetes Dashboard namespace using:
        kubectl -n kubernetes-dashboard get svc

Dashboard will be available at:
  https://localhost:8443
```

Создан yml-конфиг:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Получен токен командой:

```
kubectl -n kubernetes-dashboard create token admin-user
```

Конфиг для metallb:

```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: fast-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.16.144/32
  - 192.168.16.142/32

---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: fast
  namespace: metallb-system
spec:
  ipAddressPools:
  - fast-pool
```

Так как я пользую k9s скриншоты будут представлены из него:
![image](https://github.com/user-attachments/assets/57d4cd04-16ff-4a56-ace9-f2d3bd7397dc)

![image](https://github.com/user-attachments/assets/f62e49e8-3699-485d-afbe-98bf5f5d17df)

![image](https://github.com/user-attachments/assets/3e157300-0889-4de0-abbb-0256e37d6a32)
