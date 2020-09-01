---
title: Distribuire un'applicazione Kubernetes senza stato nel dispositivo GPU Azure Stack Edge usando kubectl | Microsoft Docs
description: Viene descritto come creare e gestire una distribuzione di applicazioni Kubernetes senza stato usando kubectl in un dispositivo Microsoft Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: f2bd0b8c222b9f9643d53da33619af438c121a2f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145961"
---
# <a name="run-and-manage-a-kubernetes-stateless-application-on-your-azure-stack-edge-device"></a>Eseguire e gestire un'applicazione Kubernetes senza stato nel dispositivo Azure Stack Edge

Questo articolo descrive come distribuire un'applicazione senza stato usando i comandi kubectl in un cluster Kubernetes esistente. Questo articolo illustra anche il processo di creazione e configurazione di Pod nell'applicazione senza stato.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare un cluster Kubernetes e usare lo `kubectl` strumento da riga di comando, è necessario verificare quanto segue:

- Sono disponibili credenziali di accesso a un dispositivo Azure Stack Edge a 1 nodo.

- Windows PowerShell 5,0 o versione successiva è installato in un sistema client Windows per accedere al dispositivo Azure Stack Edge. È possibile avere anche un altro client con un sistema operativo supportato. Questo articolo descrive la procedura quando si usa un client Windows. Per scaricare la versione più recente di Windows PowerShell, vedere [installazione di Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).

- Il calcolo è abilitato nel dispositivo Azure Stack Edge. Per abilitare il calcolo, passare alla pagina **calcolo** nell'interfaccia utente locale del dispositivo. Selezionare quindi un'interfaccia di rete che si vuole abilitare per il calcolo. Selezionare **Abilita**. L'abilitazione dei risultati di calcolo nella creazione di un commutire virtuale sul dispositivo in tale interfaccia di rete. Per altre informazioni, vedere [abilitare la rete di calcolo nel Azure stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Il dispositivo Azure Stack Edge dispone di un server cluster Kubernetes che esegue la versione 1.9 o successiva. Per altre informazioni, vedere [creare e gestire un cluster Kubernetes nel dispositivo Microsoft Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- È installato `kubectl` .

## <a name="deploy-a-stateless-application"></a>Distribuire un'applicazione senza stato

Prima di iniziare, è necessario disporre di:

1. Creazione di un cluster Kubernetes.
2. Configurare uno spazio dei nomi.
3. Utente associato con lo spazio dei nomi.
4. La configurazione utente è stata salvata in `C:\Users\<username>\.kube` .
5. Installato `kubectl` .

A questo punto è possibile iniziare a eseguire e gestire le distribuzioni di applicazioni senza stato in un dispositivo Azure Stack Edge. Prima di iniziare `kubectl` a utilizzare, è necessario verificare di disporre della versione corretta di `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Verificare di disporre della versione corretta di kubectl e configurare la configurazione

Per verificare la versione di `kubectl` :

1. Verificare che la versione di `kubectl` sia maggiore o uguale a 1,9:

   ```powershell
   kubectl version
   ```
    
   Di seguito è riportato un esempio dell'output:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   In questo caso la versione client di kubectl è v 1.15.2 ed è compatibile per continuare.

2. Ottenere un elenco dei pod in esecuzione nel cluster Kubernetes. Un pod è un contenitore dell'applicazione o un processo in esecuzione nel cluster Kubernetes.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Ecco un esempio di sintassi del comando:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   L'output deve indicare che non sono state trovate risorse (POD) perché non ci sono applicazioni in esecuzione nel cluster.

   Il comando compilerà la struttura di directory "C:\Users \\ &lt; nomeutente &gt; \\ . Kube \" con i file di configurazione. Lo strumento da riga di comando kubectl utilizzerà questi file per creare e gestire le applicazioni senza stato nel cluster Kubernetes.

3. Controllare manualmente la struttura di directory di "C:\Users \\ &lt; username &gt; \\ . Kube \" per verificare che *kubectl* sia stato popolato con le sottocartelle seguenti:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Per visualizzare un elenco di tutti i comandi kubectl, digitare `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Creare un'applicazione senza stato usando una distribuzione

Ora che è stata verificata la correttezza della versione della riga di comando kubectl e i file di configurazione necessari, è possibile creare una distribuzione di applicazione senza stato.

