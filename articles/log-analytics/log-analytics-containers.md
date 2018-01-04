---
title: Soluzione Monitoraggio contenitori in Log Analytics di Azure | Microsoft Docs
description: La soluzione Monitoraggio contenitori in Log Analytics consente di visualizzare e gestire gli host del contenitore Docker e Windows in un'unica posizione.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: magoedte;banders
ms.openlocfilehash: 4087cb787e43c3d1b40ad082e84534b34918c9e9
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Soluzione Monitoraggio contenitori in Log Analytics

![Simbolo di Contenitori](./media/log-analytics-containers/containers-symbol.png)

Questo articolo descrive come configurare e usare la soluzione Monitoraggio contenitori in Log Analytics per visualizzare e gestire gli host del contenitore Docker e Windows in un'unica posizione. Docker è un sistema di virtualizzazione software usato per creare contenitori che consentono di automatizzare la distribuzione del software nell'infrastruttura IT.

La soluzione indica quali contenitori sono in esecuzione, quale immagine del contenitore eseguono e dove vengono eseguiti i contenitori. È possibile visualizzare informazioni di controllo dettagliate che indicano i comandi usati con i contenitori. È anche possibile risolvere i problemi dei contenitori visualizzando i log centralizzati ed eseguendo ricerche al loro interno senza dover visualizzare gli host Docker o Windows in remoto. È possibile trovare contenitori che consumano una quantità eccessiva di risorse in un host. È anche possibile visualizzare informazioni centralizzate su utilizzo di CPU, memoria, archiviazione e rete e sulle prestazioni dei contenitori. Nei computer che eseguono Windows, è possibile centralizzare e confrontare i log dai contenitori Windows Server, Hyper-V e Docker. La soluzione supporta gli agenti di orchestrazione dei contenitori seguenti:

- Docker Swarm
- Controller di dominio/sistema operativo
- kubernetes
- Service Fabric
- Red Hat OpenShift


Il diagramma seguente mostra le relazioni tra vari host del contenitore e agenti con OMS.

