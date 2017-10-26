---
title: Patch Orchestration Application di Azure Service Fabric | Microsoft Docs
description: Applicazione per automatizzare l'applicazione di patch ai sistemi operativi in un cluster di Service Fabric.
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
ms.date: 5/9/2017
ms.author: nachandr
ms.openlocfilehash: aaceb556d926dbb09aeb2843a7941eadaaeb588b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Applicare patch al sistema operativo Windows nel cluster di Service Fabric

Patch Orchestration Application è un'applicazione Azure Service Fabric che automatizza l'applicazione di patch nei sistemi operativi in un cluster di Service Fabric senza tempi di inattività.

Patch Orchestration Application offre quanto segue:

- **Installazione automatica dell'aggiornamento del sistema operativo**. Gli aggiornamenti del sistema operativo vengono scaricati e installati automaticamente. I nodi del cluster vengono riavviati in base alle esigenze senza tempi di inattività del cluster.

- **Integrazione dell'integrità e l'applicazione di patch compatibile con il cluster**. Durante l'applicazione degli aggiornamenti, Patch Orchestration Application esegue il monitoraggio dell'integrità dei nodi del cluster. I nodi del cluster sono aggiornati un nodo alla volta o un dominio di aggiornamento alla volta. Se l'integrità del cluster si arresta a causa del processo di applicazione di patch, l'applicazione di patch viene interrotta per impedire l'aggravarsi del problema.

## <a name="internal-details-of-the-app"></a>Dettagli interni dell'app

Patch Orchestration Application è costituita dai sottocomponenti seguenti:

- **Coordinator Service**: il servizio con stato è responsabile per:
    - Il coordinamento del processo di Windows Update nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di Windows Update.
- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi di cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap del Node Agent NTService.
    - Il monitoraggio di Node Agent NTService
- **Node agent NTService**: questo servizio di Windows NT viene eseguito con un privilegio di livello superiore (SYSTEM). Il Node Agent Service e il Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore (NETWORK SERVICE). Il servizio è responsabile dell'esecuzione dei seguenti processi di Windows Update in tutti i nodi del cluster:
    - Disabilitazione della connessione automatica a Windows Update nel nodo.
    - Download e installazione di Windows Update in base al criterio fornito dall'utente.
    - Riavvio della macchina dopo l'installazione di Windows Update.
    - Caricamento dei risultati di Windows Update nel Coordinator Service.
    - Generazione di report sull'integrità in caso di un'operazione non riuscita dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> Patch Orchestration App usa il sistema di servizio di gestione della riparazione di Service Fabric per disabilitare o abilitare il nodo ed eseguire i controlli di integrità. L'attività di riparazione creata da Patch Orchestration App tiene traccia dell'avanzamento di Windows Update per ogni nodo.

## <a name="prerequisites"></a>Prerequisiti

### <a name="minimum-supported-service-fabric-runtime-version"></a>Versione minima di runtime di Service Fabric supportata

#### <a name="azure-clusters"></a>Cluster di Azure
Patch Orchestration App deve essere eseguita nei cluster di Azure con versione di runtime di Service Fabric 5.5 o successive.

#### <a name="standalone-on-premises-clusters"></a>Cluster locali autonomi
Patch Orchestration App deve essere eseguita nei cluster autonomi con versione di runtime di Service Fabric 5.6 o successive.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Abilitare il servizio di gestione della riparazione (se non è già in esecuzione)

Patch Orchestration App richiede che il servizio del sistema di gestione della riparazione sia abilitato nel cluster.

#### <a name="azure-clusters"></a>Cluster di Azure

I cluster di Azure al livello di durabilità silver hanno il servizio di gestione della riparazione abilitato per impostazione predefinita. I cluster di Azure al livello di durabilità gold potrebbero avere o non avere il servizio di gestione della riparazione abilitato, a seconda relativa data di creazione. I cluster di Azure al livello di durabilità bronze, per impostazione predefinita, non hanno il servizio di gestione della riparazione abilitato. Se il servizio è già abilitato, è possibile vederlo in esecuzione nella sezione relativa ai servizi del sistema in Service Fabric Explorer.

