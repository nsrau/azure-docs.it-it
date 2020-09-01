---
title: Distribuire l'app Guestbook PHP in Arc abilitato Kubernetes sul dispositivo GPU Azure Stack Edge | Microsoft Docs
description: Viene descritto come distribuire un'applicazione PHP Guestbook senza stato con Redis usando GitOps in un cluster Kubernetes con Arc abilitato del dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 83ac012c861a0d066bdc47d8e15cbe7ac398aa23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254220"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-gpu"></a>Distribuire un'applicazione PHP Guestbook senza stato con Redis on Arc il cluster Kubernetes abilitato sulla GPU Azure Stack Edge

Questo articolo illustra come creare e distribuire una semplice applicazione Web multilivello con Kubernetes e Azure Arc. Questo esempio è costituito dai componenti seguenti:

- Un Master Redis a istanza singola per archiviare le voci del Guestbook
- Più istanze di redis replicate per gestire le letture
- Più istanze front-end Web

La distribuzione viene eseguita usando GitOps nel cluster Kubernetes con Arc abilitato nel dispositivo Azure Stack Edge. 

Questa procedura è destinata agli utenti che hanno esaminato i [carichi di lavoro Kubernetes nel dispositivo Azure stack Edge](azure-stack-edge-gpu-kubernetes-workload-management.md) e hanno familiarità con i concetti di [Azure Arc Enabled Kubernetes (anteprima)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).


## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'applicazione senza stato, verificare di aver completato i prerequisiti seguenti nel dispositivo e il client che si userà per accedere al dispositivo:

### <a name="for-device"></a>Per il dispositivo

1. Sono disponibili credenziali di accesso a un dispositivo Azure Stack Edge a 1 nodo.
    1. Il dispositivo è attivato. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. Il dispositivo ha il ruolo di calcolo configurato tramite portale di Azure ed è dotato di un cluster Kubernetes. Vedere [configurare](azure-stack-edge-gpu-deploy-configure-compute.md)le risorse di calcolo.

