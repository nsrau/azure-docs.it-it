---
title: Patch Orchestration Application di Azure Service Fabric per Linux | Microsoft Docs
description: Applicazione per automatizzare l'applicazione di patch ai sistemi operativi in un cluster Linux di Service Fabric.
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: dac8068705e284b04d84d128eb1ce62c459d44ff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Applicare patch al sistema operativo Linux nel cluster di Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Patch Orchestration Application è un'applicazione Azure Service Fabric che automatizza l'applicazione di patch nei sistemi operativi in un cluster di Service Fabric senza tempi di inattività.

L'app Patch Orchestration offre le funzionalità seguenti:

- **Installazione automatica dell'aggiornamento del sistema operativo**. Gli aggiornamenti del sistema operativo vengono scaricati e installati automaticamente. I nodi del cluster vengono riavviati in base alle esigenze senza tempi di inattività del cluster.

- **Integrazione dell'integrità e l'applicazione di patch compatibile con il cluster**. Durante l'applicazione degli aggiornamenti, Patch Orchestration Application esegue il monitoraggio dell'integrità dei nodi del cluster. I nodi del cluster sono aggiornati un nodo alla volta o un dominio di aggiornamento alla volta. Se l'integrità del cluster si arresta a causa del processo di applicazione di patch, l'applicazione di patch viene interrotta per impedire l'aggravarsi del problema.

## <a name="internal-details-of-the-app"></a>Dettagli interni dell'app

Patch Orchestration Application è costituita dai sottocomponenti seguenti:

- **Coordinator Service**: il servizio con stato è responsabile per:
    - Il coordinamento del processo di aggiornamento del sistema operativo nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di aggiornamento del sistema operativo.
- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi di cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap del daemon dell'agente del nodo in Linux.
    - Monitoraggio del servizio daemon.
- **Daemon dell'agente del nodo**: questo servizio daemon Linux viene eseguito con un privilegio di livello superiore (radice). Il Node Agent Service e il Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore. Il servizio è responsabile dell'esecuzione dei processi di aggiornamento seguenti in tutti i nodi del cluster:
    - Disabilitazione dell'aggiornamento automatico del sistema operativo nel nodo.
    - Download e installazione dell'aggiornamento del sistema operativo in base al criterio specificato dall'utente.
    - Riavvio della macchina dopo l'installazione dell'aggiornamento del sistema operativo se necessario.
    - Caricamento dei risultati degli aggiornamenti del sistema operativo nel Coordinator Service.
    - Generazione di report sull'integrità in caso di un'operazione non riuscita dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> Patch Orchestration App usa il sistema di servizio di gestione della riparazione di Service Fabric per disabilitare o abilitare il nodo ed eseguire i controlli di integrità. L'attività di riparazione creata da Patch Orchestration App tiene traccia dell'avanzamento dell'aggiornamento per ogni nodo.

## <a name="prerequisites"></a>prerequisiti

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Verificare che sulle macchine virtuali di Azure sia in esecuzione Ubuntu 16.04
Al momento della stesura di questo documento, Ubuntu 16.04 (`Xenial Xerus`) è l'unica versione supportata.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Verificare che la versione del cluster Linux Service Fabric sia la 6.1.x o successive

Patch Orchestration App Linux usa alcune funzionalità di runtime disponibili solo nella versione di runtime di Service Fabric 6.1.x e versioni successive.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Abilitare il servizio di gestione della riparazione (se non è già in esecuzione)

Patch Orchestration App richiede che il servizio del sistema di gestione della riparazione sia abilitato nel cluster.

#### <a name="azure-clusters"></a>Cluster di Azure

I cluster Linux di Azure al livello di durabilità silver hanno il servizio di gestione della riparazione abilitato per impostazione predefinita. I cluster di Azure al livello di durabilità bronze, per impostazione predefinita, non hanno il servizio di gestione della riparazione abilitato. Se il servizio è già abilitato, è possibile vederlo in esecuzione nella sezione relativa ai servizi del sistema in Service Fabric Explorer.

