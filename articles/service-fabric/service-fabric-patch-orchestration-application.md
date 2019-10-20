---
title: Applicare patch al sistema operativo Windows nel cluster Service Fabric | Microsoft Docs
description: Questo articolo illustra come automatizzare l'applicazione di patch al sistema operativo in un cluster Service Fabric usando patch Orchestration Application.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: a02228593a9d8efc9fb363232da1cede3c80a8b3
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592522"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Applicare patch al sistema operativo Windows nel cluster di Service Fabric

> 
> [!IMPORTANT]
> A partire dal 30 aprile 2019, patch Orchestration Application versione 1,2. * non è più supportata. Assicurarsi di eseguire l'aggiornamento alla versione più recente.

> [!NOTE]
> Ottenere gli [aggiornamenti automatici delle immagini del sistema operativo nel set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) è la procedura consigliata per mantenere aggiornato il sistema operativo in Azure. Gli aggiornamenti automatici delle immagini del sistema operativo basati su set di scalabilità di macchine virtuali richiedono una durabilità Silver o maggiore in un set di scalabilità.
>

 Patch Orchestration Application (POA) è un wrapper per il servizio Azure Service Fabric Gestione ripristini, che consente la pianificazione della patch del sistema operativo basata sulla configurazione per i cluster non ospitati in Azure. Il POA non è obbligatorio per i cluster non ospitati da Azure, ma è necessaria la pianificazione dell'installazione delle patch da un dominio di aggiornamento per applicare patch Service Fabric host del cluster senza incorrere in tempi di inattività.

POA è un'applicazione Service Fabric che automatizza l'applicazione di patch al sistema operativo in un cluster Service Fabric senza incorrere in tempi di inattività.

In POA sono disponibili le seguenti funzionalità:

- **Installazione automatica dell'aggiornamento del sistema operativo**. Gli aggiornamenti del sistema operativo vengono scaricati e installati automaticamente. I nodi del cluster vengono riavviati in base alle esigenze senza incorrere in tempi di inattività del cluster.

- **Integrazione dell'integrità e l'applicazione di patch compatibile con il cluster**. Mentre POA sta applicando gli aggiornamenti, monitora l'integrità dei nodi del cluster. I nodi del cluster vengono aggiornati un nodo alla volta o un dominio di aggiornamento alla volta. Se l'integrità del cluster si arresta a causa del processo di applicazione di patch, l'applicazione di patch viene arrestata per evitare di aggravare il problema.

## <a name="internal-details-of-poa"></a>Dettagli interni del POA

POA è costituito dai sottocomponenti seguenti:

- **Coordinator Service**: il servizio con stato è responsabile per:
    - Il coordinamento del processo di Windows Update nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di Windows Update.

- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi di cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap del Node Agent NTService.
    - Il monitoraggio di Node Agent NTService

- **Node agent NTService**: questo servizio di Windows NT viene eseguito con un privilegio di livello superiore (SYSTEM). Il Node Agent Service e il Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore (NETWORK SERVICE). Il servizio è responsabile dell'esecuzione dei seguenti processi di Windows Update in tutti i nodi del cluster:
    - Disabilitazione degli aggiornamenti automatici di Windows nel nodo.
    - Download e installazione degli aggiornamenti di Windows in base ai criteri forniti dall'utente.
    - Riavvio del computer dopo l'installazione degli aggiornamenti di Windows.
    - Caricamento dei risultati di Windows Update nel Coordinator Service.
    - Segnalazione del rapporto di stato in caso di esito negativo di un'operazione dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> POA usa il servizio di Gestione ripristini Service Fabric per disabilitare o abilitare il nodo ed eseguire controlli di integrità. L'attività di ripristino creata da POA tiene traccia dello stato di avanzamento di Windows Update per ogni nodo.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> La versione minima richiesta del .NET Framework è 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Abilitare il servizio Gestione ripristini (se non è già in esecuzione)

POA richiede che il servizio di Gestione ripristini sia abilitato nel cluster.

#### <a name="azure-clusters"></a>Cluster di Azure

I cluster di Azure nel livello di durabilità Silver hanno il servizio Gestione ripristini abilitato per impostazione predefinita. I cluster di Azure nel livello di durabilità Gold potrebbero o meno essere abilitati per il servizio Gestione ripristini, a seconda del momento in cui sono stati creati tali cluster. Per impostazione predefinita, per i cluster di Azure nel livello di durabilità Bronze non è abilitato il servizio Gestione ripristini. Se il servizio è già abilitato, è possibile vederlo in esecuzione nella sezione servizi di sistema in Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Portale di Azure
È possibile abilitare Gestione ripristini dal portale di Azure quando si configura il cluster. Quando si configura il cluster, selezionare l'opzione **includi gestione ripristini** in **funzionalità componente aggiuntivo**.

