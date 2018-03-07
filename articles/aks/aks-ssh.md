---
title: Accesso SSH in nodi del cluster del servizio contenitore di Azure (AKS)
description: Creare una connessione SSH con i nodi di un cluster del servizio contenitore di Azure (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>Accesso SSH in nodi del cluster del servizio contenitore di Azure (AKS)

A volte potrebbe essere necessario accedere a un nodo del servizio contenitore di Azure (AKS) per operazioni di manutenzione, raccolta di log o risoluzione dei problemi. I nodi del servizio contenitore di Azure (AKS) non sono esposti in Internet. Usare la procedura descritta in questo documento per creare una connessione SSH a un nodo AKS.

## <a name="configure-ssh-access"></a>Configurare l'accesso SSH

 Per configurare l'accesso SSH in un nodo specifico, viene creato un pod con accesso `hostNetwork`. Per l'accesso al pod viene creato anche un servizio. Questa configurazione dispone di privilegi e deve essere rimossa dopo l'uso.

Creare un file denominato `aks-ssh.yaml` e copiarlo in questo manifesto. Aggiornare il nome del nodo con il nome del nodo AKS di destinazione.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Eseguire il manifesto per creare il pod e il servizio.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Ottenere l'indirizzo IP esterno del servizio esposto. Il completamento della configurazione dell'indirizzo IP potrebbe richiedere un minuto. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Creare la connessione SSH. 

Il nome utente predefinito per un cluster AKS è `azureuser`. Se questo account è stato modificato al momento della creazione del cluster, sostituire il nome utente amministratore appropriato. 

Se la chiave non è presente in `~/ssh/id_rsa`, fornire la posizione corretta tramite l'argomento `ssh -i`.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Rimuovere l'accesso SSH

Al termine, eliminare il pod e il servizio di accesso SSH.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```