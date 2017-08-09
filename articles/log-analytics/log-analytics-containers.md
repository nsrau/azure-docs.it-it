---
title: Soluzione Contenitori in Log Analytics di Azure | Microsoft Docs
description: La soluzione Contenitori in Log Analytics consente di visualizzare e gestire gli host del contenitore Docker e Windows in un'unica posizione.
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
ms.date: 08/01/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 524c63d358ce22c10b7a23e5bcf0b33e9f2e5f26
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---
# <a name="containers-preview-solution-in-log-analytics"></a>Soluzione Contenitori (anteprima) in Log Analytics

![Simbolo di Contenitori](./media/log-analytics-containers/containers-symbol.png)

Questo articolo descrive come configurare e usare la soluzione Contenitori in Log Analytics per visualizzare e gestire gli host del contenitore Docker e Windows in un'unica posizione. Docker è un sistema di virtualizzazione software usato per creare contenitori che consentono di automatizzare la distribuzione del software nell'infrastruttura IT.

Con la soluzione è possibile visualizzare i contenitori in esecuzione negli host di contenitori e le immagini in esecuzione nei contenitori. È possibile visualizzare informazioni di controllo dettagliate che indicano i comandi usati con i contenitori. È anche possibile risolvere i problemi dei contenitori visualizzando i log centralizzati ed eseguendo ricerche al loro interno senza dover visualizzare gli host Docker o Windows in remoto. È possibile trovare contenitori che consumano una quantità eccessiva di risorse in un host. È anche possibile visualizzare informazioni centralizzate su utilizzo di CPU, memoria, archiviazione e rete e sulle prestazioni dei contenitori. Nei computer che eseguono Windows, è possibile centralizzare e confrontare i log dai contenitori Windows Server, Hyper-V e Docker.

Il diagramma seguente mostra le relazioni tra vari host del contenitore e agenti con OMS.

