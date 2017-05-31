---
title: Patch Orchestration Application di Azure Service Fabric | Microsoft Docs
description: Applicazione per automatizzare l&quot;applicazione di patch ai sistemi operativi in un cluster di Service Fabric.
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Applicazione per applicare patch al sistema operativo Windows nel cluster di Service Fabric

Patch Orchestration Application è un'applicazione Service Fabric che consente di automatizzare l'applicazione di patch ai sistemi operativi in un cluster di Service Fabric in Azure o in locale, senza tempi di inattività.

Patch Orchestration Application include le seguenti funzionalità principali:

1. **Installazione dell'aggiornamento automatico del sistema operativo**: Patch Orchestration Application garantisce che gli aggiornamenti vengano scaricati e installati automaticamente e che il nodo venga riavviato ove applicabile.
    Questa operazione viene eseguita in tutti i nodi del cluster senza tempi di inattività.

1. **Integrazione dell'integrità e applicazione di patch compatibili con il cluster**: in fase di applicazione degli aggiornamenti Patch Orchestration Application monitora lo stato del cluster mentre procede un nodo alla volta o un dominio di aggiornamento alla volta. 
    Se in un qualsiasi momento rileva la violazione dell'integrità del cluster a causa dell'applicazione di patch, arresta il processo per evitare di aggravare il problema.

1. **Supporto per tutti i cluster di Service Fabric**: la genericità dell'applicazione consente il funzionamento sia nei cluster di Azure Service Fabric sia nei cluster autonomi.
    > [!NOTE]
    > Il supporto per il cluster autonomo sarà presto disponibile.

## <a name="link-to-download-the-application-package"></a>Collegamento per scaricare il pacchetto dell'applicazione

Scaricare l'applicazione dal [collegamento di download](https://go.microsoft.com/fwlink/P/?linkid=849590)

## <a name="internal-details-of-the-application"></a>Dettagli interni dell'applicazione

Patch Orchestration Application comprende i seguenti sottocomponenti:

- **Coordinator Service**: è un servizio con stato. Il servizio è responsabile per:
    - Il coordinamento del processo di Windows Update nell'intero cluster.
    - L'archiviazione del risultato delle operazioni completate di Windows Update.
- **Node Agent Service**: è un servizio senza stato che viene eseguito in tutti i nodi di cluster di Service Fabric. Il servizio è responsabile per:
    - Il bootstrap di Node Agent NTService
    - Il monitoraggio di Node Agent NTService
- **Node Agent NTService**: è un servizio di Windows NT. Node Agent NTService viene eseguito con privilegi più elevati (SISTEMA). Node Agent Service e Coordinator Service vengono invece eseguiti con un privilegio di livello inferiore (SERVIZIO DI RETE). Il servizio è responsabile dell'esecuzione dei seguenti processi di Windows Update in tutti i nodi del cluster.
    - Disabilitazione della connessione automatica a Windows Update nel nodo.
    - Download, installazione di Windows Update in base ai criteri forniti dall'utente.
    - Riavvio della macchina dopo l'installazione di Windows Update.
    - Upload del risultato di Windows Update in Coordinator Service.
    - Generazione di report sull'integrità in caso di operazione non riuscita dopo l'esaurimento di tutti i tentativi.

> [!NOTE]
> Patch Orchestration Application usa il servizio di sistema di Service Fabric, **Repair Manager**, per disabilitare/abilitare il nodo ed eseguire i controlli di integrità. L'attività di ripristino creato da Patch Orchestration Application tiene traccia dell'avanzamento di Windows Update per ogni nodo.

## <a name="prerequisites-for-using-the-application"></a>Prerequisiti per l'uso dell'applicazione

### <a name="ensure-service-fabric-version-is-55-or-above"></a>Verificare che la versione di Service Fabric sia 5.5 o successiva

Patch Orchestration Application può essere eseguita nei cluster in cui è installato il runtime di Service Fabric versione 5.5 o successive.

### <a name="enable-repair-manager-service-if-not-running-already"></a>Abilitare il servizio Repair Manager, se non già in esecuzione

Patch Orchestration Application richiede che questo servizio di sistema sia abilitato nel cluster.

#### <a name="service-fabric-clusters-on-azure"></a>Cluster di Service Fabric in Azure

