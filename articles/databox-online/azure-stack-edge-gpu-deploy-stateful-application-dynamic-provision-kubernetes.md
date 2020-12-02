---
title: Usare kubectl per distribuire un'app con stato Kubernetes tramite la condivisione con provisioning dinamico sul dispositivo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come creare e gestire una distribuzione di applicazioni con stato Kubernetes tramite una condivisione con provisioning dinamico tramite kubectl in un dispositivo GPU Pro Microsoft Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 81a52b26c5291f788ac81caeb2ca5416a2f58d36
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448866"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Usare kubectl per eseguire un'applicazione con stato Kubernetes con StorageClass nel dispositivo GPU Pro Azure Stack Edge

Questo articolo illustra come distribuire un'applicazione con stato a istanza singola in Kubernetes usando un StorageClass per eseguire il provisioning dinamico di archiviazione e una distribuzione. La distribuzione usa i `kubectl` comandi in un cluster Kubernetes esistente e distribuisce l'applicazione MySQL. 

Questa procedura è destinata a coloro che hanno esaminato l' [archiviazione Kubernetes sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md) e hanno familiarità con i concetti di [archiviazione Kubernetes](https://kubernetes.io/docs/concepts/storage/).


## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'applicazione con stato, verificare di aver completato i prerequisiti seguenti nel dispositivo e il client che si userà per accedere al dispositivo:

### <a name="for-device"></a>Per il dispositivo

- Si dispone di credenziali di accesso a un dispositivo Azure Stack Edge Pro a 1 nodo.
    - Il dispositivo è attivato. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    - Il dispositivo ha il ruolo di calcolo configurato tramite portale di Azure ed è dotato di un cluster Kubernetes. Vedere [configurare](azure-stack-edge-gpu-deploy-configure-compute.md)le risorse di calcolo.

### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

- Si dispone di un sistema client Windows che verrà usato per accedere al dispositivo Azure Stack Edge Pro.
    - Il client esegue Windows PowerShell 5,0 o versione successiva. Per scaricare la versione più recente di Windows PowerShell, vedere [installare Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Questo articolo descrive la procedura quando si usa un client Windows. 
    
    - È stata completata la procedura descritta in [accedere al cluster Kubernetes nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Precisamente:
      - È stato creato uno `userns1` spazio dei nomi tramite il `New-HcsKubernetesNamespace` comando. 
      - Creazione di un utente `user1` tramite il `New-HcsKubernetesUser` comando. 
      - Ha concesso l' `user1` accesso a `userns1` tramite il `Grant-HcsKubernetesNamespaceAccess` comando.       
      - Installato `kubectl` nel client e salvato il `kubeconfig` file con la configurazione utente in C: \\ Users \\ &lt; nomeutente &gt; \\ . Kube. 
    
    - Verificare che la versione del `kubectl` client non sia più di una versione della versione master di Kubernetes in esecuzione nel dispositivo Azure stack Edge Pro. 
        - Usare `kubectl version` per verificare la versione di kubectl in esecuzione sul client. Prendere nota della versione completa.
        - Nell'interfaccia utente locale del dispositivo Azure Stack Edge Pro passare a **Panoramica** e prendere nota del numero di software Kubernetes. 
        - Verificare che queste due versioni siano compatibili con il mapping fornito nella versione supportata di Kubernetes<!-- insert link-->. 


È possibile distribuire un'applicazione con stato sul dispositivo Azure Stack Edge Pro. 


## <a name="deploy-mysql"></a>Distribuire MySQL

A questo punto si eseguirà un'applicazione con stato creando una distribuzione Kubernetes e collegandolo al StorageClass incorporato usando un PersistentVolumeClaim (PVC). 

Tutti i `kubectl` comandi usati per creare e gestire distribuzioni di applicazioni con stato devono specificare lo spazio dei nomi associato alla configurazione. Per specificare lo spazio dei nomi in un comando kubectl, usare `kubectl <command> -n <your-namespace>` .

1. Ottenere un elenco dei pod in esecuzione nel cluster Kubernetes nello spazio dei nomi. Un pod è un contenitore dell'applicazione o un processo in esecuzione nel cluster Kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Ecco un esempio di sintassi del comando:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   L'output deve indicare che non sono state trovate risorse (POD) perché non ci sono applicazioni in esecuzione nel cluster.

1. Si useranno i file YAML seguenti. Il `mysql-deployment.yml` file descrive una distribuzione che esegue MySQL e fa riferimento al PVC. Il file definisce un montaggio del volume per `/var/lib/mysql` e quindi crea un PVC che cerca un volume di 20 GB. Viene eseguito il provisioning di un PV dinamico e il PVC è associato a questo PV.

    Copiare e salvare il file seguente in `mysql-deployment.yml` una cartella nel client Windows usato per accedere al dispositivo Azure stack Edge Pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim-sc
    ```
    
2. Copiare e salvare come `mysql-pvc.yml` file nella stessa cartella in cui è stato salvato `mysql-deployment.yml` . Per usare il StorageClass incorporato che Azure Stack dispositivo Pro Edge in un disco dati collegato, impostare il `storageClassName` campo nell'oggetto PVC su `ase-node-local` e accessModes deve essere `ReadWriteOnce` . 

    > [!NOTE] 
    > Verificare che i rientri dei file YAML siano corretti. È possibile verificare con la [lanugine YAML](http://www.yamllint.com/) per convalidare e quindi salvare.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Distribuire il `mysql-pvc.yaml` file.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Di seguito è riportato un esempio di output della distribuzione.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Si noti il nome del PVC creato, in questo caso `mysql-pv-claim-sc` . Verrà usato in un passaggio successivo. 

4. Distribuire il contenuto del `mysql-deployment.yml` file.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Di seguito è riportato un esempio di output della distribuzione.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Visualizzare informazioni sulla distribuzione.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Elenca i pod creati dalla distribuzione.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Di seguito è riportato un esempio di output.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Esaminare PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Di seguito è riportato un esempio di output.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verificare che MySQL sia in esecuzione

Per verificare che l'applicazione sia in esecuzione, digitare:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Quando richiesto, specificare la password. La password è presente nel `mysql-deployment` file.

Di seguito è riportato un esempio di output.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Per eliminare la distribuzione, eliminare gli oggetti distribuiti in base al nome. Questi oggetti includono distribuzione, servizio e PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Di seguito è riportato un esempio di output di quando si elimina la distribuzione e il servizio.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Di seguito è riportato un esempio di output di quando si elimina il PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la rete tramite kubectl, vedere [distribuire un'applicazione senza stato in un dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)