Un pod è l'unità di esecuzione di base di un'applicazione Kubernetes, l'unità più piccola e più semplice nel modello a oggetti Kubernetes creato o distribuito. Un pod incapsula anche le risorse di archiviazione, un IP di rete univoco e le opzioni che determinano la modalità di esecuzione dei contenitori.

Il tipo di applicazione senza stato creato è una distribuzione del server web nginx.

Tutti i comandi kubectl usati per creare e gestire le distribuzioni di applicazioni senza stato devono specificare lo spazio dei nomi associato alla configurazione. Lo spazio dei nomi è stato creato durante la connessione al cluster sul dispositivo Azure Stack Edge nell'esercitazione [creare e gestire un cluster Kubernetes nel dispositivo Microsoft Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md) con `New-HcsKubernetesNamespace` .

Per specificare lo spazio dei nomi in un comando kubectl, usare `kubectl <command> -n <namespace-string>` .

Per creare una distribuzione di nginx, seguire questa procedura:

1. Applicare un'applicazione senza stato creando un oggetto di distribuzione Kubernetes:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   In questo esempio il percorso del file YAML dell'applicazione è un'origine esterna.

   Di seguito è riportato un esempio di utilizzo del comando e dell'output:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   In alternativa, è possibile salvare il Markdown seguente nel computer locale e sostituire il percorso e il nome file nel parametro *-f* . Ad esempio, "C:\Kubernetes\deployment.yaml". Di seguito è illustrata la configurazione per la distribuzione dell'applicazione:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Questo comando crea una distribuzione nginx predefinita con due pod per eseguire l'applicazione.

2. Ottenere la descrizione della distribuzione di nginx Kubernetes creata:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Di seguito è riportato un esempio di utilizzo del comando e dell'output:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Se si osserva attentamente l'impostazione *repliche* , viene visualizzato quanto segue:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   L'impostazione *repliche* indica che la specifica di distribuzione richiede due pod, che i pod in cui sono stati creati e aggiornati e che sono pronti per l'uso.

   > [!NOTE]
   > Un set di repliche sostituisce i pod eliminati o terminati per qualsiasi motivo, ad esempio in caso di errore del nodo del dispositivo o di un aggiornamento di un dispositivo di disturbo. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.

3. Per elencare i pod nella distribuzione:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Di seguito è riportato un esempio di utilizzo del comando e dell'output:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   L'output verifica che siano presenti due pod con nomi univoci a cui è possibile fare riferimento usando kubectl.

4. Per visualizzare le informazioni su un singolo POD nella distribuzione:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

   Di seguito è riportato un esempio di utilizzo del comando e dell'output:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Ridimensionare la distribuzione dell'applicazione aumentando il numero di repliche

Ogni pod ha lo scopo di eseguire una singola istanza di una determinata applicazione. Se si vuole ridimensionare orizzontalmente l'applicazione per eseguire più istanze, è possibile aumentare il numero di Pod, uno per ogni istanza. In Kubernetes questa operazione viene definita replica.
È possibile aumentare il numero di Pod nella distribuzione dell'applicazione applicando un nuovo file YAML. Il file YAML modifica l'impostazione repliche in 4, che aumenta il numero di Pod nella distribuzione a quattro Pod. Per aumentare il numero di pod da 2 a 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

In alternativa, è possibile salvare i seguenti Markdown nel computer locale e sostituire il percorso e il nome file per il parametro *-f* per `kubectl apply` . Ad esempio, "C:\Kubernetes\deployment-scale.yaml". Di seguito è illustrata la configurazione per la scala di distribuzione dell'applicazione:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Per verificare che la distribuzione disponga di quattro Pod:

```powershell
kubectl get pods -l app=nginx
```

Di seguito è riportato un output di esempio per una distribuzione di ridimensionamento da due a quattro Pod:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Come si può notare dall'output, sono ora disponibili quattro Pod nella distribuzione in grado di eseguire l'applicazione.

### <a name="delete-a-deployment"></a>Eliminare una distribuzione

Per eliminare la distribuzione, inclusi tutti i pod, è necessario eseguire `kubectl delete deployment` specificando il nome della distribuzione *nginx-Deployment* e il nome dello spazio dei nomi. Per eliminare la distribuzione:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Di seguito è riportato un esempio di utilizzo del comando e output:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Kubernetes](azure-stack-edge-gpu-kubernetes-overview.md)
