---
title: Applicare patch al sistema operativo Windows nel cluster di Service Fabric
description: In questo articolo viene illustrato come automatizzare l'applicazione del sistema operativo in un cluster di Service Fabric utilizzando Patch Orchestration Application.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366329"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Applicare patch al sistema operativo Windows nel cluster di Service Fabric

> 
> [!IMPORTANT]
> A partire dal 30 aprile 2019, patch Orchestration Application versione 1.2. non è più supportata. Assicurarsi di eseguire l'aggiornamento alla versione più recente.

> [!NOTE]
> Ottenere aggiornamenti automatici delle immagini del sistema operativo nel set di scalabilità delle macchine virtuali è la procedura consigliata per mantenere il sistema operativo aggiornato in Azure.Getting [automatic OS image upgrades on your virtual machine scale set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) is the best practice for keeping your operating system patched in Azure. Gli aggiornamenti automatici delle immagini del sistema operativo basati sul set di scalabilità virtuale richiederanno una durata argento o maggiore in un set di scalabilità.
>

 Patch Orchestration Application (POA) è un wrapper per il servizio Azure Service Fabric Repair Manager, che consente la pianificazione delle patch del sistema operativo basata sulla configurazione per i cluster ospitati non di Azure.Patch Orchestration Application (POA) is a wrapper around the Azure Service Fabric Repair Manager service, which enables configuration-based OS patch scheduling for non-Azure hosted clusters. POA non è necessario per i cluster ospitati non di Azure, ma la pianificazione dell'installazione delle patch per dominio di aggiornamento è necessaria per applicare la patch agli host cluster di Service Fabric senza incorrere in tempi di inattività.

POA è un'applicazione Service Fabric che automatizza l'applicazione di patch del sistema operativo in un cluster di Service Fabric senza incorrere in tempi di inattività.

POA offre le seguenti funzionalità:

- **Installazione automatica dell'aggiornamento del sistema operativo**. Gli aggiornamenti del sistema operativo vengono scaricati e installati automaticamente. I nodi del cluster vengono riavviati in base alle esigenze senza dover sostenere i tempi di inattività del cluster.

- **Integrazione dell'integrità e l'applicazione di patch compatibile con il cluster**. Mentre POA applica gli aggiornamenti, monitora l'integrità dei nodi del cluster. I nodi del cluster vengono aggiornati un nodo alla volta o un dominio di aggiornamento alla volta. Se l'integrità del cluster si arresta a causa del processo di applicazione delle patch, l'applicazione di patch viene interrotta per evitare di aggravare il problema.

## <a name="internal-details-of-poa"></a>Dettagli interni di POA

POA è composto dai seguenti sottocomponenti:

- **Coordinator Service**: il servizio con stato è responsabile per:
    - Il coordinamento del processo di Windows Update nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di Windows Update.

- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi di cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap del Node Agent NTService.
    - Il monitoraggio di Node Agent NTService

- **Node agent NTService**: questo servizio di Windows NT viene eseguito con un privilegio di livello superiore (SYSTEM). Il Node Agent Service e il Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore (NETWORK SERVICE). Il servizio è responsabile dell'esecuzione dei seguenti processi di Windows Update in tutti i nodi del cluster:
    - Disabilitazione degli aggiornamenti automatici di Windows sul nodo.
    - Download e installazione degli aggiornamenti di Windows in base ai criteri forniti dall'utente.
    - Riavviare l'installazione degli aggiornamenti del computer dopo Windows.
    - Caricamento dei risultati di Windows Update nel Coordinator Service.
    - Segnalazione dei report di integrità se un'operazione non è riuscita dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> POA utilizza il servizio Service Fabric Repair Manager per disabilitare o abilitare il nodo ed eseguire controlli di integrità. L'attività di ripristino creata da POA tiene traccia dello stato di avanzamento di Windows Update per ogni nodo.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> La versione minima di .NET Framework richiesta è 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Abilitare il servizio Gestione riparazioni (se non è già in esecuzione)

POA richiede che il servizio Repair Manager sia abilitato nel cluster.

#### <a name="azure-clusters"></a>Cluster di Azure

I cluster di Azure nel livello di durabilità argento hanno il servizio Repair Manager abilitato per impostazione predefinita. I cluster di Azure nel livello di durabilità dell'oro potrebbero abilitare o meno il servizio Gestione riparazioni, a seconda di quando sono stati creati. I cluster di Azure nel livello di durabilità del bronzo, per impostazione predefinita, non hanno attivato il servizio Gestione riparazioni. Se il servizio è già abilitato, è possibile visualizzarlo in esecuzione nella sezione dei servizi di sistema in Service Fabric Explorer.If the service is already enabled, you can see it running in the system services section in Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Portale di Azure
È possibile abilitare Gestione riparazioni dal portale di Azure quando si configura il cluster. Quando si sta configurando il cluster, selezionare l'opzione **Includi Gestione riparazioni** in **Funzionalità aggiuntive**.