Nei cluster di Azure al livello di durabilità Silver, per impostazione predefinita Repair Manager è abilitato. Nei cluster di Azure al livello di durabilità Gold è possibile che Repair Manager non sia abilitato, a seconda del tempo trascorso dalla creazione dei cluster. Nel cluster di Azure al livello di durabilità Bronze, per impostazione predefinita il servizio Repair Manager non è abilitato. 

È possibile usare il [modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) per abilitare il servizio Repair Manager nei cluster di Service Fabric nuovi o esistenti, nel caso in cui non risulti già esecuzione nella sezione relativa ai servizi di sistema in Service Fabric Explorer.

Ottenere prima il modello per il cluster da distribuire. È possibile usare i modelli di esempio o creare un modello di Resource Manager. È quindi possibile abilitare Repair Manager seguendo questa procedura:

1. Verificare prima che il `apiversion` sia impostato su `2017-07-01-preview` per la risorsa `Microsoft.ServiceFabric/clusters` come illustrato nel frammento riportato di seguito. Se il valore è diverso, è necessario aggiornare `apiVersion` a `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. A questo punto abilitare il servizio Repair Manager aggiungendo la sezione seguente `addonFeaturres` dopo la sezione `fabricSettings`, come illustrato di seguito

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. Dopo avere aggiornato il modello di cluster con queste modifiche, applicarle e consentire il completamento dell'aggiornamento. Al completamento il servizio di sistema Repair Manager risulta in esecuzione nel cluster, `fabric:/System/RepairManagerService`, nella sezione relativa ai servizi di sistema in Service Fabric Explorer. 

#### <a name="standalone-on-premise-clusters"></a>Cluster locali autonomi

> [!NOTE]
> Il supporto per il cluster autonomo sarà presto disponibile

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Disabilitare la connessione automatica a Windows Update su tutti i nodi.

L'abilitazione della connessione automatica a Windows Update sul cluster di Service Fabric può ridurre la disponibilità poiché è possibile che più nodi vengano riavviati contemporaneamente per completare l'aggiornamento.

Per impostazione predefinita Patch Orchestration Application tenta di disabilitare la connessione automatica a Windows Update su ogni nodo del cluster.

Tuttavia, nel caso in cui le impostazioni vengano gestite dall'amministratore o dai criteri di gruppo, è consigliabile impostare i criteri di Windows Update sulla notifica prima del download in modo esplicito.


### <a name="optional-enable-windows-azure-diagnostics"></a>Facoltativo: abilitare Diagnostica di Windows Azure

Nei giorni successivi i log per Patch Orchestration Application vengono raccolti come parte dei log di Service Fabric. Fino ad allora, i log vengono tuttavia raccolti in locale su ciascuno dei nodi del cluster. È consigliabile configurare Diagnostica di Microsoft Azure per eseguire l'upload dei log da tutti i nodi in una posizione centrale.

I passaggi per l'abilitare Diagnostica di Microsoft Azure sono descritti in dettaglio [qui](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)

I log per Patch Orchestration Application verranno generati negli ID dei seguenti provider stabiliti.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Nella sezione "WadCfg" del modello di Azure Resource Manager aggiungere la sezione seguente: 

```json
"PatchOrchestrationApplication": \[
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
  },
