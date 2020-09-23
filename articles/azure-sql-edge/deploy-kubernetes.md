---
title: Distribuire un contenitore di SQL Edge di Azure in Kubernetes - SQL Edge di Azure
description: Informazioni sulla distribuzione di un contenitore di SQL Edge di Azure in Kubernetes
keywords: SQL Edge, contenitore, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 16ad757fc00439bb390a7e0dea902901c468dd1c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929438"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Distribuire un contenitore di SQL Edge di Azure in Kubernetes

È possibile distribuire SQL Edge di Azure in un cluster Kubernetes sia come modulo IoT Edge tramite Azure IoT Edge in esecuzione in Kubernetes o come pod contenitore autonomo. Nella parte restante di questo articolo verrà illustrata la distribuzione del contenitore autonomo in un cluster Kubernetes. Per informazioni sulla distribuzione di Azure IoT Edge in Kubernetes, vedere [Azure IoT Edge in Kubernetes (anteprima)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

Questa esercitazione illustra come configurare un'istanza di SQL Edge di Azure a disponibilità elevata in un contenitore in un cluster Kubernetes. 

> [!div class="checklist"]
> * Creare una password dell'amministratore di sistema
> * Creare una risorsa di archiviazione
> * Creare la distribuzione
> * Connettersi con SQL Server Management Studio (SSMS)
> * Verificare l'errore e il ripristino

Kubernetes 1.6 e versioni successive supportano le [classi di archiviazione](https://kubernetes.io/docs/concepts/storage/storage-classes/), le [richieste di volumi persistenti](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims) e il [tipo di volume del disco di Azure](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). È possibile creare e gestire le istanze di SQL Edge di Azure in modo nativo in Kubernetes. L'esempio in questo articolo illustra come creare una [distribuzione](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) per ottenere una configurazione a disponibilità elevata simile a un'istanza del cluster di failover su disco condiviso. In questa configurazione Kubernetes svolge il ruolo di agente di orchestrazione del cluster. Quando si verifica un errore in un'istanza di SQL Edge di Azure in un contenitore, l'agente di orchestrazione avvia un'altra istanza del contenitore che si collega alla stessa risorsa di archiviazione permanente.

![SQL Edge di Azure in un cluster Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

Nel diagramma precedente `azure-sql-edge` è un contenitore in un [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes orchestra le risorse nel cluster. Un [set di repliche](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garantisce che il pod venga automaticamente ripristinato dopo un errore del nodo. Le applicazioni si connettono al servizio. In questo caso, il servizio rappresenta un servizio di bilanciamento del carico che ospita un indirizzo IP che rimane invariato dopo l'errore di `azure-sql-edge`.

Nel diagramma seguente si è verificato un errore nel contenitore `azure-sql-edge`. Come agente di orchestrazione, Kubernetes garantisce il numero corretto di istanze integre nel set di repliche e avvia un nuovo contenitore in base alla configurazione. L'agente di orchestrazione avvia un nuovo pod nello stesso nodo e `azure-sql-edge` si riconnette alla stessa risorsa di archiviazione permanente. Il servizio si connette al contenitore `azure-sql-edge` ricreato.

![SQL Edge di Azure in un cluster Kubernetes dopo un errore nel pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Nel diagramma seguente si è verificato un errore nel nodo che ospita il contenitore `azure-sql-edge`. L'agente di orchestrazione avvia il nuovo pod in un nodo diverso e `azure-sql-edge` si riconnette alla stessa risorsa di archiviazione permanente. Il servizio si connette al contenitore `azure-sql-edge` ricreato.

![SQL Edge di Azure in un cluster Kubernetes dopo un errore nel nodo](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Prerequisiti

* **Cluster Kubernetes**
   - L'esercitazione richiede un cluster Kubernetes. La procedura usa [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) per gestire il cluster. 

   - Ai fini di questa esercitazione, per distribuire SQL Edge di Azure verrà usato il servizio Azure Kubernetes. Vedere [Distribuire un cluster del servizio Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-deploy-cluster) per creare e connettersi a un cluster Kubernetes a nodo singolo nel servizio Azure Kubernetes con `kubectl`. 

   >[!NOTE]
   >Per la protezione da errori del nodo, un cluster Kubernetes richiede più di un nodo.

* **Interfaccia della riga di comando di Azure**
   - Le istruzioni di questa esercitazione sono state convalidate con l'interfaccia della riga di comando di Azure 2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Creare uno spazio dei nomi di Kubernetes per la distribuzione di SQL Edge

Creare un nuovo spazio dei nomi nel cluster Kubernetes. Questo spazio dei nomi verrà usato per distribuire SQL Edge e tutti gli artefatti necessari. Per altre informazioni sugli spazi dei nomi di Kubernetes, vedere [Spazi dei nomi](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Creare una password dell'amministratore di sistema

Creare una password dell'amministratore di sistema nel cluster Kubernetes. Kubernetes può gestire informazioni di configurazione riservate, ad esempio le password come [segreti](https://kubernetes.io/docs/concepts/configuration/secret/).

Il comando seguente crea una password per l'account dell'amministratore di sistema:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Sostituire `MyC0m9l&xP@ssw0rd` con una password complessa.

## <a name="create-storage"></a>Creare una risorsa di archiviazione

Configurare un [volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) e una [richiesta di volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) nel cluster Kubernetes. Completare i passaggi seguenti: 

1. Creare un manifesto per definire la classe di archiviazione e la richiesta di volume persistente.  Il manifesto specifica lo strumento di provisioning di archiviazione, i parametri e i [criteri di recupero](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). Il cluster Kubernetes usa questo manifesto per creare l'archivio permanente. 

   L'esempio YAML seguente definisce una classe di archiviazione e una richiesta di volume persistente. Lo strumento di provisioning della classe di archiviazione è `azure-disk`, perché questo cluster Kubernetes è in Azure. Il tipo di account di archiviazione è `Standard_LRS`. La richiesta di volume persistente è denominata `mssql-data`. I metadati della richiesta di volume persistente includono un'annotazione che la riconnette alla classe di archiviazione. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Salvare il file (ad esempio, **pvc.yaml**).

2. Creare la richiesta di volume persistente in Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` è la posizione in cui si è salvato il file.

   Il volume persistente viene creato automaticamente come account di archiviazione di Azure e associato alla richiesta di volume persistente. 

    ![Screenshot del comando di richiesta di volume persistente](media/deploy-kubernetes/pvc-cmd.png)

3. Verificare la richiesta di volume persistente.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` è il nome della richiesta di volume persistente.

   Nel passaggio precedente la richiesta di volume persistente è denominata `mssql-data`. Per visualizzare i metadati relativi alla richiesta di volume persistente, eseguire il comando seguente:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   I metadati restituiti includono un valore denominato `Volume`. Questo valore esegue il mapping al nome del BLOB.

   ![Screenshot dei metadati restituiti, incluso il volume](media/deploy-kubernetes/describe-volume.png)

4. Verificare il volume persistente.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` restituisce i metadati relativi al volume persistente creato automaticamente e associato alla richiesta di volume persistente. 

## <a name="create-the-deployment"></a>Creare la distribuzione

In questo esempio il contenitore che ospita l'istanza di SQL Edge di Azure viene descritto come oggetto di distribuzione Kubernetes. La distribuzione crea un set di repliche. Il set di repliche crea il pod. 

In questo passaggio creare un manifesto per descrivere il contenitore in base all'immagine Docker di SQL Edge di Azure. Il manifesto fa riferimento alla richiesta di volume persistente `mssql-data` e al segreto `mssql` già applicato al cluster Kubernetes. Il manifesto descrive anche un [servizio](https://kubernetes.io/docs/concepts/services-networking/service/). Si tratta di un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico garantisce che l'indirizzo IP venga mantenuto anche dopo il ripristino dell'istanza di SQL Edge di Azure. 

1. Creare un manifesto (un file YAML) per descrivere la distribuzione. L'esempio seguente descrive una distribuzione, incluso un contenitore basato sull'immagine del contenitore per SQL Edge di Azure.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Copiare il codice precedente in un nuovo file, denominato `sqldeployment.yaml`. Aggiornare i valori seguenti: 

   * MSSQL_PID `value: "Developer"`: imposta il contenitore per l'esecuzione dell'edizione Developer di SQL Edge di Azure. L'edizione Developer non è concessa in licenza per i dati di produzione. Se la distribuzione è destinata all'uso in produzione, impostare l'edizione su `Premium`. 

      >[!NOTE]
      >Per altre informazioni, vedere [Come ottenere una licenza per SQL Edge di Azure](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: questo valore richiede una voce per `claimName:` che esegue il mapping al nome usato per la richiesta di volume persistente. In questa esercitazione viene usato `mssql-data`. 

   * `name: SA_PASSWORD`: configura l'immagine del contenitore per impostare la password dell'amministratore di sistema, come definito in questa sezione.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Quando Kubernetes distribuisce il contenitore, fa riferimento al segreto denominato `mssql` per ottenere il valore per la password. 

   >[!NOTE]
   >Usando il tipo di servizio `LoadBalancer`, l'istanza di SQL Edge di Azure è accessibile in remoto (tramite Internet) sulla porta 1433.

   Salvare il file, ad esempio con il nome **sqledgedeploy.yaml**.

2. Creare la distribuzione.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` è la posizione in cui si è salvato il file.

   ![Screenshot del comando di distribuzione](media/deploy-kubernetes/deploy-sql-cmd.png)

   Vengono creati la distribuzione e il servizio. L'istanza di SQL Edge di Azure si trova in un contenitore connesso a una risorsa di archiviazione permanente.

   Per visualizzare lo stato del pod, digitare `kubectl get pod -n <namespace name>`.

   ![Screenshot del comando get pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Nell'immagine precedente il pod ha lo stato `Running`. Questo stato indica che il contenitore è pronto. L'operazione potrebbe richiedere alcuni minuti.

   >[!NOTE]
   >Dopo aver creato la distribuzione, la visualizzazione del pod potrebbe richiedere alcuni minuti. Il ritardo si verifica perché il cluster esegue il pull dell'immagine del contenitore di SQL Edge di Azure dall'hub Docker. Dopo il primo pull dell'immagine, le distribuzioni successive potrebbero essere più veloci se la distribuzione viene eseguita in un nodo in cui l'immagine è già memorizzata. 

3. Verificare che i servizi siano in esecuzione. Eseguire il comando seguente:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Questo comando restituisce i servizi in esecuzione, nonché gli indirizzi IP interni ed esterni per i servizi. Prendere nota dell'indirizzo IP esterno per il servizio `mssql-deployment`. Usare questo indirizzo IP per connettersi a SQL Edge di Azure. 

   ![Screenshot del comando get service](media/deploy-kubernetes/get-service-cmd.png)

   Per altre informazioni sullo stato degli oggetti nel cluster Kubernetes, eseguire:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Connettersi all'istanza di SQL Edge di Azure

Se il contenitore è stato configurato come descritto, è possibile connettersi a un'applicazione dall'esterno della rete virtuale di Azure. Usare l'account `sa` e l'indirizzo IP esterno per il servizio. Usare la password configurata come segreto di Kubernetes. Per altre informazioni sulla connessione a un'istanza di SQL Edge di Azure, vedere [Connettersi a SQL Edge di Azure](connect.md).

## <a name="verify-failure-and-recovery"></a>Verificare l'errore e il ripristino

Per verificare l'errore e il ripristino, è possibile eliminare il pod. Eseguire i passaggi seguenti:

1. Elencare il pod che esegue SQL Edge di Azure.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Prendere nota del nome del pod che esegue SQL Edge di Azure.

2. Eliminare il pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` è il valore del nome del pod restituito nel passaggio precedente. 

Kubernetes crea nuovamente il pod in modo automatico per ripristinare un'istanza di SQL Edge di Azure e connettersi alla risorsa di archiviazione permanente. Usare `kubectl get pods` per verificare che venga distribuito un nuovo pod. Usare `kubectl get services` per verificare che l'indirizzo IP del nuovo contenitore sia lo stesso. 

## <a name="summary"></a>Summary

In questa esercitazione si è appreso come distribuire contenitori di SQL Edge di Azure in un cluster di Kubernetes per la disponibilità elevata. 

> [!div class="checklist"]
> * Creare una password dell'amministratore di sistema
> * Creare una risorsa di archiviazione
> * Creare la distribuzione
> * Connettersi con Azure SQL Server Management Studio (SSMS)
> * Verificare l'errore e il ripristino

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).
- [Creazione di una soluzione IoT end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming dei dati in SQL Edge di Azure](stream-data.md)