![Immagine dell'abilitazione di Gestione ripristini dalla portale di Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Il modello di distribuzione Azure Resource Manager
In alternativa, è possibile usare il [modello di distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio gestione ripristini nei cluster Service Fabric nuovi ed esistenti. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i modelli di esempio o creare un modello di distribuzione Azure Resource Manager personalizzato. 

Per abilitare il servizio Gestione ripristini tramite il modello di [distribuzione Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm), procedere come segue:

1. Assicurarsi che `apiVersion` sia impostato su *2017-07-01-Preview* per la risorsa *Microsoft. ServiceFabric/Clusters* . Se è diverso, è necessario aggiornare `apiVersion` a *2017-07-01-Preview* o versione successiva:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Per abilitare il servizio Gestione ripristini, aggiungere la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Dopo aver aggiornato il modello di cluster con queste modifiche, applicarle e consentire il completamento dell'aggiornamento. È ora possibile visualizzare il servizio Gestione ripristini in esecuzione nel cluster. È denominato *Fabric:/System/RepairManagerService* nella sezione dei servizi di sistema in Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Cluster locali autonomi

Per abilitare il servizio Gestione ripristini in un cluster Service Fabric nuovo o esistente, è possibile usare le [impostazioni di configurazione per il cluster autonomo di Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Per abilitare il servizio Gestione ripristini:

1. Assicurarsi che `apiVersion` in [configurazioni cluster generali](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) sia impostato su *04-2017* o versione successiva, come illustrato di seguito:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Per abilitare il servizio Gestione ripristini, aggiungere la sezione seguente `addonFeatures` dopo la sezione `fabricSettings`, come illustrato di seguito:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Aggiornare il manifesto del cluster con queste modifiche usando il manifesto del cluster aggiornato [creare un nuovo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) o [aggiornare la configurazione del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Quando il cluster è in esecuzione con un manifesto del cluster aggiornato, è possibile visualizzare il servizio Gestione ripristini in esecuzione nel cluster. È denominato *Fabric:/System/RepairManagerService*e si trova nella sezione servizi di sistema in Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurare gli aggiornamenti di Windows per tutti i nodi

Gli aggiornamenti automatici di Windows possono comportare una perdita di disponibilità, perché più nodi del cluster possono essere riavviati nello stesso momento. Per impostazione predefinita, POA tenta di disabilitare gli aggiornamenti automatici di Windows in ogni nodo del cluster. Tuttavia, se le impostazioni sono gestite da un amministratore o da una Criteri di gruppo, è consigliabile impostare i criteri di Windows Update su "notifica prima del download" in modo esplicito.

## <a name="download-the-application-package"></a>Scaricare il pacchetto dell'applicazione

Per scaricare il pacchetto dell'applicazione, passare alla [pagina della versione di patch Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) su GitHub.

## <a name="configure-poa-behavior"></a>Configurare il comportamento di POA

È possibile configurare il comportamento di POA per soddisfare le proprie esigenze. Eseguire l'override dei valori predefiniti passando il parametro dell'applicazione durante la creazione o l'aggiornamento dell'applicazione. È possibile specificare i parametri dell'applicazione specificando `ApplicationParameter` per i cmdlet `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`.

| Parametro        | Type                          | Dettagli |
|:-|-|-|
|MaxResultsToCache    |Lungo                              | Numero massimo di Windows Update risultati che devono essere memorizzati nella cache. <br><br>Il valore predefinito è 3000, supponendo che: <br> &nbsp; &nbsp;: il numero di nodi è 20. <br> &nbsp; &nbsp;: il numero di aggiornamenti per un nodo al mese è 5. <br> &nbsp; &nbsp;: il numero di risultati per ogni operazione può essere 10. <br> &nbsp; &nbsp;: i risultati degli ultimi tre mesi devono essere archiviati. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati dal Coordinator Service per installare gli aggiornamenti di Windows Update nei nodi del cluster di Service Fabric.<br><br>I valori consentiti sono: <br>*No,:* gli aggiornamenti di Windows vengono installati un nodo alla volta. <br> *UpgradeDomainWise*: gli aggiornamenti di Windows sono installati un dominio di aggiornamento alla volta. Al massimo, tutti i nodi appartenenti a un dominio di aggiornamento possono andare per un aggiornamento di Windows.<br><br> Per decidere quale criterio è più adatto per il cluster, vedere la sezione [domande frequenti](#frequently-asked-questions) .
|LogsDiskQuotaInMB   |Lungo  <br> (Valore predefinito: *1024*)               | Dimensioni massime dei log di patch Orchestration app in MB, che possono essere rese disponibili localmente nei nodi.
| WUQuery               | string<br>(Impostazione predefinita: *disinstallato = 0*)                | Eseguire una query per ottenere gli aggiornamenti di Windows. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (impostazione predefinita: false)                 | Usare questo flag per controllare quali aggiornamenti devono essere scaricati e installati. Sono consentiti i valori seguenti: <br>true: installa solo gli aggiornamenti del sistema operativo Windows.<br>false: installa tutti gli aggiornamenti disponibili nel computer.          |
| WUOperationTimeOutInMinutes | Int <br>(Valore predefinito: *90*)                   | Specifica il timeout per qualsiasi operazione di Windows Update (ricerca, download o installazione). L'operazione viene interrotta se non viene completata entro il timeout specificato.       |
| WURescheduleCount     | Int <br> (Valore predefinito: *5*)                  | Il numero massimo di volte in cui il servizio Ripianifica l'aggiornamento di Windows se un'operazione ha esito negativo in modo permanente.          |
| WURescheduleTimeInMinutes | Int <br>(Valore predefinito: *30*) | Intervallo in base al quale il servizio Ripianifica gli aggiornamenti di Windows in caso di errore permanente. |
| WUFrequency           | Stringa con valori delimitati da virgole (valore predefinito: *settimanale, mercoledì, 7:00:00*)     | Frequenza di installazione degli aggiornamenti di Windows. Il formato e i valori possibili sono: <br>&nbsp; &nbsp;-Monthly: DD, HH: MM: SS (ad esempio, *Monthly, 5, 12:22:32*)<br>I valori consentiti per il campo GG (giorno) sono numeri compresi tra 1 e 28 e "ultimo". <br> &nbsp; &nbsp;-Weekly, DAY, HH: MM: SS (ad esempio, *Weekly, Tuesday, 12:22:32*)  <br> &nbsp; &nbsp;-Daily, HH: MM: SS (ad esempio, *Daily, 12:22:32*)  <br> &nbsp; &nbsp; -  *None* indica che non è necessario eseguire gli aggiornamenti di Windows.  <br><br> Gli orari sono in formato UTC.|
| AcceptWindowsUpdateEula | boolean <br>(Valore predefinito: *true*) | Impostando questo flag, l'applicazione accetta il contratto di licenza dell'utente finale per Windows Update per conto del proprietario della macchina.              |

> [!TIP]
> Se si desidera che gli aggiornamenti di Windows vengano eseguiti immediatamente, impostare `WUFrequency` rispetto al tempo di distribuzione dell'applicazione. Ad esempio, si supponga di disporre di un cluster di test a cinque nodi e si prevede di distribuire l'app all'incirca alle 17:00:00 UTC. Se si presuppone che l'aggiornamento o la distribuzione dell'applicazione riprenda al massimo 30 minuti, impostare WUFrequency come *giornaliero, 17:30:00*.

## <a name="deploy-poa"></a>Distribuisci POA

1. Completare tutti i passaggi preliminari per preparare il cluster.
1. Distribuire il POA come qualsiasi altra app Service Fabric. Per distribuirlo usando PowerShell, vedere [distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Per configurare l'applicazione al momento della distribuzione, passare `ApplicationParameter` al cmdlet `New-ServiceFabricApplication`. Per praticità, è stato fornito lo script Deploy.ps1 insieme all'applicazione. Per usare lo script:

    - Connettersi a un cluster di Service Fabric tramite `Connect-ServiceFabricCluster`.
    - Eseguire lo script Deploy.ps1 di PowerShell con il valore `ApplicationParameter` appropriato.

> [!NOTE]
> Conserva lo script e la cartella dell'applicazione *PatchOrchestrationApplication* nella stessa directory.

## <a name="upgrade-poa"></a>Aggiornamento POA

Per aggiornare la versione di POA usando PowerShell, seguire le istruzioni riportate in [Service Fabric aggiornamento dell'applicazione con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Rimuovi POA

Per rimuovere l'applicazione, seguire le istruzioni in [distribuire e rimuovere applicazioni con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Per praticità, è stato fornito lo script undeploy. ps1 insieme all'applicazione. Per usare lo script:

  - Connettersi a un cluster di Service Fabric tramite ```Connect-ServiceFabricCluster```.
  - Eseguire lo script Undeploy.ps1 di PowerShell.

> [!NOTE]
> Conserva lo script e la cartella dell'applicazione *PatchOrchestrationApplication* nella stessa directory.

## <a name="view-the-windows-update-results"></a>Visualizzare i risultati di Windows Update

POA espone le API REST per visualizzare i risultati cronologici agli utenti. Ecco un esempio del risultato JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
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

I campi JSON sono descritti nella tabella seguente:

Campo | Valori | Dettagli
-- | -- | --
OperationResult | 0 - Completata<br> 1 - Completata con errori<br> 2 - Non riuscita<br> 3 - Interrotta<br> 4 - Interrotta con timeout | Indica il risultato dell'operazione complessiva, che in genere comporta l'installazione di uno o più aggiornamenti.
ResultCode | Stesso di OperationResult | Questo campo indica il risultato dell'operazione di installazione per un singolo aggiornamento.
OperationType | 1 - Installazione<br> 0-ricerca e download| Per impostazione predefinita, l'installazione è l'unico OperationType visualizzato nei risultati.
WindowsUpdateQuery | Impostazione predefinita: "IsInstalled = 0" | Query Windows Update utilizzata per la ricerca di aggiornamenti. Per ulteriori informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true: il riavvio è necessario<br> false: il riavvio non è necessario | Indica se è necessario riavviare il computer per completare l'installazione degli aggiornamenti.
OperationStartTime | Data e ora | Indica l'ora in cui è stata avviata l'operazione (download/installazione).
OperationTime | Data e ora | Indica l'ora in cui è stata completata l'operazione (download/installazione).
HResult | 0-operazione riuscita<br> altro-errore| Indica il motivo dell'errore di Windows Update con codice UpdateID "7392acaf-6a85-427C-8a8d-058c25beb0d6".

Se non è stato ancora pianificato alcun aggiornamento, il file JSON dei risultati è vuoto.

Accedere al cluster per eseguire query Windows Update risultati. Individuare l'indirizzo IP della replica per l'indirizzo principale del servizio Coordinator e aprire l'URL seguente dal browser: http://&lt;REPLICA-IP &gt;: &lt;ApplicationPort &gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

L'endpoint REST per Coordinator Service dispone di una porta dinamica. Per controllare l'URL esatto, vedere Service Fabric Explorer. Ad esempio, i risultati sono disponibili in *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se il proxy inverso è abilitato nel cluster, è possibile accedere anche all'URL dall'esterno del cluster.

L'endpoint che è necessario raggiungere è *http://&lt;SERVERURL &gt;: &lt;REVERSEPROXYPORT &gt;/patchorchestrationapplication/coordinatorservice/V1/getwindowsupdateresults*.

Per abilitare il proxy inverso nel cluster, seguire le istruzioni in [proxy inverso in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Dopo aver configurato il proxy inverso, tutti i microservizi nel cluster che espongono un endpoint HTTP sono indirizzabili dall'esterno del cluster.

## <a name="diagnostics-and-health-events"></a>Diagnostica ed eventi di integrità

Questa sezione illustra come eseguire il debug o diagnosticare i problemi relativi agli aggiornamenti delle patch tramite POA nei cluster Service Fabric.

> [!NOTE]
> Per ottenere molti dei miglioramenti seguenti denominati e di diagnostica automatica, è necessario che sia installato POA versione 1.4.0 o successiva.

Node Agent NTService crea [attività di ripristino](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) per l'installazione degli aggiornamenti nei nodi. Ogni attività viene quindi preparata dal servizio Coordinator in base ai criteri di approvazione delle attività. Infine, le attività preparate vengono approvate da Gestione ripristini, che non approva alcuna attività se il cluster si trova in uno stato non integro. 

Per comprendere il modo in cui gli aggiornamenti vengono eseguiti in un nodo, procediamo con la procedura dettagliata:

1. NodeAgentNTService, in esecuzione in ogni nodo, Cerca gli aggiornamenti di Windows disponibili all'ora pianificata. Se sono disponibili aggiornamenti, vengono scaricati nel nodo.

1. Una volta scaricati gli aggiornamenti, l'agente del nodo NTService crea un'attività di ripristino corrispondente per il nodo denominato *POS___ \<unique_id >* . Per visualizzare le attività di ripristino, è possibile usare il cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) o l'utilizzo di SFX nella sezione dei dettagli del nodo. Al termine della creazione, l'attività di ripristino passa rapidamente allo [stato *richiesto* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Il servizio Coordinator cerca periodicamente le attività di ripristino nello stato *richiesto* e quindi le aggiorna per *preparare* lo stato in base a TaskApprovalPolicy. Se TaskApprovalPolicy è configurato per essere No, un'attività di ripristino corrispondente a un nodo viene preparata solo se nessuna altra attività di ripristino è attualmente in fase di *preparazione*, *approvazione*, *esecuzione*o *ripristino* dello stato. 

   Analogamente, nel caso di UpgradeWise TaskApprovalPolicy, sono presenti attività negli stati precedenti solo per i nodi che appartengono allo stesso dominio di aggiornamento. Quando un'attività di ripristino viene spostata nello stato di *preparazione* , il nodo Service Fabric corrispondente viene [disabilitato](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) con l'Intent set per il *riavvio*.

   POA Versions 1.4.0 e successivi eventi post con la proprietà ClusterPatchingStatus in CoordinatorService per visualizzare i nodi a cui viene applicata la patch. Gli aggiornamenti vengono installati in _poanode_0, come illustrato nell'immagine seguente:

    [![Image dello stato di applicazione delle patch del cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Quando il nodo è disabilitato, l'attività di ripristino viene spostata in stato di *esecuzione* . 
   
   > [!NOTE]
   > Un nodo bloccato nello stato *disabilitato* può bloccare una nuova attività di ripristino, che interrompe l'operazione di applicazione di patch nel cluster.

1. Quando l'attività di ripristino si trova nello stato in *esecuzione* , viene avviata l'installazione della patch in tale nodo. Dopo l'installazione della patch, il nodo potrebbe o non essere riavviato, a seconda della patch. Successivamente, l'attività di ripristino viene spostata nello stato *Restoring* , che consente di riattivare il nodo. L'attività di ripristino viene quindi contrassegnata come completata.

   In POA Versions 1.4.0 e versioni successive, è possibile trovare lo stato dell'aggiornamento visualizzando gli eventi di integrità in NodeAgentService con la proprietà WUOperationStatus-\<NodeName >. Le sezioni evidenziate nelle immagini seguenti mostrano lo stato degli aggiornamenti di Windows nei nodi *poanode_0* e *poanode_2*:

   [![Image dello stato dell'operazione Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Image dello stato dell'operazione Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   È anche possibile ottenere i dettagli usando PowerShell. A tale scopo, connettersi al cluster e recuperare lo stato dell'attività di ripristino usando [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   Nell'esempio seguente, l'attività "POS__poanode_2_125f2969-933c-4774-85D1-ebdf85e79f15" si trova nello stato *DownloadComplete* . Significa che gli aggiornamenti sono stati scaricati nel nodo *poanode_2* e l'installazione verrà tentata quando l'attività passa allo stato in *esecuzione* .

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se vengono rilevati più problemi, accedere alla macchina virtuale (VM) o alle macchine virtuali e ottenere informazioni su di essi usando i registri eventi di Windows. L'attività di ripristino precedentemente indicata può esistere solo nei seguenti sottostati dell'Executor:

      ExecutorSubState | Description
    -- | -- 
      Nessuno = 1 |  Implica che non è stata eseguita un'operazione in corso sul nodo. Lo stato potrebbe essere in transizione.
      DownloadCompleted = 2 | Implica che l'operazione di download è stata completata con esito positivo, errore parziale o errore.
      InstallationApproved = 3 | Implica che l'operazione di download è stata completata in precedenza e Gestione ripristini ha approvato l'installazione.
      InstallationInProgress = 4 | Corrisponde allo stato dell'esecuzione dell'attività di ripristino.
      InstallationCompleted = 5 | Implica che l'installazione è stata completata con esito positivo, esito positivo o negativo.
      RestartRequested = 6 | Implica che l'installazione della patch è stata completata ed è presente un'azione di riavvio in sospeso nel nodo.
      RestartNotNeeded = 7 |  Indica che il riavvio non è necessario dopo il completamento dell'installazione della patch.
      RestartCompleted = 8 | Indica che il riavvio è stato completato correttamente.
      OperationCompleted = 9 | Operazione Windows Update completata correttamente.
      OperationAborted = 10 | Implica che l'operazione di Windows Update è stata interrotta.

1. In POA Versions 1.4.0 e versioni successive, quando viene completato un tentativo di aggiornamento del nodo, viene pubblicato un evento con la proprietà "WUOperationStatus-[nodename]" su NodeAgentService per ricevere una notifica quando viene avviato il tentativo successivo di scaricare e installare gli aggiornamenti di Windows. Viene visualizzato nell'immagine seguente:

     [![Image dello stato dell'operazione Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Log di diagnostica

I log di patch Orchestration Application vengono raccolti come parte dei log di Runtime Service Fabric.

È possibile acquisire i log usando lo strumento di diagnostica o la pipeline di propria scelta. POA usa i seguenti ID provider fissi per registrare gli eventi tramite l' [origine evento](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Report sull'integrità

POA pubblica inoltre i report sull'integrità nel servizio node Agent o nel servizio Coordinator negli scenari seguenti:

* Il Node Agent NTService è inattivo

   Se il Node Agent NTService è inattivo in un nodo, viene generato un report sull'integrità dei livelli di avviso per il Node Agent Service.

* Il servizio Gestione ripristini non è abilitato

   Se il servizio Gestione ripristini non viene trovato nel cluster, viene generato un report sull'integrità a livello di avviso per il Coordinator Service.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: perché viene visualizzato lo stato di errore del cluster quando POA è in esecuzione?**

R: durante il processo di installazione, POA Disabilita o riavvia i nodi, che possono causare temporaneamente un cluster non integro.

A seconda dei criteri dell'applicazione, un nodo può arrestarsi durante un'operazione di applicazione di patch *oppure* un intero dominio di aggiornamento può essere disattivato in una sola volta.

Al termine dell'installazione degli aggiornamenti di Windows, i nodi vengono riabilitati dopo il riavvio.

Nell'esempio seguente il cluster è passato temporaneamente a uno stato di errore perché due nodi erano inattivi e il criterio MaxPercentageUnhealthNodes è stato violato. L'errore è temporaneo fino a quando non è possibile avviare l'operazione di applicazione di patch.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi.

**D: cosa è possibile fare se il POA è in uno stato di avviso?**

R: verificare se un rapporto di stato pubblicato sull'applicazione indica la causa principale. L'avviso in genere contiene i dettagli del problema. Se il problema è temporaneo, è previsto che l'applicazione venga ripristinata automaticamente.

**D: cosa è possibile fare se il cluster non è integro ed è necessario eseguire un aggiornamento urgente del sistema operativo?**

R: POA non installa gli aggiornamenti mentre il cluster non è integro. Provare a portare il cluster in uno stato integro per sbloccare il flusso di lavoro POA.

**D: è necessario impostare TaskApprovalPolicy come "No" o "UpgradeDomainWise" per il cluster?**

R: l'impostazione "UpgradeDomainWise" accelera la correzione complessiva del cluster tramite l'applicazione di patch in parallelo a tutti i nodi appartenenti a un dominio di aggiornamento. Durante il processo, i nodi che appartengono a un intero dominio di aggiornamento non sono disponibili (in [stato *disabilitato* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Al contrario, l'impostazione "No," applica patch solo a un nodo alla volta, il che implica che l'applicazione di patch del cluster complessiva potrebbe richiedere più tempo. Tuttavia, durante il processo di applicazione di patch solo un nodo non sarà più disponibile (in stato *disabilitato* ).

Se il cluster può tollerare l'esecuzione in un numero N-1 di domini di aggiornamento durante il ciclo di applicazione (dove N è il numero totale di domini di aggiornamento nel cluster), è possibile impostare il criterio come "UpgradeDomainWise". In caso contrario, impostarlo su "No".

**D: quanto tempo è necessario per applicare una patch a un nodo?**

R: l'applicazione di patch a un nodo può richiedere da pochi minuti (ad esempio, [gli aggiornamenti delle definizioni di Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) a ore, ad esempio [gli aggiornamenti cumulativi di Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update). Il tempo necessario per applicare la patch a un nodo dipende principalmente da: 
 - Dimensioni degli aggiornamenti.
 - Il numero di aggiornamenti che devono essere applicati in una finestra di applicazione di patch.
 - Il tempo necessario per installare gli aggiornamenti, riavviare il nodo (se necessario) e completare la procedura di installazione successiva al riavvio.
 - Le prestazioni della macchina virtuale o del computer e delle condizioni della rete.

**D: quanto tempo è necessario per applicare la patch a un intero cluster?**

R: il tempo necessario per applicare una patch a un intero cluster dipende da:

- Tempo necessario per applicare la patch a un nodo.

- I criteri del Coordinator Service. Il criterio predefinito, "No,", comporta l'applicazione di patch solo a un nodo alla volta, un approccio più lento rispetto all'uso di "UpgradeDomainWise". 

   Ad esempio, se un nodo richiede circa 1 ora per l'applicazione di patch, l'applicazione di patch a un cluster da 20 nodi (stesso tipo di nodi) con 5 domini di aggiornamento, ognuno dei quali contiene 4 nodi, richiede:
    - Per "No,": ~ 20 ore.
    - Per "UpgradeDomainWise": ~ 5 ore.

- Carico del cluster. Ogni operazione di applicazione di patch richiede il trasferimento del carico di lavoro del cliente ad altri nodi disponibili nel cluster. Un nodo di cui è in corso l'applicazione di patch si trova nello [stato *disabilitato* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante questo periodo di tempo. Se il cluster è in esecuzione in prossimità del picco del carico, il processo di disabilitazione richiederà più tempo. Pertanto, il processo di applicazione di patch globale potrebbe sembrare lento in condizioni di questo tipo.

- Errori di integrità del cluster durante l'applicazione di patch. Qualsiasi [riduzione](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) dell' [integrità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interrompe il processo di applicazione delle patch. Questo problema può essere aggiunto al tempo complessivo necessario per applicare la patch all'intero cluster.

**D: perché vengono visualizzati alcuni aggiornamenti nei risultati Windows Update ottenuti tramite l'API REST, ma non nella cronologia Windows Update del computer?**

R: alcuni aggiornamenti del prodotto vengono visualizzati solo nella relativa cronologia di aggiornamento o patch. Ad esempio, è possibile che gli aggiornamenti di Windows Defender non vengano visualizzati nella cronologia Windows Update in Windows Server 2016.

**D: è possibile usare POA per applicare patch al cluster di sviluppo (cluster a un nodo)?**

R: No, non è possibile usare POA per applicare patch a un cluster a un nodo. Questa limitazione è dovuta alla progettazione, perché [Service Fabric servizi di sistema](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) o altre app del cliente potrebbero causare tempi di inattività. Pertanto, l'applicazione di patch ai processi di ripristino non verrà mai approvata da Gestione ripristini.

**D: Ricerca per categorie applicare patch ai nodi del cluster in Linux?**

R: per informazioni su come orchestrare gli aggiornamenti in Linux, vedere [aggiornamenti automatici delle immagini del sistema operativo del set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**D: perché il ciclo di aggiornamento richiede molto tempo?**

R: eseguire una query per il risultato JSON, immettere il ciclo di aggiornamento per tutti i nodi e quindi provare a individuare il tempo impiegato dall'installazione dell'aggiornamento in ogni nodo usando OperationStartTime e OperationTime (OperationCompletionTime). 

Se è presente un intervallo di tempo di grandi dimensioni in cui non si verifica alcun aggiornamento, il cluster potrebbe trovarsi in uno stato di errore e, di conseguenza, Gestione ripristini non può approvare le attività di ripristino del POA. Se l'installazione dell'aggiornamento sta richiedendo molto tempo su qualsiasi nodo, è possibile che il nodo non sia stato aggiornato in un periodo di tempo. Un numero elevato di aggiornamenti potrebbe essere un'installazione in sospeso, che può causare ritardi. 

Potrebbe anche essere possibile che l'applicazione di patch del nodo sia bloccata perché è bloccata nello stato *disabilitato* . Questo problema si verifica in genere perché la disabilitazione del nodo può causare situazioni di quorum o di perdita dei dati.

**D: perché il nodo deve essere disabilitato quando POA esegue l'applicazione di patch?**

R: POA Disabilita il nodo con una finalità di *riavvio* , che interrompe o rialloca tutti i servizi Service fabric in esecuzione nel nodo. POA esegue questa operazione per garantire che le applicazioni non finiscano con una combinazione di dll nuove e obsolete, pertanto è consigliabile non applicare l'applicazione di patch a un nodo senza disabilitarlo.

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- POA accetta il contratto di licenza con l'utente finale per Windows Update per conto dell'utente. Facoltativamente, l'impostazione può essere disattivata nella configurazione dell'applicazione.

- POA raccoglie i dati di telemetria per tenere traccia dell'utilizzo e delle prestazioni. I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric (attiva per impostazione predefinita).

## <a name="troubleshooting"></a>risoluzione dei problemi

In questa sezione vengono fornite le possibili soluzioni per la risoluzione dei problemi relativi ai nodi patch.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nodo non torna allo stato attivo

* Il nodo potrebbe essere bloccato nello stato *disabilitato* perché:

  - Un controllo di sicurezza è in sospeso. Per risolvere questo problema, assicurarsi che siano disponibili sufficienti nodi in uno stato di integrità.

* Il nodo potrebbe essere bloccato nello stato *disabilitato* perché:

  - È stato disabilitato manualmente.
  - È stato disabilitato a causa di un processo di infrastruttura di Azure in corso.
  - È stata disabilitata temporaneamente da POA per applicare la patch al nodo.

* Il nodo potrebbe essere bloccato in uno stato di inattività perché:

  - È stato inserito manualmente in uno stato inattivo.
  - È in corso il riavvio (che potrebbe essere attivato da POA).
  - Ha una macchina virtuale o un computer difettoso o problemi di connettività di rete.

### <a name="updates-were-skipped-on-some-nodes"></a>Gli aggiornamenti sono stati ignorati in alcuni nodi

POA tenta di installare un aggiornamento di Windows in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, viene generato un report sull'integrità dei livelli di avviso per Node Agent Service. Il risultato della Windows Update contiene anche il possibile motivo dell'errore.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>L'integrità del cluster passa ad errore durante l'installazione dell'aggiornamento

Un aggiornamento di Windows difettoso può comportare l'integrità di un'applicazione o di un cluster in un nodo o un dominio di aggiornamento specifico. POA interrompe le successive operazioni di Windows Update fino a quando il cluster non è integro.

Un amministratore deve intervenire e stabilire perché l'applicazione o il cluster è diventato non integro a causa di Windows Update.

## <a name="poa-release-notes"></a>Note sulla versione di POA

>[!NOTE]
> Per le versioni di POA 1.4.0 e versioni successive, è possibile trovare le note sulla versione e le versioni nella [pagina della versione di patch Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/) su GitHub.

### <a name="version-110"></a>Versione 1.1.0
- Versione pubblica

### <a name="version-111"></a>Versione 1.1.1
- Correzione di un bug in SetupEntryPoint di NodeAgentService che ha impedito l'installazione di NodeAgentNTService.

### <a name="version-120"></a>Versione 1.2.0

- Correzioni di bug nel flusso di lavoro di riavvio del sistema.
- Correzione di bug nella creazione di attività RM a causa delle quali il controllo dell'integrità durante la preparazione delle attività di ripristino non ha avuto luogo come previsto.
- Modificare la modalità di avvio per il servizio Windows POANodeSvc da automatico a ritardato-auto.

### <a name="version-121"></a>Versione 1.2.1

- Correzione di bug nel flusso di lavoro di riduzione delle prestazioni del cluster. È stata introdotta la logica di Garbage Collection per le attività di riparazione di Patch Orchestration Application relative a nodi inesistenti.

### <a name="version-122"></a>Versione 1.2.2

- Varie correzioni di bug.
- I file binari sono ora firmati.
- Aggiunta del collegamento a sfpkg per l'applicazione.

### <a name="version-130"></a>Versione 1.3.0

- L'impostazione di InstallWindowsOSOnlyUpdates su false consente di installare tutti gli aggiornamenti disponibili.
- Modifica della logica per disabilitare gli aggiornamenti automatici. Questa correzione risolve un bug che impediva la disabilitazione degli aggiornamenti automatici nei sistemi Server 2016 e versioni successive.
- Vincolo di posizionamento con parametri per i microservizi di POA per i casi d'uso avanzati.

### <a name="version-131"></a>Versione 1.3.1
- Correzione della regressione in cui POA 1.3.0 non funziona in Windows Server 2012 R2 o versioni precedenti a causa di un errore durante la disabilitazione degli aggiornamenti automatici. 
- Correzione di un bug per cui la configurazione di InstallWindowsOSOnlyUpdates risulta sempre true.
- Modifica del valore predefinito di InstallWindowsOSOnlyUpdates su False.

### <a name="version-132"></a>Versione 1.3.2
- Correzione di un problema che influisca sul ciclo di vita dell'applicazione di patch in un nodo, se sono presenti nodi con un nome che è un subset del nome del nodo corrente. Per tali nodi è possibile che l'applicazione di patch sia stata persa o che sia in sospeso un riavvio.