1. Azure Arc è stato abilitato nel cluster Kubernetes esistente nel dispositivo ed è presente una risorsa di Azure Arc corrispondente nella portale di Azure. Per i passaggi dettagliati, vedere [abilitare Azure Arc sul dispositivo Azure stack Edge](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

1. Si dispone di un sistema client Windows che verrà usato per accedere al dispositivo Azure Stack Edge.
  
    - Il client esegue Windows PowerShell 5,0 o versione successiva. Per scaricare la versione più recente di Windows PowerShell, vedere [installare Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Questo articolo descrive la procedura quando si usa un client Windows. 
    
1. È stata completata la procedura descritta in [accedere al cluster Kubernetes nel dispositivo Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). Precisamente:
    
    - Installato `kubectl` nel client  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Verificare che la versione del `kubectl` client non sia più di una versione della versione master di Kubernetes in esecuzione nel dispositivo Azure stack Edge. 
      - Usare `kubectl version` per verificare la versione di kubectl in esecuzione sul client. Prendere nota della versione completa.
      - Nell'interfaccia utente locale del dispositivo Azure Stack Edge passare a **Panoramica** e prendere nota del numero di software Kubernetes. 
      - Verificare che queste due versioni siano compatibili con il mapping fornito nella versione supportata di Kubernetes <!--insert link-->.

1. Si dispone [di una configurazione GitOps che è possibile usare per eseguire una distribuzione di Azure Arc](https://github.com/kagoyal/dbehaikudemo). `yaml`Per la distribuzione nel dispositivo Azure stack Edge si useranno i file seguenti.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Distribuire la configurazione

Seguire questa procedura per configurare la risorsa di Azure Arc per distribuire una configurazione di GitOps tramite la portale di Azure: 

1. Nel portale di Azure passare alla risorsa Azure Arc creata quando è stato abilitato Azure Arc sul cluster Kubernetes nel dispositivo. 

    ![Vai alla risorsa Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Passare a **configurazioni** e selezionare **+ Aggiungi configurazione**.

    ![Vai a configurazioni](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. In **Aggiungi configurazione**immettere i valori appropriati per i campi e selezionare **applica**.

    |Parametro  |Description |
    |---------|---------|
    |Nome configurazione     | Nome della risorsa di configurazione.        |
    |Nome dell'istanza dell'operatore     |Nome dell'istanza dell'operatore per identificare una configurazione specifica. Il nome è una stringa costituita da un massimo di 253 caratteri che devono essere di solo minuscolo, alfanumerico, trattino e punto.         |
    |Spazio dei nomi operator     | Impostare su **demotestguestbook** in modo che corrisponda allo spazio dei nomi specificato nella distribuzione `yaml` . <br> Il campo definisce lo spazio dei nomi in cui è installato l'operatore. Il nome è una stringa costituita da un massimo di 253 caratteri che devono essere di solo minuscolo, alfanumerico, trattino e punto.         |
    |URL del repository     |<br>Percorso del repository git in `http://github.com/username/repo` o nel `git://github.com/username/repo` formato in cui si trova la configurazione di GitOps.         |
    |Ambito operatore     | Selezionare **spazio dei nomi**. <br>Definisce l'ambito in cui è installato l'operatore. Selezionare come spazio dei nomi. L'operatore verrà installato nello spazio dei nomi specificato nei file YAML della distribuzione.       |
    |Tipo di operatore     | Lasciare il valore predefinito. <br>Specifica il tipo di operatore, per impostazione predefinita, impostato come Flux.        |
    |Operatore params     | Lasciarlo vuoto. <br>Questo campo contiene i parametri da passare all'operatore Flux.        |
    |Helm     | Impostare questa impostazione su **disabilitato**. <br>Abilitare questa opzione se si eseguiranno distribuzioni basate su grafico.        |


    ![Aggiungere la configurazione](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Viene avviata la distribuzione della configurazione e lo **stato dell'operatore** viene visualizzato come **in sospeso**. 

    ![Vai a configurazioni](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. La distribuzione richiede alcuni minuti. Al termine della distribuzione, lo **stato dell'operatore** indica **installato**.

    ![Vai a configurazioni](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>Verificare la distribuzione

La distribuzione tramite la configurazione GitOps crea uno `demotestguestbook` spazio dei nomi come specificato nei file di distribuzione che `yaml` si trovano nel repository git.

1. Dopo aver applicato la configurazione di GitOps, [connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Eseguire il comando seguente per elencare i pod in esecuzione nello `demotestguestbook` spazio dei nomi corrispondente alla distribuzione.

    `kubectl get pods -n <your-namespace>`
  
    Di seguito è riportato un esempio di output.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. In questo esempio il servizio front-end è stato distribuito come tipo: LoadBalancer. Per visualizzare il Guestbook, sarà necessario trovare l'indirizzo IP del servizio. Eseguire il seguente comando.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Il servizio front-end di `type:LoadBalancer` ha un indirizzo IP esterno. Questo IP è compreso nell'intervallo di indirizzi IP specificato per i servizi esterni quando si configurano le impostazioni di rete di calcolo nel dispositivo. Usare questo indirizzo IP per visualizzare il Guestbook all'URL: `https://<external-IP-address>` .

    ![Visualizza guestbook](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Elimina distribuzione

Per eliminare la distribuzione, è possibile eliminare la configurazione dalla portale di Azure. Questa operazione eliminerà gli oggetti creati, inclusi i servizi e le distribuzioni.

1. Nel portale di Azure passare alla risorsa Azure Arc > configurazioni. 
1. Individuare la configurazione che si desidera eliminare. Selezionare il... per richiamare il menu di scelta rapida e selezionare **Elimina**.
    ![Elimina configurazione](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Potrebbero essere necessari alcuni minuti per eliminare la configurazione.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare il dashboard di Kubernetes per monitorare le distribuzioni nel dispositivo Azure stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