##### <a name="azure-portal"></a>Portale di Azure
È possibile abilitare il servizio di gestione della riparazione dal portale di Azure al momento della configurazione del cluster. Selezionare l'opzione **Includi funzionalità di gestione ripristini** in **Aggiungi funzionalità** durante la configurazione del cluster.
![Immagine dell'attivazione del servizio di gestione della riparazione dal portale di Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modello di distribuzione di Azure Resource Manager
In alternativa, è possibile usare il [modello di distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio di gestione della riparazione nei cluster nuovi ed esistenti di Service Fabric. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i modelli di esempio o creare un modello di distribuzione Azure Resource Manager personalizzato. 

Per abilitare il servizio di gestione della riparazione tramite il [modello di distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Verificare prima di tutto che `apiversion` sia impostato su `2017-07-01-preview` per la risorsa `Microsoft.ServiceFabric/clusters`. Se il valore è diverso, è necessario aggiornare `apiVersion` al valore `2017-07-01-preview` o superiore:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto, abilitare il servizio di gestione della riparazione aggiungendo la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Dopo aver aggiornato il modello del cluster con queste modifiche, applicarle e consentire la conclusione dell'aggiornamento. È ora possibile vedere il servizio del sistema di gestione della riparazione in esecuzione nel cluster. Viene chiamato `fabric:/System/RepairManagerService` nella sezione relativa ai servizi del sistema in Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Cluster locali autonomi

Al momento della stesura di questo documento non sono supportati i cluster Linux Service Fabric autonomi.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Disabilitare l'aggiornamento del sistema operativo su tutti i nodi

Gli aggiornamenti automatici del sistema operativo potrebbero causare la perdita di disponibilità e o la modifica del comportamento delle applicazioni in esecuzione. Patch Orchestration App, per impostazione predefinita, tenta di disabilitare l'aggiornamento del sistema operativo su ogni nodo del cluster per evitare questi scenari.
Per Ubuntu gli [aggiornamenti automatici](https://help.ubuntu.com/community/AutomaticSecurityUpdates) vengono disabilitati da Patch Orchestration App.

## <a name="download-the-app-package"></a>Scaricare il pacchetto dell'app

Scaricare l'applicazione dal [collegamento di download](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Configurare l'app

Il comportamento di Patch Orchestration App può essere configurato per soddisfare le esigenze. Eseguire l'override dei valori predefiniti passando il parametro dell'applicazione durante la creazione o l'aggiornamento dell'applicazione. È possibile fornire i parametri dell'applicazione specificando `ApplicationParameter` ai cmdlet `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`.

|**Parametro**        |**Tipo**                          | **Dettagli**|
|:-|-|-|
|MaxResultsToCache    |long                              | Numero massimo di risultati dell'aggiornamento memorizzabili nella cache. <br>Il valore predefinito è 3000 presumendo che il: <br> - Numero di nodi sia 20. <br> - Numero di aggiornamenti eseguiti su un nodo per ogni mese sia pari a cinque. <br> - Numero di risultati per ogni operazione sia pari a 10. <br> - I risultati per gli ultimi tre mesi debbano essere archiviati. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati dal Coordinator Service per installare gli aggiornamenti nei nodi del cluster di Service Fabric.<br>                         I valori consentiti sono i seguenti: <br>                                                           <b>NodeWise</b>. Gli aggiornamenti vengono installati un nodo alla volta. <br>                                                           <b>UpgradeDomainWise</b>. Gli aggiornamenti vengono installati un dominio di aggiornamento alla volta. (Possono esser aggiornati al massimo tutti i nodi appartenenti a un dominio di aggiornamento).
| UpdateOperationTimeOutInMinutes | int <br>(Predefinito: 180)                   | Specifica il timeout per qualsiasi operazione di aggiornamento (download o installazione). L'operazione viene interrotta se non viene completata entro il timeout specificato.       |
| RescheduleCount      | int <br> Predefinito: 5                  | Il numero massimo di volte in cui il servizio ripianifica l'aggiornamento del sistema operativo quando un'operazione continua ad avere esito negativo.          |
| RescheduleTimeInMinutes  | int <br>Predefinito: 30 | L'intervallo con cui il servizio ripianifica l'aggiornamento del sistema operativo se il problema persiste. |
| UpdateFrequency           | Stringa separata da virgole Predefinito: "Weekly, Wednesday, 7:00:00"     | La frequenza per l'installazione di aggiornamenti del sistema operativo nel cluster. Il formato e i valori possibili sono: <br>-   Monthly, DD, HH:MM:SS, ad esempio Monthly, 5, 12:22:32. <br> -   Weekly, DAY, HH:MM:SS, ad esempio Weekly, Tuesday, 12:22:32.  <br> -   Daily, HH:MM:SS, ad esempio, Daily, 12:22:32.  <br> - None indica che l'aggiornamento non deve essere eseguito.  <br><br> Tutti gli orari sono in formato UTC.|
| UpdateClassification | Stringa separata da virgole (Predefinito: "securityupdates") | Tipo di aggiornamenti che devono essere installati nei nodi del cluster. I valori accettabili sono securityupdates, all. <br> - securityupdates - vengono installati solo gli aggiornamenti della sicurezza <br> - all - vengono installati tutti gli aggiornamenti disponibili da apt.|
| ApprovedPatches | Stringa separata da virgole (Predefinito: "") | Si tratta dell'elenco di aggiornamenti approvati che devono essere installati nei nodi del cluster. L'elenco delimitato da virgole contiene pacchetti approvati e, facoltativamente, la versione di destinazione desiderata.<br> Ad esempio: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> installerebbe <br> - apt-utils con versione 1.2.10ubuntu1, se disponibile in apt-cache. Se quella versione specifica non è disponibile, non viene eseguita alcuna operazione. <br> -python3 jwt aggiorna alla versione più recente disponibile. Se il pacchetto non è presente, non viene eseguita alcuna operazione. <br> -apt trasporto https aggiorna alla versione più recente, ovvero precedente la 1.2.194. Se questa versione non è presente, non viene eseguita alcuna operazione. <br> - libsystemd0 aggiorna alla versione più recente, ovvero 229-4ubuntu16 o successiva. Se questa versione non esiste, non viene eseguita alcuna operazione.|
| RejectedPatches | Stringa separata da virgole (Predefinito: "") | Si tratta dell'elenco di aggiornamenti che non devono essere installati nei nodi del cluster. <br> Ad esempio: "bash, sudo" <br> esclude bash, sudo dalla ricezione di aggiornamenti. |


> [!TIP]
> Se si desidera che l'aggiornamento del sistema operativo venga eseguito immediatamente, impostare `UpdateFrequency` in relazione al tempo di distribuzione dell'applicazione. Ad esempio, si supponga di disporre di un cluster di test a cinque nodi e si prevede di distribuire l'app all'incirca alle 17:00:00 UTC. Se si presuppone che l'aggiornamento o la distribuzione dell'applicazione richiedano un massimo di 30 minuti, impostare UpdateFrequency su "Daily, 17:30:00".

## <a name="deploy-the-app"></a>Distribuire l'app

1. Preparare il cluster completando tutti i passaggi preliminari.
2. Distribuire Patch Orchestration App come qualsiasi altra app di Service Fabric. È possibile distribuire l'app tramite l'interfaccia della riga di comando di Service Fabric PowerShell o Azure. Seguire la procedura descritta in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) o [Distribuire un'applicazione con l'interfaccia della riga di comando di Service Fabric Azure](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Per configurare l'applicazione al momento della distribuzione, passare `ApplicationParamater` al cmdlet `New-ServiceFabricApplication` o agli script forniti. Per comodità, insieme all'applicazione vengono forniti gli script powershell (Deploy.ps1) e bash (Deploy.sh). Per usare lo script:

    - Connettersi a un cluster di Service Fabric.
    - Eseguire lo script di distribuzione. Facoltativamente, passare il parametro dell'applicazione allo script. Ad esempio: .\Deploy.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} OPPURE ./Deploy.sh "{\"UpdateFrequency\":\"Daily, 11:00:00\"}" 

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="upgrade-the-app"></a>Aggiornare l'app

Per aggiornare una Patch Orchestration App esistente, seguire la procedura in [Aggiornamento di un'applicazione di Service Fabric con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) o [Aggiornamento di un'applicazione di Service Fabric con l'interfaccia della riga di comando di Service Fabric Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Rimuovere l'app

Per rimuovere l'applicazione, seguire la procedura descritta in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) o [Rimuovere un'applicazione con l'interfaccia della riga di comando di Service Fabric Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Per comodità, insieme all'applicazione vengono forniti gli script powershell (Undeploy.ps1) e bash (Undeploy.sh). Per usare lo script:

  - Connettersi a un cluster di Service Fabric.
  - Eseguire lo script Undeploy.ps1 o Undeploy.sh

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="view-the-update-results"></a>Visualizzare i risultati dell'aggiornamento

Patch Orchestration Application espone l'API REST per visualizzare i risultati cronologici all'utente. Di seguito è riportato un esempio di risultato: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

I campi del formato JSON sono descritti di seguito:

Campo | Valori | Dettagli
-- | -- | --
OperationResult | 0 - Completata<br> 1 - Completata con errori<br> 2 - Non riuscita<br> 3 - Interrotta<br> 4 - Interrotta con timeout | Indica il risultato dell'operazione globale, che in genere implica l'installazione di uno o più aggiornamenti.
ResultCode | Stesso di OperationResult | Questo campo indica il risultato dell'operazione di installazione di un singolo aggiornamento.
OperationType | 1 - Installazione<br> 0 - Ricerca e download.| Installation è l'unico OperationType visualizzato nei risultati per impostazione predefinita.
UpdateClassification | Il valore predefinito è "securityupdates" | Tipo di aggiornamenti che vengono installati durante l'operazione di aggiornamento
UpdateFrequency | Il valore predefinito è "Weekly, Wednesday, 7:00:00" | Frequenza configurata per questo aggiornamento.
RebootRequired | true: il riavvio è necessario<br> false: il riavvio non è necessario | Indica che è necessario riavviare per completare l'installazione degli aggiornamenti.
ApprovedList | L'impostazione predefinita è "" | Elenco delle patch approvate per questo aggiornamento
RejectedList | L'impostazione predefinita è "" | Elenco delle patch rifiutate per questo aggiornamento

Se non è stato ancora pianificato alcun aggiornamento, il file JSON dei risultati è vuoto.

Accedere al cluster per eseguire la query sui risultati dell'aggiornamento. Trovare quindi l'indirizzo della replica per il primario del Coordinator Service e raggiungere l'URL dal browser: http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults.

L'endpoint REST per Coordinator Service dispone di una porta dinamica. Per controllare l'URL esatto, fare riferimento a Service Fabric Explorer. Ad esempio, i risultati sono disponibili in `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Eventi di diagnostica/integrità

### <a name="diagnostic-logs"></a>Log di diagnostica

Vengono raccolti i log dell'app di orchestrazione delle patch come parte dei log di runtime di Service Fabric.

Nel caso in cui si vogliano acquisire i log tramite lo strumento o la pipeline di diagnostica preferita. Patch Orchestration Application usa gli ID provider corretti riportati di seguito per registrare gli eventi tramite [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Report sull'integrità

Patch Orchestration App pubblica anche i report sull'integrità per il Coordinator Service o il Node Agent Service nei casi seguenti:

#### <a name="an-update-operation-failed"></a>Operazione di aggiornamento non riuscita

Se un'operazione di aggiornamento ha esito negativo in un nodo, viene generato un report sull'integrità del Node Agent Service. I dettagli del report sull'integrità contengono il nome del nodo problematico.

Il report viene cancellato automaticamente dopo che viene completata correttamente l'applicazione delle patch nel nodo problematico.

#### <a name="the-node-agent-daemon-service-is-down"></a>Il servizio daemon dell'agente del nodo è inattivo

Se il servizio daemon dell'agente del nodo è inattivo in un nodo, viene generato un report sull'integrità dei livelli di avviso per il Node Agent Service.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Il servizio di gestione della riparazione non è abilitato

Se il servizio di gestione della riparazione non viene individuato nel cluster, viene generato un report sull'integrità dei livelli di avviso per il Coordinator Service.

## <a name="frequently-asked-questions"></a>Domande frequenti

D: **Perché il cluster è in uno stato di errore quando Patch Orchestration App è in esecuzione?**

R. Durante il processo di installazione, Patch Orchestration App disabilita o riavvia i nodi. Questa operazione può determinare la temporanea indisponibilità dell'integrità del cluster.

A seconda dei criteri impostati per l'applicazione, durante un'operazione di applicazione di patch è possibile che diventi non disponibile un singolo nodo *oppure* un intero dominio di aggiornamento simultaneamente.

Al termine dell'installazione, i nodi vengono abilitati di nuovo dopo il riavvio.

Nell'esempio seguente il cluster è passato temporaneamente a uno stato di errore perché i due nodi sono stati resi inattivi e il criterio MaxPercentageUnhealthNodes è stato violato. L'errore è temporaneo fino a quando l'operazione di aggiornamento è in corso.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi.

D: **Patch Orchestration App è in stato di avviso**

R. Verificare se la causa principale è un report sull'integrità inviato all'applicazione. L'avviso in genere contiene i dettagli del problema. Se il problema è temporaneo, è previsto il ripristino automatico dell'applicazione da questo stato.

D: **Che cosa è possibile fare se il cluster non è integro ed è necessario eseguire un aggiornamento del sistema operativo?**

R. Patch Orchestration App non installa gli aggiornamenti mentre il cluster non è in uno stato di integrità. Per sbloccare il flusso di lavoro di Patch Orchestration App, provare a portare il cluster in uno stato di integrità.

D: **Perché l'applicazione di patch nei cluster richiede molto tempo?**

R. Il tempo impiegato da Patch Orchestration App dipende principalmente dai seguenti fattori:

- I criteri del Coordinator Service. 
  - I criteri predefiniti, `NodeWise`, comportano l'applicazione di patch solo un nodo alla volta. In particolare nel caso di cluster più grandi, è consigliabile usare il criterio `UpgradeDomainWise` per ottenere un'applicazione più rapida delle patch nel cluster.
- Il numero di aggiornamenti disponibili per il download e l'installazione. 
- Il tempo medio necessario per scaricare e installare un aggiornamento, che non deve superare un paio d'ore.
- Le prestazioni della VM e la larghezza di banda della rete.

D: **In che modo Patch Orchestration App decide quali aggiornamenti sono gli aggiornamenti della sicurezza.**

R. Patch Orchestration App usa la logica specifica della distribuzione per determinare quali aggiornamenti tra quelli disponibili sono gli aggiornamenti della sicurezza. Ad esempio: In ubuntu l'app ricerca di aggiornamenti dagli archivi $RELEASE-security, $RELEASE-updates ($RELEASE = xenial o la versione della release Linux Standard Base). 

 
D: **Come è possibile bloccare una versione specifica del pacchetto?**

R. Usare le impostazioni di ApprovedPatches per bloccare i pacchetti a una versione specifica. 


D: **Cosa accade agli aggiornamenti automatici abilitati in Ubuntu?**

R. Non appena si installa Patch Orchestration App nel cluster, gli aggiornamenti automatici sul nodo del cluster verrebero disabilitati. Tutto il flusso di lavoro di aggiornamento periodico dipenderebbe da Patch Orchestration App.
Per garantire la coerenza dell'ambiente nei cluster, è consigliabile installare gli aggiornamenti solo tramite Patch Orchestration App. 
 
D: **Dopo l'aggiornamento Patch Orchestration App esegue la pulizia dei pacchetti inutilizzati?**

R. Sì, la pulizia viene eseguita durante i passaggi successivi all'installazione. 

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nodo non torna allo stato attivo

**Il nodo potrebbe essere bloccato in uno stato di disattivazione perché**:

Un controllo di sicurezza è in sospeso. Per risolvere questo problema, assicurarsi che siano disponibili sufficienti nodi in uno stato di integrità.

**Il nodo potrebbe essere bloccato in uno stato di disattivazione perché**:

- Il nodo è stato disabilitato manualmente.
- Il nodo è stato disabilitato a causa di un processo dell'infrastruttura di Azure in corso.
- Il nodo è stato disabilitato temporaneamente da Patch Orchestration App per applicare le patch al nodo.

**Il nodo potrebbe essere bloccato in uno stato inattivo perché**:

- Il nodo è stato impostato su uno stato inattivo manualmente.
- Il nodo è in fase di riavvio (che è possibile che venga avviato da Patch Orchestration App).
- Il nodo è inattivo a causa di problemi relativi a una rete o macchina o macchina virtuale difettosa.

### <a name="updates-were-skipped-on-some-nodes"></a>Gli aggiornamenti sono stati ignorati in alcuni nodi

Patch Orchestration App tenta di installare un aggiornamento in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, viene generato un report sull'integrità dei livelli di avviso per Node Agent Service. Il risultato dell'aggiornamento contiene anche la possibile causa dell'errore.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>L'integrità del cluster passa nello stato di errore durante l'installazione dell'aggiornamento

Un aggiornamento difettoso può compromettere l'integrità di un'applicazione o di un cluster in un nodo o un dominio di aggiornamento specifico. Patch Orchestration App interrompe qualsiasi successiva operazione di aggiornamento fino a quando il cluster è di nuovo integro.

Un amministratore deve intervenire e stabilire perché l'applicazione o il cluster è diventato non integro a causa di un aggiornamento installato precedentemente.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità

Patch Orchestration App raccoglie i dati di telemetria per tenere traccia dell'uso e delle prestazioni. I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric (attiva per impostazione predefinita).

## <a name="release-notes"></a>Note sulla versione

### <a name="version-010"></a>Versione 0.1.0
- Versione di anteprima privata

### <a name="version-200-latest"></a>Versione 2.0.0 (la più recente)
- Versione pubblica