![Immagine dell'abilitazione di Repair Manager dal portale di Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Modello di distribuzione di Azure Resource ManagerThe Azure Resource Manager deployment model
In alternativa, è possibile usare il modello di distribuzione di [Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio Gestione riparazioni nei cluster dell'infrastruttura di servizio nuovi ed esistenti. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i modelli di esempio o creare un modello di distribuzione Azure Resource Manager personalizzato. 

Per abilitare il servizio Gestione procedure di ripristino usando il modello di distribuzione di [Azure Resource Manager,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)eseguire le operazioni seguenti:

1. Verificare che `apiVersion` sia impostato su *2017-07-01-anteprima* per la risorsa *Microsoft.ServiceFabric/clusters.* Se è diverso, è necessario `apiVersion` eseguire l'aggiornamento a *2017-07-01-preview* o versione successiva:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Abilitare il servizio Gestione `addonFeatures` riparazioni `fabricSettings` aggiungendo la sezione seguente dopo la sezione:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Dopo aver aggiornato il modello di cluster con queste modifiche, applicarle e lasciare che l'aggiornamento venga completato. È ora possibile visualizzare il servizio Gestione ripristino in esecuzione nel cluster. Si chiama *fabric:/System/RepairManagerService* nella sezione dei servizi di sistema in Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Cluster locali autonomi

Per abilitare il servizio Gestione riparazioni in un cluster di infrastruttura del servizio nuovo o esistente, è possibile utilizzare le impostazioni di configurazione per il [cluster Windows autonomo.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)

Per attivare il servizio Repair Manager:

1. Verificare che `apiVersion` in [Configurazioni cluster generali](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) sia impostato su *04-2017* o versione successiva, come illustrato di seguito:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Abilitare il servizio Gestione `addonFeatures` riparazioni `fabricSettings` aggiungendo la sezione seguente dopo la sezione, come illustrato di seguito:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Aggiornare il manifesto del cluster con queste modifiche utilizzando il manifesto del cluster aggiornato [per creare un nuovo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) o [aggiornare la configurazione del cluster.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server) 

   Dopo che il cluster è in esecuzione con un manifesto del cluster aggiornato, è possibile visualizzare il servizio Gestione ripristino in esecuzione nel cluster. Si chiama *fabric:/System/RepairManagerService*ed è nella sezione dei servizi di sistema in Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurare gli aggiornamenti di Windows per tutti i nodi

Gli aggiornamenti automatici di Windows potrebbero causare una perdita di disponibilità, perché più nodi del cluster possono essere riavviati contemporaneamente. POA, per impostazione predefinita, tenta di disabilitare gli aggiornamenti automatici di Windows in ogni nodo del cluster. Tuttavia, se le impostazioni sono gestite da un amministratore o da Criteri di gruppo, è consigliabile impostare il criterio di Windows Update su "Notifica prima del download" in modo esplicito.

## <a name="download-the-application-package"></a>Scaricare il pacchetto dell'applicazioneDownload the application package

Per scaricare il pacchetto dell'applicazione, passare alla pagina di [rilascio dell'applicazione di orchestrazione di patch](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) in GitHub.To download the application package, go to the Patch Orchestration Application release page on GitHub.

## <a name="configure-poa-behavior"></a>Configurare il comportamento POA

È possibile configurare il comportamento POA in base alle proprie esigenze. Eseguire l'override dei valori predefiniti passando il parametro dell'applicazione durante la creazione o l'aggiornamento dell'applicazione. È possibile fornire i `ApplicationParameter` parametri `Start-ServiceFabricApplicationUpgrade` dell'applicazione specificando i cmdlet o `New-ServiceFabricApplication` .

| Parametro        | Type                          | Dettagli |
|:-|-|-|
|MaxResultsToCache    |long                              | Numero massimo di risultati di Windows Update che devono essere memorizzati nella cache. <br><br>Il valore predefinito è 3000, presupponendo che: <br> &nbsp;&nbsp;- Il numero di nodi è 20. <br> &nbsp;&nbsp;- Il numero di aggiornamenti a un nodo al mese è 5.- The number of updates to a node per month is 5. <br> &nbsp;&nbsp;- Il numero di risultati per operazione può essere 10. <br> &nbsp;&nbsp;- I risultati degli ultimi tre mesi devono essere conservati. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati dal Coordinator Service per installare gli aggiornamenti di Windows Update nei nodi del cluster di Service Fabric.<br><br>Di seguito sono elencati i valori consentiti: <br>*NodeWise*: gli aggiornamenti di Windows vengono installati un nodo alla volta. <br> *UpgradeDomainWise*: gli aggiornamenti di Windows vengono installati un dominio di aggiornamento alla volta. (Al massimo, tutti i nodi appartenenti a un dominio di aggiornamento possono andare per un aggiornamento di Windows.)<br><br> Per decidere quale criterio è più adatto per il cluster, vedere la sezione [Domande frequenti.](#frequently-asked-questions)
|LogsDiskQuotaInMB   |long  <br> (Impostazione predefinita: *1024*)               | La dimensione massima dei log dell'app di orchestrazione delle patch in MB, che possono essere resi persistenti localmente nei nodi.
| WUQuery               | string<br>(Impostazione predefinita: *IsInstalled -0*)                | Eseguire una query per ottenere gli aggiornamenti di Windows. Per altre informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (impostazione predefinita: false)                 | Usare questo flag per controllare quali aggiornamenti devono essere scaricati e installati. Sono consentiti i valori seguenti: <br>true: installa solo gli aggiornamenti del sistema operativo Windows.<br>false: installa tutti gli aggiornamenti disponibili nel computer.          |
| WUOperationTimeOutInMinutes | Int <br>(Impostazione predefinita: *90*)                   | Specifica il timeout per qualsiasi operazione di Windows Update (ricerca, download o installazione). L'operazione viene interrotta se non viene completata entro il timeout specificato.       |
| WURescheduleCount     | Int <br> (Impostazione predefinita: *5*)                  | Numero massimo di volte in cui il servizio ripianifica l'aggiornamento di Windows se un'operazione non riesce in modo permanente.          |
| WURescheduleTimeInMinutes | Int <br>(Impostazione predefinita: *30*) | Intervallo in cui il servizio ripianifica gli aggiornamenti di Windows se l'errore persiste. |
| WUFrequency           | Stringa delimitata da virgole (impostazione predefinita: *settimanale, mercoledì, 7:00:00*)     | Frequenza di installazione degli aggiornamenti di Windows. Il formato e i valori possibili sono: <br>&nbsp;&nbsp;- Mensile: DD, HH:MM:SS (ad esempio, *Mensile, 5,12:22:32*)<br>I valori consentiti per il campo Gg (giorno) sono numeri compresi tra 1 e 28 e "ultimo". <br> &nbsp;&nbsp;- Settimanale, GIORNO, HH:MM:SS (ad esempio, *Settimanale, Martedì, 12:22:32*)  <br> &nbsp;&nbsp;- Giornaliero, HH:MM:SS (ad esempio, *Giornaliero, 12:22:32*)  <br> &nbsp;&nbsp;-  *Nessuno* indica che gli aggiornamenti di Windows non devono essere eseguiti.  <br><br> Gli orari sono in UTC.|
| AcceptWindowsUpdateEula | Boolean <br>(Impostazione predefinita: *true*) | Impostando questo flag, l'applicazione accetta il contratto di licenza dell'utente finale per Windows Update per conto del proprietario della macchina.              |

> [!TIP]
> Se si desidera che gli `WUFrequency` aggiornamenti di Windows vengano eseguiti immediatamente, impostare in relazione al tempo di distribuzione dell'applicazione. Ad esempio, si supponga di disporre di un cluster di test a cinque nodi e si prevede di distribuire l'app all'incirca alle 17:00:00 UTC. Se si presuppone che l'aggiornamento o la distribuzione dell'applicazione risulti di un'operazione di installazione per 30 minuti, impostare WUFrequency su *Daily, 17:30:00*.

## <a name="deploy-poa"></a>Distribuire POADeploy POA

1. Completare tutti i passaggi preliminari per preparare il cluster.
1. Distribuire POA come qualsiasi altra app di Service Fabric. Per distribuirlo tramite PowerShell, vedere [Distribuire e rimuovere applicazioni tramite PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Per configurare l'applicazione al momento della distribuzione, passare `ApplicationParameter` al cmdlet `New-ServiceFabricApplication`. Per praticità, è stato fornito lo script Deploy.ps1 insieme all'applicazione. Per usare lo script:

    - Connettersi a un cluster di Service Fabric tramite `Connect-ServiceFabricCluster`.
    - Eseguire lo script Deploy.ps1 di PowerShell con il valore `ApplicationParameter` appropriato.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione *PatchOrchestrationApplication* nella stessa directory.

## <a name="upgrade-poa"></a>Aggiornamento POA

Per aggiornare la versione POA tramite PowerShell, seguire le istruzioni [nell'aggiornamento dell'applicazione Service Fabric tramite PowerShell.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)

## <a name="remove-poa"></a>Rimuovi POA

Per rimuovere l'applicazione, seguire le istruzioni in [Distribuire e rimuovere applicazioni tramite PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Per comodità, abbiamo fornito lo script Undeploy.ps1 insieme all'applicazione. Per usare lo script:

  - Connettersi a un cluster di Service Fabric tramite ```Connect-ServiceFabricCluster```.
  - Eseguire lo script Undeploy.ps1 di PowerShell.

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione *PatchOrchestrationApplication* nella stessa directory.

## <a name="view-the-windows-update-results"></a>Visualizzare i risultati di Windows Update

POA espone le API REST per visualizzare i risultati cronologici agli utenti. Ecco un esempio del risultato JSON:Here's an example of the result JSON:

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
Tipo operazione | 1 - Installazione<br> 0 - Ricerca e download| Per impostazione predefinita, l'installazione è l'unico OperationType visualizzato nei risultati.
WindowsUpdateQuery | Impostazione predefinita: "IsInstalled = 0" | Query di Windows Update utilizzata per la ricerca di aggiornamenti. Per ulteriori informazioni, vedere [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true: il riavvio è necessario<br> false: il riavvio non è necessario | Indica se è stato necessario un riavvio per completare l'installazione degli aggiornamenti.
OperazioneInizioOra | Datetime | Indica l'ora di avvio dell'operazione (Download/Installazione).
Tempo funzionamento | Datetime | Indica l'ora in cui è stata completata l'operazione(Download/Installazione).
HResult | 0 - Operazione riuscita<br> altro - fallimento| Indica il motivo dell'errore dell'aggiornamento di Windows con updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se non è stato ancora pianificato alcun aggiornamento, il file JSON dei risultati è vuoto.

Accedere al cluster per eseguire una query sui risultati di Windows Update. Individuare l'indirizzo IP di replica per l'indirizzo primario del servizio&lt;Coordinatore&gt;e&lt;aprire&gt;l'URL seguente dal browser: http:// REPLICA-IP : ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

L'endpoint REST per Coordinator Service dispone di una porta dinamica. Per verificare l'URL esatto, fare riferimento a Service Fabric Explorer. Ad esempio, i risultati *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*sono disponibili all'indirizzo .

![Immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se il proxy inverso è abilitato nel cluster, è possibile accedere all'URL anche dall'esterno del cluster.

L'endpoint che è necessario raggiungere è *http://&lt;SERVERURL&gt;&lt;: REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Per abilitare il proxy inverso nel cluster, seguire le istruzioni in [Proxy inverso in Azure Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 

> 
> [!WARNING]
> Dopo aver configurato il proxy inverso, tutti i microservizi del cluster che espongono un endpoint HTTP sono indirizzabili dall'esterno del cluster.

## <a name="diagnostics-and-health-events"></a>Diagnostica ed eventi di integrità

In questa sezione viene illustrato come eseguire il debug o diagnosticare i problemi relativi agli aggiornamenti delle patch tramite POA nei cluster di Service Fabric.

> [!NOTE]
> Per ottenere molti dei seguenti miglioramenti di chiamata, auto-diagnostica, è necessario avere POA versione 1.4.0 o successiva installato.

L'agente di nodo NTService crea attività di [ripristino](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) per l'installazione degli aggiornamenti nei nodi. Ogni attività viene quindi preparata dal servizio coordinatore in base ai criteri di approvazione dell'attività. Infine, le attività preparate vengono approvate da Gestione riparazioni, che non approva alcuna attività se il cluster è in uno stato non integro. 

Per comprendere come procedono gli aggiornamenti su un nodo, procediamo passo dopo passo:

1. NodeAgentNTService, in esecuzione su ogni nodo, cerca gli aggiornamenti di Windows disponibili all'ora pianificata. Se sono disponibili aggiornamenti, vengono scaricati nel nodo.

1. Dopo il download degli aggiornamenti, l'agente nodo NTService crea un'attività di ripristino corrispondente per il nodo con il nome *POS___\<unique_id>*. È possibile visualizzare queste attività di ripristino utilizzando il cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) o SFX nella sezione dei dettagli del nodo. Dopo aver creato l'attività di ripristino, passa rapidamente allo stato [ *Rivendicato* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Il servizio Coordinatore cerca periodicamente le attività di riparazione nello stato *Rivendicato* e quindi le aggiorna allo stato *Preparazione* in base a TaskApprovalPolicy. Se TaskApprovalPolicy è configurato per essere NodeWise, un'attività di ripristino che corrisponde a un nodo viene preparata solo se nessun'altra attività di ripristino è attualmente in fase di *preparazione*, *Approvato*, *Esecuzione*o *ripristino.* 

   Analogamente, nel caso di UpgradeWise TaskApprovalPolicy, sono presenti attività negli stati precedenti solo per i nodi che appartengono allo stesso dominio di aggiornamento. Dopo che un'attività di ripristino viene spostata nello stato *Preparazione,* il nodo dell'infrastruttura del servizio corrispondente viene [disabilitato](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) con la finalità impostata su *Riavvia*.

   POA versioni 1.4.0 e successive pubblicano eventi con la proprietà ClusterPatchingStatus in CoordinatorService per visualizzare i nodi a cui viene noto la patch. Gli aggiornamenti vengono installati in _poanode_0, come illustrato nell'immagine seguente:

    [![Immagine dello stato di applicazione delle patch del cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Dopo che il nodo è stato disabilitato, l'attività di ripristino viene spostata nello stato *In esecuzione.* 
   
   > [!NOTE]
   > Un nodo bloccato nello stato *Disabilitato* può bloccare una nuova attività di ripristino, che interrompe l'operazione di applicazione delle patch nel cluster.

1. Quando l'attività di ripristino è in *stato di esecuzione,* viene avviata l'installazione della patch in tale nodo. Dopo aver installato la patch, il nodo potrebbe essere riavviato o meno, a seconda della patch. Successivamente, l'attività di ripristino viene spostata nello stato *di ripristino,* che riabilita il nodo. L'attività di riparazione viene quindi contrassegnata come completata.

   Nelle versioni POA 1.4.0 e successive, è possibile trovare lo stato dell'aggiornamento visualizzando\<gli eventi di integrità in NodeAgentService con la proprietà WUOperationStatus- NodeName>. Le sezioni evidenziate nelle immagini seguenti mostrano lo stato degli aggiornamenti di Windows nei nodi *poanode_0* e *poanode_2:*

   [![Immagine dello stato dell'operazione di Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Immagine dello stato dell'operazione di Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   È anche possibile ottenere i dettagli tramite PowerShell.You can also get the details by using PowerShell. A tale scopo, connettersi al cluster e recuperare lo stato dell'attività di ripristino utilizzando [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   Nell'esempio seguente, l'attività "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" è nello stato *DownloadComplete.* Ciò significa che gli aggiornamenti sono stati scaricati sul nodo *poanode_2* e l'installazione verrà tentata quando l'attività passa allo stato *Di sinstallazione.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se rimangono altri problemi, accedere alla macchina virtuale (VM) o alle macchine virtuali e ottenere informazioni su di esse tramite i registri eventi di Windows.If more issues remain to be found, sign in to your virtual machine (VM) or VMs and learn about them by using Windows event logs. L'attività di ripristino menzionata in precedenza può esistere solo nei seguenti sottostati dell'executor:

      ExecutorSubState | Descrizione
    -- | -- 
      Nessuno: 1 |  Si implica che non c'era un'operazione in corso sul nodo. Lo stato potrebbe essere in transizione.
      DownloadCompletato 2 | Implica che l'operazione di download è stata completata con esito positivo, errore parziale o errore.
      InstallazioneApprovato: 3 | Il vale che l'operazione di download è stata completata in precedenza e Repair Manager ha approvato l'installazione.
      InstallationInProgress: 4 | Corrisponde allo stato di esecuzione dell'attività di ripristino.
      Installazione completata: 5 | Implica che l'installazione è stata completata con esito positivo, parziale o errore.
      Richiesta di riavvio 6 | Implica che l'installazione della patch è stata completata e c'è un'azione di riavvio in sospeso sul nodo.
      Riavvio Non Necessario 7 |  Significa che il riavvio non era necessario dopo il completamento dell'installazione della patch.
      RestartCompleted: 8 | Implica che il riavvio è stato completato correttamente.
      Operazione Completata : 9 | L'operazione di Windows Update è stata completata correttamente.
      OperazioneInterrotta 10 | Implica che l'operazione di Windows Update è stata interrotta.

1. Nelle versioni POA 1.4.0 e successive, al termine di un tentativo di aggiornamento del nodo, viene inviato un evento con la proprietà "WUOperationStatus-[NodeName]" su NodeAgentService per notificare quando inizierà il successivo tentativo di scaricare e installare gli aggiornamenti di Windows. Viene visualizzato nell'immagine seguente:This is displayed in the following image:

     [![Immagine dello stato dell'operazione di Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Log di diagnostica

I log dell'applicazione di orchestrazione delle patch vengono raccolti come parte dei log di runtime di Service Fabric.Patch orchestration application logs are collected as part of the Service Fabric runtime logs.

È possibile acquisire i log utilizzando lo strumento di diagnostica o la pipeline desiderata. POA utilizza i seguenti ID provider fissi per registrare gli eventi tramite [l'origine evento](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Report sull'integrità

POA pubblica inoltre i report sull'integrità nel servizio Agente nodo o nel servizio Coordinatore nei seguenti scenari:

* Il Node Agent NTService è inattivo

   Se il Node Agent NTService è inattivo in un nodo, viene generato un report sull'integrità dei livelli di avviso per il Node Agent Service.

* Il servizio Repair Manager non è abilitato

   Se il servizio Repair Manager non viene trovato nel cluster, viene generato un rapporto di integrità a livello di avviso per il servizio Coordinatore.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Perché il cluster viene visualizzato in stato di errore quando POA è in esecuzione?**

R: Durante il processo di installazione, POA disabilita o riavvia i nodi, il che può causare temporaneamente un cluster non integro.

A seconda dei criteri dell'applicazione, uno dei nodi può essere attivo durante un'operazione di applicazione delle patch *o* un intero dominio di aggiornamento può essere attivo contemporaneamente.

Al termine dell'installazione degli aggiornamenti di Windows, i nodi vengono riabilitati dopo il riavvio.

Nell'esempio seguente il cluster è passato temporaneamente a uno stato di errore perché due nodi erano inattivi e il criterio MaxPercentageUnhealthNodes è stato violato. L'errore è temporaneo fino all'inizio dell'operazione di applicazione delle patch.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi.

**D: Cosa posso fare se POA è in uno stato di avviso?**

R: Verificare se un rapporto di integrità inviato rispetto all'applicazione indica la causa principale. L'avviso in genere contiene i dettagli del problema. Se il problema è temporaneo, si prevede che l'applicazione venga ripristinata automaticamente.

**D: Cosa è possibile fare se il cluster non è integro ed è necessario eseguire un aggiornamento urgente del sistema operativo?**

R: POA non installa gli aggiornamenti mentre il cluster non è integro. Provare a portare il cluster a uno stato integro per sbloccare il flusso di lavoro POA.

**D: È necessario impostare TaskApprovalPolicy come "NodeWise" o "UpgradeDomainWise" per il cluster?**

R: L'impostazione "UpgradeDomainWise" velocizza il ripristino complessivo del cluster applicando patch in parallelo a tutti i nodi che appartengono a un dominio di aggiornamento. Durante il processo, i nodi che appartengono a un intero dominio di aggiornamento non sono disponibili (nello stato [ *Disabilitato* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Al contrario, l'impostazione "NodeWise" applica patch a un solo nodo alla volta, il che implica che l'applicazione di patch complessive del cluster potrebbe richiedere più tempo. Tuttavia, solo un nodo al massimo non sarebbe disponibile (nello stato *Disabilitato)* durante il processo di applicazione delle patch.

Se il cluster può tollerare l'esecuzione su un numero N-1 di domini di aggiornamento durante il ciclo di applicazione delle patch (dove N è il numero totale di domini di aggiornamento nel cluster) è possibile impostare il criterio come "UpgradeDomainWise". In caso contrario, impostarlo su "NodeWise".

**D: Quanto tempo è necessario per applicare la patch a un nodo?**

R: L'applicazione di patch a un nodo può richiedere da minuti (ad esempio, aggiornamenti delle [definizioni](https://www.microsoft.com/en-us/wdsi/definitions)di Windows Defender ) alle ore (ad esempio, [gli aggiornamenti cumulativi](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)di Windows ). Il tempo necessario per applicare la patch a un nodo dipende principalmente da: 
 - La dimensione degli aggiornamenti.
 - Il numero di aggiornamenti, che devono essere applicati in una finestra di applicazione di patch.
 - Il tempo necessario per installare gli aggiornamenti, riavviare il nodo (se necessario) e completare la procedura di installazione successiva al riavvio.
 - Le prestazioni della macchina virtuale o del computer e le condizioni di rete.

**D: Quanto tempo è necessario per applicare la patch a un intero cluster?**

R: Il tempo necessario per applicare la patch a un intero cluster dipende da:

- Il tempo necessario per applicare patch a un nodo.

- I criteri del Coordinator Service. Il criterio predefinito, "NodeWise", comporta l'applicazione di patch solo un nodo alla volta, un approccio più lento rispetto all'utilizzo di "UpgradeDomainWise". 

   Ad esempio: se un nodo richiede 1 ora per essere patchato, l'applicazione di patch a un cluster di 20 nodi (stesso tipo di nodi) con 5 domini di aggiornamento, ognuno contenente 4 nodi, richiede:
    - Per "NodeWise": 20 ore.
    - Per "UpgradeDomainWise": 5 ore.

- Caricamento del cluster. Ogni operazione di applicazione di patch richiede la rilocazione del carico di lavoro del cliente in altri nodi disponibili nel cluster. Un nodo che è in fase di patch sarebbe in [ *stato di disabilitazione* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante questo periodo. Se il cluster è in esecuzione vicino al carico di picco, il processo di disabilitazione richiederebbe più tempo. Pertanto, il processo di applicazione delle patch complessivo potrebbe sembrare lento in tali condizioni di stress.

- Errori di integrità del cluster durante l'applicazione di patch. Qualsiasi [degradazione](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [nell'integrità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperebbe il processo di applicazione delle patch. Questo problema si aggiungerebbe al tempo complessivo necessario per applicare patch all'intero cluster.

**D: Perché vengono visualizzati alcuni aggiornamenti nei risultati di Windows Update ottenuti tramite l'API REST, ma non sotto la cronologia di Windows Update nel computer?**

R: Alcuni aggiornamenti dei prodotti vengono visualizzati solo nella cronologia degli aggiornamenti o delle patch. Ad esempio, gli aggiornamenti di Windows Defender potrebbero o non essere visualizzati nella cronologia di Windows Update in Windows Server 2016.For example, Windows Defender updates might or not might be displayed in Windows Update history on Windows Server 2016.

**D: POA può essere usato per applicare patch al mio cluster di sviluppo (cluster a un nodo)?**

R: No, POA non può essere utilizzato per applicare patch a un cluster a un nodo. Questa limitazione è in base alla progettazione, perché i [servizi di sistema di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) o altre app per i clienti comporterebbe tempi di inattività. Pertanto, i processi di riparazione delle patch non verrebbero mai approvati da Repair Manager.

**D: Come si applicano patch ai nodi del cluster in Linux?**

R: Per informazioni sull'orchestrazione degli aggiornamenti in Linux, vedere Aggiornamenti automatici delle immagini del sistema operativo con [scalabilità virtuale](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)di Azure.

**D: Perché il ciclo di aggiornamento richiede così tanto tempo?**

R: Query per il risultato JSON, immettere il ciclo di aggiornamento per tutti i nodi e quindi è possibile provare a scoprire il tempo impiegato dall'installazione dell'aggiornamento in ogni nodo utilizzando OperationStartTime e OperationTime (OperationCompletionTime). 

Se è presente un intervallo di tempo elevato in cui non è in corso alcun aggiornamento, il cluster potrebbe essere in uno stato di errore e, di conseguenza, Repair Manager non può approvare le attività di ripristino POA. Se l'installazione dell'aggiornamento richiede molto tempo su qualsiasi nodo, è possibile che tale nodo non sia stato aggiornato da un po' di tempo. Un sacco di aggiornamenti potrebbero essere in attesa di installazione, che può causare ritardi. 

È anche possibile che l'applicazione di patch ai nodi sia bloccata perché bloccata nello stato *Disabling.* Ciò si verifica in genere perché la disabilitazione del nodo potrebbe causare situazioni di quorum o perdita di dati.

**D: Perché il nodo deve essere disabilitato quando POA ne applica l'applicazione di patch?**

R: POA disabilita il nodo con uno scopo *di riavvio,* che arresta o rialloca tutti i servizi di Service Fabric in esecuzione sul nodo. POA fa questo per garantire che le applicazioni non finiscano per utilizzare una combinazione di DLL nuove e vecchie, quindi si consiglia di non applicare patch a un nodo senza disabilitarlo.

**D: Qual è il numero massimo di nodi che possono essere aggiornati utilizzando POA?**

R: POA utilizza Service Fabric Repair Manager per creare attività di riparazione per i nodi per gli aggiornamenti. Tuttavia, non possono essere presenti più di 250 attività di riparazione contemporaneamente. Attualmente, POA crea attività di riparazione per ogni nodo contemporaneamente, pertanto POA può aggiornare non più di 250 nodi in un cluster. 

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- POA accetta il Contratto di Licenza con l'utente finale per Windows Update per conto dell'utente. Facoltativamente, l'impostazione può essere disattivata nella configurazione dell'applicazione.

- POA raccoglie dati di telemetria per tenere traccia dell'utilizzo e delle prestazioni. I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric (attiva per impostazione predefinita).

## <a name="troubleshooting"></a>Risoluzione dei problemi

In questa sezione vengono fornite le possibili soluzioni per la risoluzione dei problemi relativi ai nodi di applicazione delle patch.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nodo non torna allo stato attivo

* Il nodo potrebbe essere bloccato nello stato Disabling perché:The node might be stuck in *Disabling* state because:

  - Un controllo di sicurezza è in sospeso. Per risolvere questo problema, assicurarsi che siano disponibili sufficienti nodi in uno stato di integrità.

* Il nodo potrebbe essere bloccato nello stato Disabilitato perché:The node might be stuck in *Disabled* state because:

  - È stato disabilitato manualmente.
  - È stato disabilitato a causa di un processo di infrastruttura di Azure in corso.
  - È stato disabilitato temporaneamente da POA per applicare patch al nodo.

* Il nodo potrebbe essere bloccato in uno stato inattivo perché:

  - È stato messo in uno stato di down manualmente.
  - È in fase di riavvio (che potrebbe essere attivato da POA).
  - Ha una macchina virtuale o una macchina difettosa o ha problemi di connettività di rete.

### <a name="updates-were-skipped-on-some-nodes"></a>Gli aggiornamenti sono stati ignorati in alcuni nodi

POA tenta di installare un aggiornamento di Windows in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, viene generato un report sull'integrità dei livelli di avviso per Node Agent Service. Il risultato di Windows Update contiene anche il possibile motivo dell'errore.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>L'integrità del cluster diventa un errore durante l'installazione dell'aggiornamento

Un aggiornamento di Windows difettoso può ridurre l'integrità di un'applicazione o di un cluster in un determinato nodo o dominio di aggiornamento. POA interrompe qualsiasi operazione successiva di Windows Update fino a quando il cluster è di nuovo integro.

Un amministratore deve intervenire e determinare il motivo per cui l'applicazione o il cluster è diventato non integro a causa di Windows Update.

## <a name="poa-release-notes"></a>Note sulla versione di POA

>[!NOTE]
> Per le versioni POA 1.4.0 e successive, è possibile trovare note sulla versione e rilasci nella [pagina rilascio dell'applicazione Patch Orchestration](https://github.com/microsoft/Service-Fabric-POA/releases/) in GitHub.For POA versions 1.4.0 and later, you can find release notes and releases on the Patch Orchestration Application release page on GitHub.

### <a name="version-110"></a>Versione 1.1.0
- Versione pubblica

### <a name="version-111"></a>Versione 1.1.1
- Correzione di un bug in SetupEntryPoint di NodeAgentService che ha impedito l'installazione di NodeAgentNTService.

### <a name="version-120"></a>Versione 1.2.0

- Correzioni di bug nel flusso di lavoro di riavvio del sistema.
- Correzione di bug nella creazione di attività RM a causa delle quali il controllo dell'integrità durante la preparazione delle attività di ripristino non ha avuto luogo come previsto.
- Modificata la modalità di avvio per il servizio Windows POANodeSvc da auto a delayed-auto.

### <a name="version-121"></a>Versione 1.2.1

- Correzione di bug nel flusso di lavoro di riduzione delle prestazioni del cluster. È stata introdotta la logica di Garbage Collection per le attività di riparazione di Patch Orchestration Application relative a nodi inesistenti.

### <a name="version-122"></a>Versione 1.2.2

- Varie correzioni di bug.
- I file binari sono ora firmati.
- Aggiunta del collegamento a sfpkg per l'applicazione.

### <a name="version-130"></a>Versione 1.3.0

- L'impostazione di InstallWindowsOSOnlyUpdates su false consente di installare tutti gli aggiornamenti disponibili.
- Modifica della logica per disabilitare gli aggiornamenti automatici. Questa correzione risolve un bug che impediva la disabilitazione degli aggiornamenti automatici nei sistemi Server 2016 e versioni successive.
- Vincolo di posizionamento con parametri per entrambi i microservizi di POA per i casi d'uso avanzati.

### <a name="version-131"></a>Versione 1.3.1
- Correzione della regressione in cui POA 1.3.0 non funziona in Windows Server 2012 R2 o versioni precedenti a causa di un errore nella disabilitazione degli aggiornamenti automatici. 
- Correzione di un bug per cui la configurazione di InstallWindowsOSOnlyUpdates risulta sempre true.
- Modifica del valore predefinito di InstallWindowsOSOnlyUpdates su False.

### <a name="version-132"></a>Versione 1.3.2
- Correzione di un problema che interessa il ciclo di vita dell'applicazione di patch in un nodo, se sono presenti nodi con un nome che è un sottoinsieme del nome del nodo corrente. Per tali nodi, è possibile che l'applicazione di patch è stata persa o un riavvio è in sospeso.