\]
```

> [!NOTE]
> Nel caso in cui il cluster di Service Fabric sia costituito da più tipi di nodo, è necessario aggiungere la sezione precedente in tutte le sezioni "WadCfg".

## <a name="configuring-the-application"></a>Configurazione dell'applicazione

Di seguito sono riportate le configurazioni che possono essere impostate dall'utente per modificare il comportamento di Patch Orchestration Application in base alle proprie esigenze.

È possibile eseguire l'override di valori predefiniti passando il parametro dell'applicazione durante la creazione/l'aggiornamento dell'applicazione. È possibile specificare i parametri dell'applicazione specificando `ApplicationParameter` al cmdlet `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`

|**Parametro**        |**Tipo**                          | **Dettagli**|
|:-|-|-|
|MaxResultsToCache    |long                              | Numero massimo di cronologie dei risultati di Windows Update memorizzabili nella cache. <br>Il valore predefinito è 3000 presumendo <br> - Un numero di nodi pari a 20 <br> - Un numero di aggiornamenti eseguiti su un nodo per ogni mese pari a 5 <br> - Un numero di risultati per ogni operazione pari a 10 <br> - L'archiviazione dei risultati relativi agli ultimi 3 mesi |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy indica i criteri che devono essere usati da CoordinatorService per installare gli aggiornamenti di Windows Update nei nodi del cluster di Service Fabric<br>                         I valori consentiti sono i seguenti: <br><br>                                                           <b>NodeWise</b>: Windows Update viene installato un nodo alla volta <br>                                                           <b>UpgradeDomainWise</b>: Windows Update viene installato un dominio di aggiornamento alla volta; al massimo l'aggiornamento può essere eseguito per tutti i nodi appartenenti a un dominio di aggiornamento.
|LogsDiskQuotaInMB   |long  <br> Predefinito: 1024               |La dimensione massima in MB dei log di Patch Orchestration Application che è possibile salvare in modo permanente in locale sul nodo
| WUQuery               | string<br>Impostazione predefinita: "IsInstalled = 0"                | Eseguire query per ottenere gli aggiornamenti Windows. Per altre informazioni, vedere [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Booleano <br> Predefinito: True                 | Questo flag consente gli aggiornamenti dei sistemi operativi Windows da installare.            |
| WUOperationTimeOutInMinutes | int <br>Predefinito: 90                   | Specifica il timeout per qualsiasi operazione di Windows Update (ricerca/download/installazione). L'operazione viene interrotta, se non viene completata entro il timeout specificato.       |
| WURescheduleCount     | int <br> Predefinito: 5                  | Questa configurazione determina il numero massimo di volte in cui il servizio ripianificherà Windows Update quando un'operazione continua a non riuscire          |
| WURescheduleTimeInMinutes | int <br>Predefinito: 30 | Questa configurazione determina l'intervallo con cui il servizio ripianificherà Windows Update se il problema persiste |
| WUFrequency           | Stringa separata da virgole Predefinito: "Weekly, Wednesday, 7:00:00"     | La frequenza di installazione di Windows Update. Il formato e i valori possibili sono indicati di seguito <br>- Monthly,DD,HH:MM:SS Esempio: Monthly,5,12:22:32 <br> - Weekly,DAY,HH:MM:SS Esempio: Weekly, Tuesday, 12:22:32  <br> - Daily, HH:MM:SS Esempio: Daily, 12:22:32  <br> - None: indica che non deve essere eseguito Windows Update  <br><br> NOTA: tutti gli orari sono in formato UTC|
| AcceptWindowsUpdateEula | Booleano <br>Predefinito: True | Impostando questo flag, l'applicazione accetta il contratto di licenza per Windows Update per conto del proprietario del computer.              |


## <a name="steps-to-deploy-the-application"></a>Passaggi per distribuire l'applicazione

1. Completare tutti i passaggi necessari per preparare il cluster.
1. Distribuire l'applicazione: questa operazione può essere applicata da PowerShell usando i passaggi illustrati [qui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)
1. Per la configurazione dell'applicazione in fase di distribuzione usare `ApplicationParamater` nel cmdlet `New-ServiceFabricApplication`

    Per facilitare l'utente è stato fornito lo script Deploy.ps1 con l'applicazione. Per usare lo script:

    - Connettersi a un cluster di Service Fabric usando `Connect-ServiceFabricCluster`
    - Eseguire lo script Deploy.ps1 di PowerShell con il valore `ApplicationParameter` appropriato

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="steps-to-upgrade-the-application"></a>Passaggi per aggiornare l'applicazione

Per l'aggiornamento di un'istanza esistente di Patch Orchestration Application con PowerShell, vedere i passaggi indicati [qui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)

Per modificare solo i parametri di applicazione dell'applicazione, specificare `ApplicationParamater` al cmdlet `Start-ServiceFabricApplicationUpgrade` con la versione dell'applicazione esistente.

## <a name="steps-to-remove-the-application"></a>Passaggi per rimuovere l'applicazione

Per rimuovere l'applicazione, vedere i passaggi indicati [qui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)

Per facilitare l'utente è stato fornito lo script Undeploy.ps1 con l'applicazione. Per usare lo script:
    - Connettersi a un cluster di Service Fabric usando ```Connect-ServiceFabricCluster```
    - Eseguire lo script Undeploy.ps1 di PowerShell

> [!NOTE]
> Mantenere lo script e la cartella dell'applicazione PatchOrchestrationApplication nella stessa directory.

## <a name="viewing-the-windows-update-results"></a>Visualizzazione dei risultati di Windows Update

Patch Orchestration Application espone l'API REST per visualizzare i risultati cronologici all'utente.

È possibile eseguire query sui risultati di Windows Update eseguendo la registrazione al cluster, individuando l'indirizzo di replica per il sito primario di CoordinatorService e raggiungendo l'URL dal browser.
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults: l'endpoint REST per CoordinatorService dispone di porta dinamica per verificare l'URL preciso a Service Fabric Explorer.
Nell'esempio seguente i risultati saranno disponibili in `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` 
 ![immagine dell'endpoint REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


L'utente può accedere all'URL anche dall'esterno del cluster, nel caso in cui il proxy inverso sia abilitato nel cluster.
Endpoint che è necessario raggiungere: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults Il proxy inverso può essere abilitato nel cluster seguendo i passaggi [qui.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 

> 
> [!WARNING]
> Quando si configura il proxy inverso, tutti i microservizi nel cluster che espongono un endpoint HTTP sono indirizzabili dall'esterno del cluster.

## <a name="diagnostics--health-events"></a>Diagnostica/eventi di integrità

### <a name="collecting-patch-orchestration-application-logs"></a>Raccolta dei log di Patch Orchestration Application

Nei giorni successivi i log per Patch Orchestration Application vengono raccolti come parte dei log di Service Fabric.
Fino a quel momento i log possono essere raccolti usando uno dei modi seguenti

#### <a name="locally-on-each-node"></a>In locale in ogni nodo

I log vengono raccolti in locale in ogni nodo del cluster di Service Fabric. Il percorso per accedere ai log è \[Service Fabric\_Installation\_unità\]:\\PatchOrchestrationApplication\\log

Ad esempio, se Service Fabric è stato installato nell'unità "D", il percorso sarà D:\\PatchOrchestrationApplication\\log

#### <a name="central-location"></a>Posizione centrale

Se Diagnostica di Microsoft Azure viene configurata nell'ambito dei passaggi preliminari, i log per Patch Orchestration Application saranno disponibili in Archiviazione di Azure.

### <a name="health-reports"></a>Report sull'integrità

Patch Orchestration Application pubblica anche i report sull'integrità per CoordinatorService o NodeAgentService nei casi seguenti

#### <a name="windows-update-operation-failed"></a>Operazione di Windows Update non riuscita

Se l'operazione di Windows Update non riesce in un nodo, verrà generato il report sull'integrità per NodeAgentService.
I dettagli del report sull'integrità conterranno il nome del nodo problematico.

Il report verrà cancellato automaticamente dopo che viene eseguita correttamente l'applicazione nel nodo problematico.

#### <a name="node-agent-ntservice-is-down"></a>Node Agent NTService è inattivo

Se NodeAgentNTService è inattivo in un nodo, viene generato il report sull'integrità sui livelli di avviso per NodeAgentService

#### <a name="repair-manager-is-not-enabled"></a>Repair Manager non è abilitato

Se il servizio Repair Manager non è stato individuato nel cluster, viene generato il report sull'integrità dei livelli di avviso per CoordinatorService.

## <a name="frequently-asked-questions"></a>Domande frequenti:

D: **Il cluster è in uno stato di errore quando Patch Orchestration Application è in esecuzione**.

R. Patch Orchestration Application disabilita e/o riavvia i nodi durante il processo di installazione e ciò può comportare temporaneamente la violazione dell'integrità del cluster.

In base a criteri impostati per l'applicazione, durante un'operazione di applicazione di patch è possibile che diventi non disponibile un singolo nodo OPPURE un intero dominio di aggiornamento simultaneamente.

Si noti che al termine dell'installazione di Windows Update, i nodi verranno abilitati di nuovo dopo il riavvio.

Ad esempio, nel caso seguente si è verificato un errore del cluster temporaneamente a causa di 2 nodi non disponibili per violazione del criterio MaxPercentageUnhealthNodes. È un errore temporaneo fino a quando l'operazione di aggiornamento è in corso.

![Immagine del cluster non integro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Se il problema persiste, fare riferimento alla sezione relativa alla risoluzione dei problemi

D: **È possibile usare Patch Orchestration Application per i cluster autonomi?**

R. Non ancora. Il supporto per i cluster autonomi sarà presto disponibile.

D: **Patch Orchestration Application è in stato di avviso**

R. Verificare se un report sull'integrità inviato per l'applicazione è la causa principale. L'avviso in genere contiene i dettagli del problema.
È previsto il ripristino automatico dell'applicazione da questo stato se il problema è temporaneo.

D: **Il cluster non è integro. Tuttavia è necessario eseguire urgentemente l'aggiornamento del sistema operativo**

R. Patch Orchestration Application non installa gli aggiornamenti mentre il cluster non è in uno stato di integrità.
Provare a portare il cluster allo stato di integrità per sbloccare il flusso di lavoro di Patch Orchestration Application.

D: **Perché l'applicazione di patch nel cluster richiede molto tempo?**

R. Il tempo impiegato da Patch Orchestration Application dipende principalmente dai seguenti fattori:

- Criteri di CoordinatorService: il criterio predefinito `NodeWise` comporta l'applicazione di patch un solo nodo alla volta; in particolare nel caso di cluster più grandi è consigliabile usare il criterio `UpgradeDomainWise` per velocizzare il processo nel cluster.
- Numero di aggiornamenti disponibili per il download e l'installazione: tempo medio per scaricare e installare un aggiornamento non deve superare alcune ore.
- Prestazioni della VM e della larghezza di banda di rete.

## <a name="disclaimers"></a>Dichiarazioni di non responsabilità

- Patch Orchestration Application accetta contratto di licenza di Windows Update per conto dell'utente. L'impostazione può facoltativamente essere disattivata nella configurazione dell'applicazione.

- Patch Orchestration Application raccoglie i dati di telemetria per tenere traccia dell'uso e delle prestazioni.
    I dati di telemetria dell'applicazione seguono l'impostazione dei dati di telemetria del runtime di Service Fabric, che è attivata per impostazione predefinita.

## <a name="troubleshooting-help"></a>Guida alla risoluzione dei problemi

### <a name="node-not-coming-back-to-up-state"></a>Il nodo non torna allo stato attivo

**Il nodo potrebbe essere bloccato nello stato disabilitato** Questa situazione può verificarsi quando non è possibile disabilitare un nodo pianificato per l'operazione a causa di un controllo di sicurezza in sospeso. Per risolvere questo problema, assicurarsi che siano disponibili sufficienti nodi in stato di integrità.

**Il nodo potrebbe essere bloccato nello stato disabilitato per la causa seguente**

- Il nodo è stato disabilitato manualmente.
- Il nodo è stato disabilitato a causa di un processo dell'infrastruttura di Azure in corso.
- Il nodo è stato disabilitato temporaneamente da Patch Orchestration Application per applicare patch al nodo.

**Il nodo potrebbe essere bloccato nello stato inattivo per il motivo seguente**

- Il nodo è stato inserito nello stato inattivo manualmente.
- Il nodo è in fase di riavvio; può essere stato attivato da Patch Orchestration Application.
- Il nodo è inattivo a causa di problemi di connettività della rete di VM/computer con errore.

### <a name="updates-were-skipped-on-some-nodes"></a>Gli aggiornamenti sono stati ignorati in alcuni nodi

Patch Orchestration Application tenta di installare Windows Update in base ai criteri di ripianificazione. Il servizio tenta di recuperare il nodo e di ignorare l'aggiornamento in base ai criteri dell'applicazione.

In questo caso, verrà generato un report sull'integrità dei livelli di avviso per Node Agent Service.
Il risultato di Windows Update conterrà anche la possibile causa dell'errore.

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>Lo stato del cluster passa nello stato di errore durante l'installazione dell'aggiornamento

Nel caso in cui un aggiornamento di Windows non valido interrompa l'integrità dell'applicazione/cluster in un nodo o un dominio di aggiornamento specifico, Patch Orchestration Application non procederà con le successive operazioni di Windows Update finché il cluster non diventa nuovamente integro.

L'amministratore deve intervenire e comprendere perché un aggiornamento di Windows Update sta causando la violazione dell'integrità dell'applicazione/cluster.