![Diagramma dei contenitori](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements"></a>Requisiti di sistema
Prima di iniziare, esaminare i dettagli seguenti per verificare che i prerequisiti siano soddisfatti.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Supporto della soluzione di monitoraggio del contenitore per l'orchestrazione di Docker e la piattaforma del sistema operativo 
La tabella seguente descrive il supporto del monitoraggio dell'orchestrazione di Docker e del sistema operativo per inventario, prestazioni e log del contenitore con Log Analytics.   

| | ACS | Linux | Windows | Contenitore<br>Inventario | Image<br>Inventario | Nodo<br>Inventario | Contenitore<br>Prestazioni | Contenitore<br>Evento | Evento<br>Log | Contenitore<br>Log | 
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| kubernetes | Sì | Sì | | Sì | Sì | Sì | Sì | Sì | Sì | Sì | 
| Mesosphere<br>Controller di dominio/sistema operativo | Sì | Sì | | Sì | Sì | Sì | Sì| Sì | Sì | Sì | 
| Docker<br>Swarm | Sì | Sì | Sì | Sì | Sì | Sì | Sì | Sì | | Sì |
| Service<br>Infrastruttura | | | Sì | Sì | Sì | Sì | Sì | Sì | Sì | Sì | 
| Red Hat Open<br>MAIUSC | | Sì | | Sì | Sì| Sì | Sì | Sì | | Sì | 
| Windows Server<br>(autonomo) | | | Sì | Sì | Sì | Sì | Sì | Sì | | Sì |
| Server Linux<br>(autonomo) | | Sì | | Sì | Sì | Sì | Sì | Sì | | Sì |


### <a name="supported-linux-operating-system"></a>Sistema operativo Linux supportato

- Docker (da 1.11 a 1.13)
- Docker CE e EE v17.03

Le distribuzioni Linux x64 seguenti sono supportate come host del contenitore:

- Ubuntu 14.04 LTS, 16.04 LTS
- CoreOS (stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2, 7.3
- SLES 12
- RHEL 7.2, 7.3

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows supportato

- Windows Server 2016
- Versione di Windows per il 10° anniversario (professionale o aziendale)

### <a name="docker-versions-supported-on-windows"></a>Versioni di Docker supportate in Windows

- Docker 1.12 – 1.13
- Docker 17.03.0 

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

Aggiungere la soluzione Contenitori all'atra di lavoro OMS da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).

Esistono alcuni metodi per installare e usare Docker con OMS:

* Nei sistemi operativi Linux supportati installare ed eseguire Docker, quindi installare e configurare l'agente OMS per Linux.
* Non è possibile eseguire l'agente OMS per Linux in CoreOS. È invece necessario eseguire una versione di tale agente inserita in contenitori.
* In Windows Server 2016 e Windows 10, installare il motore e il client Docker, quindi connettere un agente per raccogliere informazioni da inviare a Log Analytics.


È possibile esaminare le versioni di sistemi operativi Docker e Linux supportate per l'host del contenitore in [GitHub](https://github.com/Microsoft/OMS-docker).

### <a name="container-services"></a>Servizi contenitore

- Se si ha un cluster Kubernetes che usa il servizio contenitore di Azure, vedere [Monitorare un cluster del servizio contenitore di Azure con Microsoft Operations Management Suite (OMS)](../container-service/kubernetes/container-service-kubernetes-oms.md).
- Se si ha un cluster DC/OS del servizio contenitore di Azure, vedere [Monitorare un cluster DC/OS del servizio contenitore di Azure con Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
- Se è presente un ambiente in modalità Docker Swarm, per altre informazioni vedere [Configurare un agente OMS per Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
- Se si usano contenitori con Service Fabric, informazioni dettagliate sono disponibili in [Panoramica di Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Consultare l'articolo sul [motore Docker in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) per altre informazioni su come installare e configurare i motori di Docker sui computer che eseguono Windows.

> [!IMPORTANT]
> Docker deve essere in esecuzione **prima** di installare l'[agente OMS per Linux](log-analytics-agent-linux.md) negli host di contenitori. Se l'agente è stato installato prima di installare Docker, è necessario reinstallare l'agente di OMS per Linux. Per altre informazioni su Docker, vedere il [sito Web di Docker](https://www.docker.com).
>
>

Sono necessarie le seguenti impostazioni negli host di contenitori prima di poter monitorare i contenitori.

## <a name="linux-container-hosts"></a>Host del contenitore Linux

Dopo aver installato Docker, usare le impostazioni seguenti per l'host di contenitori per configurare l'agente per l'uso con Docker. Saranno necessari l'[ID area di lavoro e la chiave di OMS](log-analytics-agent-linux.md).


### <a name="for-all-linux-container-hosts-except-coreos"></a>Per tutti gli host del contenitore Linux, ad eccezione di CoreOS

- Per altre informazioni e procedure su come installare l'agente OMS per Linux, vedere [Connettere i computer Linux a Operations Management Suite (OMS)](log-analytics-agent-linux.md).

### <a name="for-all-linux-container-hosts-including-coreos"></a>Per tutti gli host del contenitore Linux inclusi CoreOS

Avviare il contenitore OMS da monitorare. Modificare usando l'esempio seguente.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="for-all-azure-government-linux-container-hosts-including-coreos"></a>Per tutti gli host del contenitore Linux Azure per enti pubblici incluso CoreOS

Avviare il contenitore OMS da monitorare. Modificare usando l'esempio seguente.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```


### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>Passaggio dall'uso di un agente Linux installato a un agente in un contenitore
Se in precedenza veniva usato l'agente installato direttamente e si vuole usare invece un agente in esecuzione in un contenitore, prima è necessario rimuovere l'agente OMS per Linux. Vedere [Disinstallazione dell'agente OMS per Linux](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux).

### <a name="configure-an-oms-agent-for-docker-swarm"></a>Configurare un agente OMS per Docker Swarm

È possibile eseguire l'agente OMS come servizio globale in Docker Swarm. Usare le informazioni seguenti per creare un servizio agente OMS. È necessario inserire l'ID area di lavoro e la chiave primaria.

- Eseguire quanto segue sul nodo principale.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

### <a name="secure-your-secret-information-for-container-services"></a>Proteggere i segreti per i servizi contenitore

È possibile proteggere l'ID segreto dell'area di lavoro OMS e le chiavi primarie segrete per Docker Swarm e Kubernetes.

#### <a name="secure-secrets-for-docker-swarm"></a>Proteggere i segreti per Docker Swarm

In Docker Swarm, una volta creato il segreto per l'ID area di lavoro e la chiave primaria è possibile creare il servizio Docker per OMSagent. Usare le informazioni seguenti per creare i segreti.

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

#### <a name="secure-secrets-for-kubernetes-with-yaml-files"></a>Proteggere i segreti per Kubernetes con file yaml

Per Kubernetes è possibile usare uno script per generare il file dei segreti con estensione yaml per l'ID area di lavoro e la chiave primaria. Nella pagina [OMS Docker/Kubernetes di GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) sono disponibili file usabili con o senza informazioni segrete.

- Il file DaemonSet predefinito dell'agente OMS che non include informazioni segrete (omsagent.yaml)
- Il file DaemonSet dell'agente OMS con estensione yaml che usa le informazioni segrete (omsagent-ds-secrets.yaml) con script per la generazione di segreti e genera il file di segreti con estensione yaml (omsagentsecret.yaml).

È possibile scegliere di creare DaemonSet dell'agente OMS con o senza segreti.

##### <a name="default-omsagent-daemonset-yaml-file-without-secrets"></a>File DaemonSet con estensione yaml predefinito dell'agente OMS senza segreti

- Per il file DaemonSet con estensione yaml predefinito dell'agente OMS, sostituire `<WSID>` e `<KEY>` a WSID e KEY. Copiare il file nel nodo principale ed eseguire le operazioni seguenti:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

##### <a name="default-omsagent-daemonset-yaml-file-with-secrets"></a>File DaemonSet con estensione yaml predefinito dell'agente OMS con segreti

1. Per usare il DaemonSet dell'agente OMS con informazioni segrete, in primo luogo creare i segreti.
    1. Copiare lo script e il file modello dei segreti e assicurarsi che siano nella stessa directory.
        - Script per la generazione di segreti: secret-gen.sh
        - Modello di segreto: secret-template.yaml
    2. Eseguire lo script come nell'esempio seguente. Lo script richiede l'ID e la chiave primaria dell'area di lavoro OMS. Dopo la specificazione di queste credenziali lo script crea un file di segreti con estensione yaml che può essere eseguito.   

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


Per Kubernetes usare uno script per generare il file dei segreti con estensione yaml per l'ID area di lavoro e la chiave primaria. Usare le informazioni di esempio seguenti con il [file yaml dell'agente OMS](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) per proteggere le informazioni segrete.

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


## <a name="windows-container-hosts"></a>Host del contenitore Windows

### <a name="preparation-before-installing-windows-agents"></a>Preparazione prima dell'installazione degli agenti di Windows

Prima di installare gli agenti nei computer che eseguono Windows, è necessario configurare il servizio Docker. La configurazione consente all'agente di Windows o all'estensione macchina virtuale Log Analytics di usare il socket TCP di Docker in modo che gli agenti possano accedere in remoto al daemon Docker e acquisire i dati per il monitoraggio.

#### <a name="to-start-docker-and-verify-its-configuration"></a>Per avviare Docker e verificare la configurazione

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


### <a name="install-windows-agents"></a>Installare gli agenti Windows

Per abilitare il monitoraggio dei contenitori Windows e Hyper-V, installare gli agenti nei computer Windows che sono host del contenitore. Per i computer che eseguono Windows nell'ambiente locale, vedere [Collegare i computer di Windows a Log Analytics](log-analytics-windows-agents.md). Per le macchine virtuali eseguite in Azure, collegarle a Log Analytics usando l'[estensione macchina virtuale](log-analytics-azure-vm-extension.md).

È possibile monitorare i contenitori Windows in esecuzione in Service Fabric. Solo le [macchine virtuali in esecuzione in Azure](log-analytics-azure-vm-extension.md) e i [computer che eseguono Windows nell'ambiente locale](log-analytics-windows-agents.md), tuttavia, sono attualmente supportati da Service Fabric.

Per verificare che la soluzione Contenitori sia impostata correttamente:

- Verificare che il management pack sia stato scaricato correttamente e cercare *ContainerManagement.xxx*.
    - Il file dovrebbe trovarsi nella cartella C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.
- Verificare che l'ID dell'area di lavoro OMS sia corretta andando in **Pannello di controllo** > **Sistema e sicurezza**.
    - Aprire **Microsoft Monitoring Agent** e verificare che le informazioni dell'area di lavoro siano corrette.


## <a name="containers-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori
La soluzione Contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host del contenitore e contenitori usando gli agenti abilitati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per i contenitori.

| Piattaforma | [Agente OMS per Linux](log-analytics-linux-agents.md) | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Sì](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |ogni 3 minuti |

| Piattaforma | [Agente Windows](log-analytics-windows-agents.md) | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sì](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |ogni 3 minuti |

| Piattaforma | [Estensione macchina virtuale di Log Analytics](log-analytics-azure-vm-extension.md) | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![Sì](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |ogni 3 minuti |

La tabella seguente mostra esempi di tipi di dati raccolti dalla soluzione Contenitori nonché i tipi di dati usati nella ricerca e nei risultati dei log.

| Tipo di dati | Tipo di dati in Ricerca log | Fields |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Type=Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `Type=ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | `Type=ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | `Type=ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | `Type=ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Monitorare i contenitori
Dopo aver abilitato la soluzione nel portale di OMS, verrà visualizzato il riquadro **Containers** (Contenitori) con le informazioni di riepilogo sugli host di contenitori e i contenitori in esecuzione negli host.

![Riquadro Containers (Contenitori)](./media/log-analytics-containers/containers-title.png)

Il riquadro visualizza una panoramica del numero di contenitori nell'ambiente e indica se i contenitori presentano errori, sono in esecuzione oppure sono stati arrestati.

### <a name="using-the-containers-dashboard"></a>Uso del dashboard Containers (Contenitori)
Fare clic sul riquadro **Containers** (Contenitori). Le visualizzazioni sono organizzate in base agli elementi seguenti:

* Eventi dei contenitori
* Errori
* Stato dei contenitori
* Inventario delle immagini dei contenitori
* Prestazioni di CPU e memoria

Ogni pannello nel dashboard è una rappresentazione visiva di una ricerca eseguita sui dati raccolti.

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash01.png)

![Dashboard Containers (Contenitori)](./media/log-analytics-containers/containers-dash02.png)

Nel pannello **Containers Status** (Stato contenitori) fare clic sull'area superiore come illustrato di seguito.

![Stato dei contenitori](./media/log-analytics-containers/containers-status.png)

Si aprirà Ricerca log con informazioni sugli host e sui contenitori in esecuzione al loro interno.

![Ricerca log per i contenitori](./media/log-analytics-containers/containers-log-search.png)

A questo punto è possibile modificare la query di ricerca per trovare specifiche informazioni di interesse. Per altre informazioni sulle ricerche log, vedere [Ricerche nei log in Log Analytics](log-analytics-log-searches.md).

È ad esempio possibile modificare la query di ricerca per visualizzare tutti i contenitori arrestati anziché i contenitori in esecuzione sostituendo **Running** con **Stopped** nella query.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Risolvere i problemi cercando un contenitore con errori
OMS contrassegna un contenitore come **Non riuscito** se il contenitore è stato terminato con un codice di uscita diverso da zero. È possibile visualizzare una panoramica degli errori nell'ambiente nel pannello **Contenitori non riusciti**.

### <a name="to-find-failed-containers"></a>Per trovare i contenitori non riusciti
1. Fare clic sul pannello **Container Events** (Eventi dei contenitori).  
   ![eventi dei contenitori](./media/log-analytics-containers/containers-events.png)
2. Si aprirà Ricerca log con lo stato di contenitori. Vedere l'esempio seguente.  
   ![stato dei contenitori](./media/log-analytics-containers/containers-container-state.png)
3. Fare quindi clic sul valore Non riuscito per visualizzare altre informazioni, ad esempio le dimensioni dell'immagine e il numero di immagini arrestate e non riuscite. Espandere **mostra dettagli** per visualizzare l'ID immagine.  
   ![contenitori non riusciti](./media/log-analytics-containers/containers-state-failed.png)
4. Trovare quindi il contenitore che esegue questa immagine. Digitare il codice seguente nella query di ricerca.
   `Type=ContainerInventory <ImageID>` Consente di visualizzare i log. È possibile scorrere per visualizzare il contenitore non riuscito.  
   ![contenitori non riusciti](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Ricerca dei dati dei contenitori nei log
Nella risoluzione di un errore specifico può essere utile vedere dove l'errore si verifica nell'ambiente. I tipi di log seguenti consentono di creare query per ottenere le informazioni necessarie.

* **ContainerInventory**: usare questo tipo per ottenere informazioni sul percorso dei contenitori, i relativi nomi e le immagini che eseguono.
* **ContainerImageInventory**: usare questo tipo per trovare informazioni organizzate per immagine e visualizzare le informazioni sulle immagini, ad esempio ID o dimensioni.
* **ContainerLog**: usare questo tipo per trovare informazioni e voci specifiche del registro errori.
* **ContainerServiceLog**: usare questo tipo per trovare informazioni di audit trail per il daemon Docker, ad esempio comandi di avvio, arresto, eliminazione o pull.

### <a name="to-search-logs-for-container-data"></a>Per cercare i dati dei contenitori nei log
* Scegliere un'immagine non riuscita di recente e trovare i relativi registri degli errori. Iniziare cercando il nome di un contenitore che esegue l'immagine con una ricerca **ContainerInventory**. Cercare ad esempio `Type=ContainerInventory ubuntu Failed`  
    ![Cercare contenitori Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Prendere nota del nome del contenitore accanto a **Name** e cercare questi log. In questo esempio si tratta di `Type=ContainerLog adoring_meitner`.

**Visualizzare le informazioni sulle prestazioni**

Quando si inizia a creare query, può essere utile comprendere prima le operazioni possibili. Per visualizzare ad esempio tutti i dati sulle prestazioni, provare con una query generica digitando la query di ricerca seguente.

```
Type=Perf
```

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf01.png)

È possibile visualizzare i dati in un formato più grafico facendo clic sulla parola **Metrics** (Metriche) nei risultati.

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf02.png)

È possibile limitare i dati sulle prestazioni visualizzati a un contenitore specifico digitando il relativo nome a destra della query.

```
Type=Perf <containerName>
```

Verrà visualizzato l'elenco delle metriche delle prestazioni raccolte per un singolo contenitore.

![prestazioni dei contenitori](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Esempio di query di ricerca log
Spesso è utile compilare query iniziando con qualche esempio da modificare in funzione dell'ambiente. Come punto di partenza è possibile provare a usare il pannello **Query rilevanti** per compilare query più avanzate.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Query sui contenitori](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Salvataggio delle query di ricerca log
Il salvataggio di query è una funzionalità standard di Log Analytics. Le query salvate potranno essere riusate rapidamente in futuro.

Dopo aver creato una query che si ritiene utile, salvarla facendo clic su **Preferiti** nella parte superiore della pagina Ricerca log. Sarà possibile accedere facilmente alla query in seguito dalla pagina **Dashboard**.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare i record di dati dettagliati per i contenitori.