![Diagramma dei contenitori](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisiti di sistema e piattaforme supportate

Prima di iniziare, esaminare i dettagli seguenti per verificare che i prerequisiti siano soddisfatti.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Supporto della soluzione di monitoraggio del contenitore per l'orchestrazione di Docker e la piattaforma del sistema operativo
La tabella seguente descrive il supporto del monitoraggio dell'orchestrazione di Docker e del sistema operativo per inventario, prestazioni e log del contenitore con Log Analytics.   

| | ACS | Linux | Windows | Contenitore<br>Inventario | Image<br>Inventario | Nodo<br>Inventario | Contenitore<br>Prestazioni | Contenitore<br>Event | Event<br>Log | Contenitore<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>Controller di dominio/sistema operativo | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Service<br>Infrastruttura | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>MAIUSC | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autonomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Server Linux<br>(autonomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Versioni di Docker supportate in Linux

- Docker da 1.11 a 1.13
- Docker CE e EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Distribuzioni Linux x64 supportate come host del contenitore

- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS (stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 e 3.5
- ACS Mesosphere DC/OS da 1.7.3 a 1.8.8
- ACS Kubernetes da 1.4.5 a 1.6
    - Gli eventi Kubernetes, l'inventario di Kubernetes e i processi contenitore sono supportati solo con la versione 1.4.1-45 e successive dell'agente di OMS per Linux
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows supportato

- Windows Server 2016
- Versione di Windows per il 10° anniversario (professionale o aziendale)

### <a name="docker-versions-supported-on-windows"></a>Versioni di Docker supportate in Windows

- Docker 1.12 e 1.13
- Docker 17.03.0 e successive

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

1. Aggiungere la soluzione Monitoraggio contenitori all'area di lavoro OMS da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).

2. Installare e usare Docker con un agente OMS. In base al sistema operativo e all'agente di orchestrazione Docker, è possibile usare i metodi seguenti per configurare l'agente.
  - Per gli host autonomi:
    - Nei sistemi operativi Linux supportati installare ed eseguire Docker, quindi installare e configurare l'[agente OMS per Linux](log-analytics-agent-linux.md).  
    - Non è possibile eseguire l'agente OMS per Linux in CoreOS. È invece necessario eseguire una versione di tale agente inserita in contenitori. Vedere la sezione [Per tutti gli host del contenitore Linux inclusi CoreOS](#for-all-linux-container-hosts-including-coreos) o [Per tutti gli host del contenitore Linux Azure per enti pubblici incluso CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) se si usano contenitori nel cloud di Azure per enti pubblici.
    - In Windows Server 2016 e Windows 10, installare il motore e il client Docker, quindi connettere un agente per raccogliere informazioni da inviare a Log Analytics. Se si dispone di un ambiente Windows, consultare [Install and configure Windows container hosts](#install-and-configure-windows-container-hosts) (installare e configurare gli host contenitore di Windows).
  - Per l'orchestrazione di multi-host Docker:
    - Se si opera in un ambiente Red Hat OpenShift, vedere [Configurare un agente OMS per Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Se si dispone di un cluster Kubernetes tramite il servizio contenitore di Azure, consultare [Configure an OMS agent for Kubernetes](#configure-an-oms-agent-for-kubernetes) (Configurare un agente OMS per Kubernetes).
    - Se si ha un cluster DC/OS del servizio contenitore di Azure, vedere [Monitorare un cluster DC/OS del servizio contenitore di Azure con Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Se è presente un ambiente in modalità Docker Swarm, per altre informazioni vedere [Configurare un agente OMS per Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Se si usano contenitori con Service Fabric, vedere [Panoramica di Azure Service Fabric](../service-fabric/service-fabric-overview.md).

Consultare l'articolo sul [motore Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) per altre informazioni su come installare e configurare i motori di Docker sui computer che eseguono Windows.

> [!IMPORTANT]
> Docker deve essere in esecuzione **prima** di installare l'[agente OMS per Linux](log-analytics-agent-linux.md) negli host di contenitori. Se l'agente era già stato installato prima di installare Docker, è necessario reinstallare l'agente di OMS per Linux. Per altre informazioni su Docker, vedere il [sito Web di Docker](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Installare e configurare gli host del contenitore Linux

Dopo aver installato Docker, usare le impostazioni seguenti per l'host di contenitori per configurare l'agente per l'uso con Docker. Saranno necessari l'ID e la chiave dell'area di lavoro OMS, che è possibile identificare nel portale di Azure. Nell'area di lavoro fare clic su **Avvio rapido** > **Computer** per visualizzare **ID area di lavoro** e **Chiave primaria**.  Copiare e incollare entrambi i valori nell'editor predefinito.

**Per tutti gli host del contenitore Linux, ad eccezione di CoreOS:**

- Per altre informazioni e procedure su come installare l'agente OMS per Linux, vedere [Connettere i computer Linux a Operations Management Suite (OMS)](log-analytics-agent-linux.md).

**Per tutti gli host del contenitore Linux, incluso CoreOS:**

Avviare il contenitore OMS da monitorare. Modificare e usare l'esempio seguente:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Per tutti gli host del contenitore Linux Azure per enti pubblici, incluso CoreOS:**

Avviare il contenitore OMS da monitorare. Modificare e usare l'esempio seguente:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Passaggio dall'uso di un agente Linux installato a un agente in un contenitore**

Se in precedenza veniva usato l'agente installato direttamente e si vuole usare invece un agente in esecuzione in un contenitore, prima è necessario rimuovere l'agente OMS per Linux. Vedere [Disinstallazione dell'agente OMS per Linux](log-analytics-agent-linux.md) per comprendere come disinstallare correttamente l'agente.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Configurare un agente OMS per Docker Swarm

È possibile eseguire l'agente OMS come servizio globale in Docker Swarm. Usare le informazioni seguenti per creare un servizio agente OMS. È necessario inserire l'ID e la chiave primaria dell'area di lavoro OMS.

- Eseguire quanto segue sul nodo principale.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Proteggere i segreti per Docker Swarm

Per Docker Swarm, una volta creato il segreto per l'ID area di lavoro e la chiave primaria, usare le informazioni seguenti per creare i segreti.

1. Eseguire quanto segue sul nodo principale.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Verificare che i segreti siano stati creati correttamente.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Eseguire il comando seguente per impostare i segreti per l'agente OMS nel contenitore.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Configurare un agente OMS per Red Hat OpenShift
Esistono tre modi per aggiungere l'agente OMS a Red Hat OpenShift e avviare la raccolta dei dati di monitoraggio del contenitore.

* [Installare l'agente OMS per Linux](log-analytics-agent-linux.md) direttamente in ogni nodo OpenShift  
* [Abilitare l'estensione della macchina virtuale di Log Analytics](log-analytics-azure-vm-extension.md) in ogni nodo OpenShift che risiede in Azure  
* Installare l'agente OMS come DaemonSet OpenShift  

In questa sezione viene illustrata la procedura necessaria per installare l'agente OMS come DaemonSet OpenShift.  

1. Accedere al nodo principale OpenShift e copiare il file yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) da GitHub nel nodo principale e modificare il valore con l'ID dell'area di lavoro OMS e la chiave primaria.
2. Eseguire i comandi seguenti per creare un progetto per OMS e configurare l'account utente.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Per distribuire DaemonSet, eseguire il comando seguente:

    `oc create -f ocp-omsagent.yaml`

5. Per verificare che sia configurato e funzioni correttamente, digitare quanto segue:

    `oc describe daemonset omsagent`  

    L'output deve essere simile a:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Se si vuole usare i segreti per proteggere l'ID e la chiave primaria dell'area di lavoro OMS quando si usa il file yaml DaemonSet dell'agente OMS, seguire questa procedura.

1. Accedere al nodo principale OpenShift e copiare il file yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e il segreto che genera lo script [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) da GitHub.  Questo script genererà il file yaml dei segreti per l'ID e la chiave primaria dell'area di lavoro OMS per proteggere le informazioni segrete.  
2. Eseguire i comandi seguenti per creare un progetto per OMS e configurare l'account utente. Il segreto che genera lo script chiede di specificare l'ID <WSID> e la chiave primaria <KEY> dell'area di lavoro OMS e, al completamento, crea il file ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Distribuire il file del segreto eseguendo il comando seguente:

    `oc create -f ocp-secret.yaml`

5. Verificare la distribuzione eseguendo il comando seguente:

    `oc describe secret omsagent-secret`  

    L'output deve essere simile a:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Distribuire il file yaml DaemonSet dell'agente OMS eseguendo il comando seguente:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Verificare la distribuzione eseguendo il comando seguente:

    `oc describe ds oms`

    L'output deve essere simile a:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Configurare un agente OMS Linux per Kubernetes

Per Kubernetes è possibile usare uno script per generare il file yaml dei segreti per l'ID area di lavoro e la chiave primaria per installare l'agente OMS per Linux. Nella pagina [OMS Docker/Kubernetes di GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) sono disponibili file usabili con o senza informazioni segrete.

- Il file DaemonSet predefinito dell'agente OMS per Linux non include informazioni segrete (omsagent.yaml)
- Il file yaml DaemonSet dell'agente OMS per Linux usa le informazioni segrete (omsagent-ds-secrets.yaml) con script per la generazione di segreti per generare il file yaml dei segreti (omsagentsecret.yaml).

È possibile scegliere di creare DaemonSet dell'agente OMS con o senza segreti.

**File DaemonSet con estensione yaml predefinito dell'agente OMS senza segreti**

- Per il file DaemonSet con estensione yaml predefinito dell'agente OMS, sostituire `<WSID>` e `<KEY>` a WSID e KEY. Copiare il file nel nodo principale ed eseguire le operazioni seguenti:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**File DaemonSet con estensione yaml predefinito dell'agente OMS con segreti**

1. Per usare il DaemonSet dell'agente OMS con informazioni segrete, in primo luogo creare i segreti.
    1. Copiare lo script e il file modello dei segreti e assicurarsi che siano nella stessa directory.
        - Script per la generazione di segreti: secret-gen.sh
        - Modello di segreto: secret-template.yaml
    2. Eseguire lo script come nell'esempio seguente. Lo script richiede l'ID e la chiave primaria dell'area di lavoro OMS. Dopo aver specificato queste credenziali, lo script crea un file yaml dei segreti che può essere eseguito.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Creare il pod dei segreti eseguendo le operazioni seguenti:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Per la verifica eseguire le operazioni seguenti:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        L'output deve essere simile a:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        L'output deve essere simile a:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Verificare che il DaemonSet dell'agente OMS sia in esecuzione, con un output simile al seguente:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Per Kubernetes usare uno script per generare il file dei segreti con estensione yaml per l'ID area di lavoro e la chiave primaria per l'agente OMS per Linux. Usare le informazioni di esempio seguenti con il [file yaml dell'agente OMS](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) per proteggere le informazioni segrete.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-an-oms-agent-for-windows-kubernetes"></a>Configurare un agente OMS per Kubernetes Windows
Per Kubernetes Windows è possibile usare uno script per generare il file yaml dei segreti per l'ID area di lavoro e la chiave primaria per installare l'agente OMS. Nella pagina [OMS Docker/Kubernetes di GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) sono disponibili file usabili con informazioni segrete.  È necessario installare l'agente OMS separatamente per i nodi master e agente.  

1. Per usare il DaemonSet dell'agente OMS con informazioni segrete nel nodo master, in primo luogo eseguire l'accesso e creare i segreti.
    1. Copiare lo script e il file modello dei segreti e assicurarsi che siano nella stessa directory.
        - Script per la generazione di segreti: secret-gen.sh
        - Modello di segreto: secret-template.yaml

    2. Eseguire lo script come nell'esempio seguente. Lo script richiede l'ID e la chiave primaria dell'area di lavoro OMS. Dopo aver specificato queste credenziali, lo script crea un file yaml dei segreti che può essere eseguito.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ``` kubectl create -f omsagentsecret.yaml ```
    4. Per verificare, eseguire quanto segue:
    
        ``` 
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        L'output deve essere simile a:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>
    
        Type:   Opaque
    
        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes 
        ```

    5. Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Verificare che il DaemonSet dell'agente OMS sia in esecuzione, con un output simile al seguente:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Per installare l'agente nel nodo del lavoro, che esegue Windows, seguire i passaggi della sezione [Installare e configurare gli host del contenitore Windows](#install-and-configure-windows-container-hosts). 

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Usare Helm per distribuire l'agente OMS in Kubernetes Linux 
Per usare Helm al fine di distribuire l'agente OMS in ambiente Kubernetes Linux, eseguire la procedura seguente.

1. Creare il DaemonSet dell'agente OMS eseguendo l'istruzione ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. I risultati saranno simili ai seguenti:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. È possibile controllare lo stato dell'agente OSM eseguendo: ```helm status "omsagent"``` e l'output sarà simile al seguente:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Per altre informazioni, visitare il [grafico Helm della soluzione contenitore](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installare e configurare gli host del contenitore Windows

Usare le informazioni contenute nella sezione Installare e configurare gli host del contenitore Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparazione prima dell'installazione degli agenti di Windows

Prima di installare gli agenti nei computer che eseguono Windows, è necessario configurare il servizio Docker. La configurazione consente all'agente di Windows o all'estensione macchina virtuale Log Analytics di usare il socket TCP di Docker in modo che gli agenti possano accedere in remoto al daemon Docker e acquisire i dati per il monitoraggio.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Per avviare Docker e verificare la configurazione

Per configurare pipe TCP e named pipe per Windows Server, procedere come segue:

1. In Windows PowerShell, abilitare pipe TCP e named pipe.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Configurare Docker con il file di configurazione per pipe TCP e named pipe. Il file di configurazione è disponibile in C:\ProgramData\docker\config\daemon.json.

    Nel file daemon.json, è necessario specificare quanto segue:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Per altre informazioni sulla configurazione del daemon Docker usata con contenitori Windows, vedere [Motore Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Installare gli agenti Windows

Per abilitare il monitoraggio dei contenitori Windows e Hyper-V, installare Microsoft Monitoring Agent (MMA) nei computer Windows che sono host del contenitore. Per i computer che eseguono Windows nell'ambiente locale, vedere [Collegare i computer di Windows a Log Analytics](log-analytics-windows-agent.md). Per le macchine virtuali eseguite in Azure, collegarle a Log Analytics usando l'[estensione macchina virtuale](log-analytics-azure-vm-extension.md).

È possibile monitorare i contenitori Windows in esecuzione in Service Fabric. Solo le [macchine virtuali in esecuzione in Azure](log-analytics-azure-vm-extension.md) e i [computer che eseguono Windows nell'ambiente locale](log-analytics-windows-agent.md), tuttavia, sono attualmente supportati da Service Fabric.

È possibile verificare che la soluzione Monitoraggio contenitori sia impostata correttamente per Windows. Per verificare che il Management Pack sia stato scaricato correttamente, cercare *ContainerManagement.xxx*. Il file dovrebbe trovarsi nella cartella C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.


## <a name="solution-components"></a>Componenti della soluzione

Se si usano agenti Windows, il Management Pack seguente viene installato in ogni computer con un agente quando si aggiunge questa soluzione. Per il Management Pack non è richiesta alcuna configurazione o manutenzione.

- *ContainerManagement.xxx* installato in C:\Programmi\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori
La soluzione Monitoraggio contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host del contenitore e contenitori usando gli agenti abilitati.

I dati vengono raccolti ogni tre minuti dai tipi di agenti seguenti.

- [Agente OMS per Linux](log-analytics-linux-agents.md)
- [Agente Windows](log-analytics-windows-agent.md)
- [Estensione macchina virtuale di Log Analytics](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti dalla soluzione Monitoraggio contenitori e i tipi di dati visualizzati nei risultati della ricerca log.

| Tipo di dati | Tipo di dati in Ricerca log | Fields |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Type=Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `Type=ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | `Type=ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | `Type=ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | `Type=ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `Type=ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventario di Kubernetes | `Type=KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo contenitore | `Type=ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventi di Kubernetes | `Type=KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Le etichette aggiunte ai tipi di dati *PodLabel* sono etichette personalizzate. Le etichette PodLabel aggiunte indicate nella tabella sono esempi. `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` saranno quindi diverse nel set di dati dell'ambiente e in genere saranno simili a `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Monitorare i contenitori
Dopo avere abilitato la soluzione nel portale di OMS, il riquadro **Contenitori** mostra le informazioni di riepilogo sugli host di contenitori e i contenitori in esecuzione negli host.

![Riquadro Containers (Contenitori)](./media/log-analytics-containers/containers-title.png)

Il riquadro visualizza una panoramica del numero di contenitori nell'ambiente e indica se i contenitori presentano errori, sono in esecuzione oppure sono stati arrestati.

### <a name="using-the-containers-dashboard"></a>Uso del dashboard Containers (Contenitori)
Fare clic sul riquadro **Containers** (Contenitori). Le visualizzazioni sono organizzate in base agli elementi seguenti:

- **Eventi del contenitore**: visualizza lo stato dei contenitori e i computer con contenitori non riusciti.
- **Log contenitori**: visualizza un grafico dei file di log dei contenitori generati nel corso del tempo e un elenco di computer con il numero più elevato di file di log.
- **Eventi Kubernetes**: visualizza un grafico degli eventi di Kubernetes generati nel corso del tempo e un elenco dei motivi per cui i pod hanno generato gli eventi. *Questo set di dati viene usato solo negli ambienti Linux.*
- **Inventario degli spazi dei nomi Kubernetes**: visualizza il numero di spazi dei nomi e di pod e la gerarchia. *Questo set di dati viene usato solo negli ambienti Linux.*
- **Inventario nodi del contenitore**: visualizza il numero di tipi di orchestrazioni usati nei nodi/host del contenitore. I nodi/host del computer vengono anche indicati dal numero di contenitori. *Questo set di dati viene usato solo negli ambienti Linux.*
- **Inventario immagini contenitore**: visualizza il numero totale di immagini del contenitore usate e il numero di tipi di immagini. Il numero delle immagini è indicato anche dal tag immagine.
- **Stato dei contenitori**: visualizza il numero totale di nodi contenitore/computer host con contenitori in esecuzione. I computer vengono anche indicati dal numero di host in esecuzione.
- **Processi del contenitore**: visualizza un grafico a linee dei processi del contenitore in esecuzione nel corso del tempo. I contenitori vengono anche indicati dal comando/processo in esecuzione nei contenitori. *Questo set di dati viene usato solo negli ambienti Linux.*
- **Prestazioni CPU contenitore**: visualizza un grafico a linee dell'utilizzo medio della CPU nel corso del tempo per i nodi/host del computer. Elenca anche i nodi/host del computer in base all'utilizzo medio della CPU.
- **Prestazioni memoria contenitore**: visualizza un grafico a linee dell'utilizzo della memoria nel corso del tempo. Elenca anche l'utilizzo della memoria del computer in base al nome dell'istanza.
- **Prestazioni computer**: visualizza i grafici a linee di percentuale di prestazioni della CPU nel corso del tempo, percentuale di utilizzo della memoria nel corso del tempo e megabyte di spazio su disco nel corso del tempo. È possibile passare il puntatore su una linea di un grafico per visualizzare altri dettagli.


Ogni area del dashboard è una rappresentazione visiva di una ricerca eseguita sui dati raccolti.

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash01.png)

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash02.png)

Nel pannello **Stato del contenitore** fare clic sull'area superiore come illustrato di seguito.

![Stato dei contenitori](./media/log-analytics-containers/containers-status.png)

Si aprirà Ricerca log con informazioni sugli host e sullo stato del contenitore.

![Ricerca log per i contenitori](./media/log-analytics-containers/containers-log-search.png)

A questo punto è possibile modificare la query di ricerca per trovare specifiche informazioni di interesse. Per altre informazioni sulle ricerche log, vedere [Ricerche nei log in Log Analytics](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Risolvere i problemi cercando un contenitore con errori

Log Analytics contrassegna un contenitore come **Non riuscito** se il contenitore è stato terminato con un codice di uscita diverso da zero. È possibile visualizzare una panoramica degli errori nell'ambiente nell'area **Contenitori non riusciti**.

### <a name="to-find-failed-containers"></a>Per trovare i contenitori non riusciti
1. Fare clic sull'area **Stato del contenitore**.  
   ![Stato dei contenitori](./media/log-analytics-containers/containers-status.png)
2. Si aprirà Ricerca log e verrà visualizzato lo stato dei contenitori. Vedere l'esempio seguente.  
   ![stato dei contenitori](./media/log-analytics-containers/containers-log-search.png)
3. Fare quindi clic sul valore aggregato dei contenitori non riusciti per visualizzare altre informazioni. Espandere **mostra dettagli** per visualizzare l'ID immagine.  
   ![contenitori non riusciti](./media/log-analytics-containers/containers-state-failed.png)  
4. Digitare quindi il codice seguente nella query di ricerca. `Type=ContainerInventory <ImageID>` visualizza i dettagli dell'immagine, ad esempio dimensioni e numero di immagini arrestate e non riuscite.  
   ![contenitori non riusciti](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Ricerca dei dati dei contenitori nei log
Nella risoluzione di un errore specifico può essere utile vedere dove l'errore si verifica nell'ambiente. I tipi di log seguenti consentono di creare query per ottenere le informazioni necessarie.


- **ContainerImageInventory**: usare questo tipo per trovare informazioni organizzate per immagine e visualizzare le informazioni sulle immagini, ad esempio ID o dimensioni.
- **ContainerInventory**: usare questo tipo per ottenere informazioni sul percorso dei contenitori, i relativi nomi e le immagini che eseguono.
- **ContainerLog**: usare questo tipo per trovare informazioni e voci specifiche del registro errori.
- **ContainerNodeInventory_CL** Usare questo tipo quando sono necessarie le informazioni sull'host o sul nodo in cui si trovano i contenitori. Fornisce informazioni su versione di Docker, tipo di orchestrazione, risorsa di archiviazione e rete.
- **ContainerProcess_CL** Usare questo tipo per visualizzare velocemente il processo in esecuzione nel contenitore.
- **ContainerServiceLog**: usare questo tipo per trovare informazioni di audit trail per il daemon Docker, ad esempio comandi di avvio, arresto, eliminazione o pull.
- **KubeEvents_CL** Usare questo tipo per visualizzare gli eventi di Kubernetes.
- **KubePodInventory_CL** Usare questo tipo quando sono necessarie le informazioni sulla gerarchia dei cluster.


### <a name="to-search-logs-for-container-data"></a>Per cercare i dati dei contenitori nei log
* Scegliere un'immagine non riuscita di recente e trovare i relativi registri degli errori. Iniziare cercando il nome di un contenitore che esegue l'immagine con una ricerca **ContainerInventory**. Cercare ad esempio `Type=ContainerInventory ubuntu Failed`  
    ![Cercare contenitori Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Prendere nota del nome del contenitore accanto a **Name** e cercare questi log. In questo esempio si tratta di `Type=ContainerLog cranky_stonebreaker`.

**Visualizzare le informazioni sulle prestazioni**

Quando si inizia a creare query, può essere utile comprendere prima le operazioni possibili. Per visualizzare ad esempio tutti i dati sulle prestazioni, provare con una query generica digitando la query di ricerca seguente.

```
Type=Perf
```

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf01.png)

È possibile limitare i dati sulle prestazioni visualizzati a un contenitore specifico digitando il relativo nome a destra della query.

```
Type=Perf <containerName>
```

Verrà visualizzato l'elenco delle metriche delle prestazioni raccolte per un singolo contenitore.

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Esempio di query di ricerca log
Spesso è utile compilare query iniziando con qualche esempio da modificare in funzione dell'ambiente. Come punto di partenza è possibile provare a usare l'area **Query di esempio** per compilare query più avanzate.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Query sui contenitori](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Salvataggio delle query di ricerca log
Il salvataggio di query è una funzionalità standard di Log Analytics. Le query salvate potranno essere riusate rapidamente in futuro.

Dopo aver creato una query che si ritiene utile, salvarla facendo clic su **Preferiti** nella parte superiore della pagina Ricerca log. Sarà possibile accedere facilmente alla query in seguito dalla pagina **Dashboard**.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare i record di dati dettagliati per i contenitori.