##### <a name="azure-portal"></a>Portale di Azure
È possibile abilitare il servizio di gestione della riparazione dal portale di Azure al momento della configurazione del cluster. Selezionare l'opzione **Includi funzionalità di gestione ripristini** in **Aggiungi funzionalità** durante la configurazione del cluster.
![Immagine dell'attivazione del servizio di gestione della riparazione dal portale di Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
In alternativa, è possibile usare il [modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio di gestione della riparazione nei cluster nuovi ed esistenti di Service Fabric. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i modelli di esempio o creare un modello di Resource Manager. 

Per abilitare il servizio di gestione della riparazione tramite il [modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Verificare prima che il `apiversion` sia impostato su `2017-07-01-preview` per la risorsa `Microsoft.ServiceFabric/clusters` come illustrato nel frammento riportato di seguito. Se il valore è diverso, è necessario aggiornare `apiVersion` a `2017-07-01-preview`:

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

È possibile usare le [Impostazioni di configurazione per un cluster autonomo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) per abilitare il servizio di gestione della riparazione nei cluster di Service Fabric nuovi ed esistenti.

Per abilitare il servizio di gestione della riparazione:

1. In primo luogo, verificare che `apiversion` in [Configurazioni generali del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) sia impostato su `04-2017` o versione successiva:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. A questo punto abilitare il servizio di gestione della riparazione aggiungendo la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`, come illustrato di seguito:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Aggiornare il manifesto del cluster con queste modifiche usando il manifesto del cluster aggiornato [Creare un cluster autonomo in esecuzione su Windows Server](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) o [Aggiornare il cluster autonomo di Azure Service Fabric in Windows Server](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Quando il cluster è in esecuzione con il manifesto del cluster aggiornato, è possibile vedere il servizio del sistema di gestione della riparazione in esecuzione nel cluster, denominato `fabric:/System/RepairManagerService`, nella sezione relativa ai servizi del sistema in Service Fabric Explorer.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Disabilitare la connessione automatica a Windows Update su tutti i nodi

Gli aggiornamenti automatici di Windows potrebbero causare la perdita di disponibilità perché più nodi del cluster possono riavviarsi nello stesso momento. Patch Orchestration App, per impostazione predefinita, tenta di disabilitare la connessione automatica a Windows Update su ogni nodo del cluster. Tuttavia, nel caso in cui le impostazioni vengano gestite da un amministratore o dai criteri di gruppo, è consigliabile impostare i criteri di Windows Update sulla notifica prima del download in modo esplicito.

### <a name="optional-enable-azure-diagnostics"></a>Facoltativo: abilitare Diagnostica di Azure

Per i cluster che eseguono la versione di runtime di Service Fabric `5.6.220.9494` e livelli successivi, verranno raccolti i log dell'app di orchestrazione come parte dei log di Service Fabric.
È possibile ignorare questo passaggio se il cluster è in esecuzione nella versione di runtime di Service Fabric `5.6.220.9494` e nelle versioni successive.

Per i cluster che eseguono la versione di runtime di Service Fabric precedente a `5.6.220.9494`, i log per l'app di orchestrazione delle patch vengono raccolti localmente su ogni nodo del cluster.
Si consiglia di configurare Diagnostica di Azure per caricare i log da tutti i nodi in una posizione centrale.

Per altre informazioni su come abilitare Diagnostica di Azure, vedere [Raccogliere log con Diagnostica di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad).

I log per Patch Orchestration App sono generati nei seguenti ID dei provider stabiliti:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Nel modello di Resource Manager passare alla sezione `EtwEventSourceProviderConfiguration` in `WadCfg` e aggiungere le seguenti voci:

```json
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  }
```

> [!NOTE]
> Se il cluster di Service Fabric ha più tipi di nodo, è necessario aggiungere la sezione precedente per tutte le sezioni `WadCfg`.

## <a name="download-the-app-package"></a>Scaricare il pacchetto dell'app

Scaricare l'applicazione dal [collegamento di download](https://go.microsoft.com/fwlink/P/?linkid=849590).

## <a name="configure-the-app"></a>Configurare l'app

Il comportamento di Patch Orchestration App può essere configurato per soddisfare le esigenze. Eseguire l'override dei valori predefiniti passando il parametro dell'applicazione durante la creazione o l'aggiornamento dell'applicazione. È possibile fornire i parametri dell'applicazione specificando `ApplicationParameter` ai cmdlet `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`.

|**Parametro**        |**Tipo**                          | **Dettagli**|
|:-|-|-|
|MaxResultsToCache    |long                              | Numero massimo di risultati di Windows Update memorizzabili nella cache. <br>Il valore predefinito è 3000 presumendo che il: <br> - Numero di nodi sia 20. <br> - Numero di aggiornamenti eseguiti su un nodo per ogni mese sia pari a cinque. <br> - Numero di risultati per ogni operazione sia pari a 10. <br> - I risultati per gli ultimi tre mesi debbano essere archiviati. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati dal Coordinator Service per installare gli aggiornamenti di Windows Update nei nodi del cluster di Service Fabric.<br>                         I valori consentiti sono i seguenti: <br>                                                           <b>NodeWise</b>. Windows Update viene installato un nodo alla volta. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update viene installato un dominio di aggiornamento alla volta (al massimo, tutti i nodi appartenenti a un dominio di aggiornamento possono passare per Windows Update).
|LogsDiskQuotaInMB   |long  <br> Predefinito: 1024               |Dimensione massima in MB dei log di Patch Orchestration App che è possibile salvare in modo permanente e locale sui nodi.
| WUQuery               | string<br>Impostazione predefinita: "IsInstalled = 0"                | Eseguire una query per ottenere gli aggiornamenti di Windows. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | Booleano <br> Predefinito: True                 | Questo flag consente l'installazione degli aggiornamenti del sistema operativo Windows.            |
| WUOperationTimeOutInMinutes | int <br>Predefinito: 90                   | Specifica il timeout per qualsiasi operazione di Windows Update (ricerca, download o installazione). L'operazione viene interrotta se non viene completata entro il timeout specificato.       |
| WURescheduleCount     | int <br> Predefinito: 5                  | Il numero massimo di volte in cui il servizio ripianifica l'aggiornamento di Windows quando un'operazione continua ad avere esito negativo.          |
| WURescheduleTimeInMinutes | int <br>Predefinito: 30 | L'intervallo con cui il servizio ripianifica l'aggiornamento di Windows se il problema persiste. |
| WUFrequency           | Stringa separata da virgole Predefinito: "Weekly, Wednesday, 7:00:00"     | La frequenza di installazione di Windows Update. Il formato e i valori possibili sono: <br>-   Monthly, DD,HH:MM:SS, ad esempio, Monthly, 5,12:22:32. <br> -   Weekly, DAY,HH:MM:SS, ad esempio, Weekly, Tuesday, 12:22:32.  <br> -   Daily, HH:MM:SS, ad esempio, Daily, 12:22:32.  <br> - None: indica che non deve essere eseguito Windows Update.  <br><br> SI noti che tutti gli orari sono in formato UTC.|
| AcceptWindowsUpdateEula | Booleano <br>Predefinito: True | Impostando questo flag, l'applicazione accetta il contratto di licenza dell'utente finale per Windows Update per conto del proprietario della macchina.              |

> [!TIP]
> Se si desidera che Windows Update venga eseguito immediatamente, impostare `WUFrequency` in relazione al tempo di distribuzione dell'applicazione. Ad esempio, si supponga di disporre di un cluster di test a cinque nodi e si prevede di distribuire l'app all'incirca alle 17:00:00 UTC. Se si presuppone che l'aggiornamento o la distribuzione dell'applicazione richiedano un massimo di 30 minuti, impostare il WUFrequency su "Daily, 17:30:00".

## <a name="deploy-the-app"></a>Distribuire l'app

1. Completare tutti i passaggi preliminari per preparare il cluster.
2. Distribuire Patch Orchestration App come qualsiasi altra app di Service Fabric. È possibile distribuire l'app tramite PowerShell. Seguire la procedura in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Per configurare l'applicazione al momento della distribuzione, passare `ApplicationParamater` al cmdlet `New-ServiceFabricApplication`. Per praticità, è stato fornito lo script Deploy.ps1 insieme all'applicazione. Per usare lo script:

    - Connettersi a un cluster di Service Fabric tramite `Connect-ServiceFabricCluster`.
    - Eseguire lo script Deploy.ps1 di PowerShell con il valore `ApplicationParameter` appropriato.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="upgrade-the-app"></a>Aggiornare l'app

Per aggiornare una Patch Orchestration App esistente tramite PowerShell, seguire la procedura in [Aggiornamento di un'applicazione di Service Fabric mediante PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Rimuovere l'app

Per rimuovere l'applicazione, seguire la procedura in [Distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Per praticità, è stato fornito lo script Undeploy.ps1 insieme all'applicazione. Per usare lo script:

  - Connettersi a un cluster di Service Fabric tramite ```Connect-ServiceFabricCluster```.

  - Eseguire lo script Undeploy.ps1 di PowerShell.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="view-the-windows-update-results"></a>Visualizzare i risultati di Windows Update

Patch Orchestration Application espone l'API REST per visualizzare i risultati cronologici all'utente. Un esempio del file JSON dei risultati:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

I campi del formato JSON sono descritti di seguito.

Campo | Valori | Dettagli
-- | -- | --
OperationResult | 0 - Completata<br> 1 - Completata con errori<br> 2 - Non riuscita<br> 3 - Interrotta<br> 4 - Interrotta con timeout | Indica il risultato dell'operazione globale, che in genere implica l'installazione di uno o più aggiornamenti.
ResultCode | Stesso di OperationResult | Questo campo indica il risultato dell'operazione di installazione di un singolo aggiornamento.
OperationType | 1 - Installazione<br> 0 - Ricerca e download.| Installation è l'unico OperationType visualizzato nei risultati per impostazione predefinita.
WindowsUpdateQuery | Impostazione predefinita: "IsInstalled = 0" |Query di aggiornamento di Windows usata per ricercare gli aggiornamenti. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true: il riavvio è necessario<br> false: il riavvio non è necessario | Indica se è necessario riavviare per completare l'installazione degli aggiornamenti.

Se non è stato ancora pianificato alcun aggiornamento, il file JSON dei risultati è vuoto.

Accedere al cluster per eseguire la query sui risultati di Windows Update. Trovare quindi l'indirizzo della replica per il primario del Coordinator Service e raggiungere l'URL dal browser: http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

L'endpoint REST per Coordinator Service dispone di una porta dinamica. Per controllare l'URL esatto, fare riferimento a Service Fabric Explorer. Ad esempio, i risultati sono disponibili in `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Nel caso in cui il proxy inverso sia abilitato nel cluster, l'utente può accedere all'URL anche dall'esterno del cluster.
L'endpoint da scegliere è: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Per abilitare il proxy inverso nel cluster, seguire la procedura in [Proxy inverso in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Dopo aver configurato il proxy inverso, tutti i microservizi nel cluster che espongono un endpoint HTTP sono indirizzabili dall'esterno del cluster.

## <a name="diagnosticshealth-events"></a>Eventi di diagnostica/integrità

### <a name="collect-patch-orchestration-app-logs"></a>Raccogliere i log di Patch Orchestration App

Vengono raccolti i log di Patch Orchestration App come parte dei log di Service Fabric dalla versione di runtime `5.6.220.9494` e versioni successive.
Per i cluster che eseguono la versione di runtime di Service Fabric inferiore a `5.6.220.9494`, i log possono essere raccolti usando uno dei metodi seguenti.

#### <a name="locally-on-each-node"></a>In locale in ogni nodo

I log vengono raccolti localmente su ogni nodo del cluster di Service Fabric se la versione del runtime di Service Fabric è precedente a `5.6.220.9494`. Il percorso per accedere ai log è \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs.

Ad esempio, se Service Fabric è installato nell'unità D, il percorso è D:\\PatchOrchestrationApplication\\logs.

#### <a name="central-location"></a>Posizione centrale

Se Diagnostica di Azure viene configurata come parte dei passaggi preliminari, i log per Patch Orchestration App sono disponibili in Archiviazione di Azure.

### <a name="health-reports"></a>Report sull'integrità

Patch Orchestration App pubblica anche i report sull'integrità per il Coordinator Service o il Node Agent Service nei casi seguenti:

#### <a name="a-windows-update-operation-failed"></a>Un'operazione di Windows Update ha avuto esito negativo

Se un'operazione di Windows Update ha esito negativo in un nodo, viene generato un report sull'integrità del Node Agent Service. I dettagli del report sull'integrità contengono il nome del nodo problematico.

Il report viene cancellato automaticamente dopo che viene completata correttamente l'applicazione delle patch nel nodo problematico.

#### <a name="the-node-agent-ntservice-is-down"></a>Il Node Agent NTService è inattivo

Se il Node Agent NTService è inattivo in un nodo, viene generato un report sull'integrità dei livelli di avviso per il Node Agent Service.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Il servizio di gestione della riparazione non è abilitato

Se il servizio di gestione della riparazione non viene individuato nel cluster, viene generato un report sull'integrità dei livelli di avviso per il Coordinator Service.

## <a name="frequently-asked-questions"></a>Domande frequenti

D: **Perché il cluster è in uno stato di errore quando Patch Orchestration App è in esecuzione?**

R. Durante il processo di installazione, Patch Orchestration App disabilita o riavvia i nodi, il che può comportare temporaneamente la violazione dell'integrità del cluster.

A seconda dei criteri impostati per l'applicazione, durante un'operazione di applicazione di patch è possibile che diventi non disponibile un singolo nodo *oppure* un intero dominio di aggiornamento simultaneamente.

Al termine dell'installazione di Windows Update, i nodi vengono abilitati di nuovo dopo il riavvio.

Nell'esempio seguente, il cluster è passato temporaneamente a uno stato di errore perché i due nodi sono stati resi inattivi e il criterio MaxPercentageUnhealthNodes è stato violato. L'errore è temporaneo fino a quando l'operazione di aggiornamento è in corso.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi.

D: **Patch Orchestration App è in stato di avviso**

R. Verificare se la causa principale è un report sull'integrità inviato all'applicazione. L'avviso in genere contiene i dettagli del problema. Se il problema è temporaneo, è previsto il ripristino automatico dell'applicazione da questo stato.

D: **Che cosa è possibile fare se il cluster non è integro ed è necessario eseguire un aggiornamento del sistema operativo?**

R. Patch Orchestration App non installa gli aggiornamenti mentre il cluster non è in uno stato di integrità. Provare a portare il cluster in uno stato di integrità per sbloccare il flusso di lavoro di Patch Orchestration App.

D: **Perché l'applicazione di patch nei cluster richiede molto tempo?**

R. Il tempo impiegato da Patch Orchestration App dipende principalmente dai seguenti fattori:

- I criteri del Coordinator Service. 
  - I criteri predefiniti, `NodeWise`, comportano l'applicazione di patch solo un nodo alla volta. In particolare nel caso di cluster più grandi, è consigliabile usare i criteri `UpgradeDomainWise` per ottenere un'applicazione più rapida delle patch nei cluster.
- Il numero di aggiornamenti disponibili per il download e l'installazione. 
- Il tempo medio necessario per scaricare e installare un aggiornamento, che non deve superare un paio d'ore.
- Le prestazioni della VM e la larghezza di banda della rete.

D: **Qual è il motivo per cui vengono visualizzati alcuni aggiornamenti nei risultati di Windows Update ottenuti tramite l'API REST, ma non nella cronologia di Windows Update sul computer?**

R. Alcuni aggiornamenti del prodotto devono essere archiviati nella rispettiva cronologia patch/di aggiornamento. Ad esempio: gli aggiornamenti di Windows Defender non vengono visualizzati nella cronologia di Windows Update in Windows Server 2016.

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Patch Orchestration App accetta il contratto licenza di Windows Update per conto dell'utente. Facoltativamente, l'impostazione può essere disattivata nella configurazione dell'applicazione.

- Patch Orchestration App raccoglie i dati di telemetria per tenere traccia dell'uso e delle prestazioni. I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric (attiva per impostazione predefinita).

## <a name="troubleshooting"></a>Risoluzione dei problemi

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

Patch Orchestration App tenta di installare un aggiornamento di Windows in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, viene generato un report sull'integrità dei livelli di avviso per Node Agent Service. Il risultato di Windows Update contiene anche la possibile causa dell'errore.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>L'integrità del cluster passa nello stato di errore durante l'installazione dell'aggiornamento

Un aggiornamento difettoso di Windows può compromettere l'integrità di un'applicazione o di un cluster in un nodo particolare o in un dominio di aggiornamento. Patch Orchestration App interrompe qualsiasi successiva operazione di aggiornamento di Windows fino a quando il cluster è di nuovo integro.

Un amministratore deve intervenire e stabilire perché l'applicazione o il cluster è diventato non integro a causa di Windows Update.

## <a name="release-notes"></a>Note sulla versione

### <a name="version-110"></a>Versione 1.1.0
- Versione pubblica

### <a name="version-111"></a>Versione 1.1.1
- Correzione di un bug in SetupEntryPoint di NodeAgentService che ha impedito l'installazione di NodeAgentNTService.

### <a name="version-120-latest"></a>Versione 1.2.0 (versione più recente)

- Correzioni di bug nel flusso di lavoro di riavvio del sistema.
- Correzione di bug nella creazione di attività RM a causa delle quali il controllo dell'integrità durante la preparazione delle attività di ripristino non ha avuto luogo come previsto.
- Modificata la modalità di avvio per il servizio di windows POANodeSvc da automatico ad automatico ritardato.
