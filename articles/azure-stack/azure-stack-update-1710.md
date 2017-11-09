---
title: Aggiornamento di Azure Stack 1710 (Build 20171020.1) | Documenti Microsoft
description: "Informazioni sulle novità nell'aggiornamento 1710 per Azure Stack integrate di sistemi, i problemi noti e come scaricare l'aggiornamento."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: twooley
ms.openlocfilehash: d91a23ae4eb5aee14d3d2fef74467e7f33c458cc
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Aggiornamento di Azure Stack 1710 (Build 20171020.1)

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo vengono descritti i miglioramenti e correzioni in questo pacchetto di aggiornamento noti problemi per questa versione e come scaricare l'aggiornamento. Nota problemi sono divisi in problemi noti relativi al processo di aggiornamento direttamente e problemi noti con la compilazione (post-installazione).

> [!IMPORTANT]
> Questo pacchetto di aggiornamento è solo per i sistemi Azure Stack integrato. Questo pacchetto di aggiornamento non si applicano al Kit di sviluppo dello Stack di Azure.

## <a name="improvements-and-fixes"></a>Miglioramenti e correzioni

Questo aggiornamento include gli aggiornamenti e i miglioramenti seguenti per lo Stack di Azure.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Correzioni e miglioramenti di Windows Server 2016

- Gli aggiornamenti per Windows Server 2016: il 10 ottobre 2017: KB4041691 (Build 14393.1770 del sistema operativo. Vedere [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691) per ulteriori informazioni.

### <a name="additional-quality-improvements-and-fixes"></a>Correzioni e miglioramenti della qualità aggiuntive

- Aggiungere i cmdlet di PowerShell con privilegi di endpoint per la risoluzione dei problemi e aggiornare il server di protocollo NTP (Network Time).
- Aggiunta del supporto per l'aggiornamento i cmdlet whitelist e i moduli di endpoint di endpoint con privilegi Just Enough Administration (JEA). 
- Correzione di errori di lingua locale nell'endpoint con privilegi.
- Aggiunta la possibilità di ruotare le credenziali di gateway.
- Rimuovere l'account di amministratore locale CBLocalAdmin. 
- Fissa il contenuto del modello avviso heartbeat per rendere heartbeat che gli avvisi lavoro correttamente dopo un aggiornamento.
- Fissa il provider di risorse dell'infrastruttura per gestire i timeout durante le operazioni di FRU. 
- Aggiunta la possibilità per gli sviluppatori cloud per usare i profili di API di gestione risorse di Azure nello Stack di Azure.
- Disabilitato il servizio Windows Update nella macchina virtuale distribuzione (DVM). 
- Rimuovere il nodo accensione e spegnimento azioni dall'interfaccia utente.
- Varie altre correzioni stabilità e prestazioni. 
 
## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

In questa sezione contiene i problemi noti che possono verificarsi durante l'installazione dell'aggiornamento 1710.

> [!IMPORTANT]
> Se l'aggiornamento non riesce, quando in un secondo momento tenta di riprendere l'aggiornamento è necessario utilizzare il `Resume-AzureStackUpdate` cmdlet dall'endpoint con privilegi. Non riprendere l'aggiornamento tramite il portale dell'amministratore. (Si tratta di un problema noto in questa versione). Per ulteriori informazioni, vedere [monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi](azure-stack-monitor-update.md).

| Sintomo  | Causa  | Risoluzione |
|---------|---------|---------|
|Quando si esegue un aggiornamento, può verificarsi un errore simile al seguente errore durante il passaggio "Archiviazione riavviare archiviazione nodo host" del piano di azione di aggiornamento.<br><br>**{"name": "Riavviare archiviazione host", "descrizione": "Riavviare archiviazione host.", "errorMessage": "tipo"Restart' del ruolo 'BareMetal' generato un computer di eccezione: \n\nThe-05 di nome host viene ignorata. Impossibile recuperare il relativo LastBootUpTime tramite il servizio WMI con il seguente messaggio di errore: il server RPC non è disponibile. (Eccezione da HRESULT: 0x800706BA). riavviare \nat-Host** | Questo problema è causato da un driver difettoso potenziali presente in alcune configurazioni. | 1. Accedere all'interfaccia web baseboard management controller (BMC) e riavviare l'host viene identificato nel messaggio di errore.<br><br>2. Riprendere l'aggiornamento utilizzando l'endpoint con privilegi. |
| Quando si esegue un aggiornamento, il processo di aggiornamento viene visualizzato per molto tempo e non fare progressi dopo il passaggio "passaggio: esecuzione del passaggio 2.4 - installare l'aggiornamento" del piano di azione di aggiornamento.<br><br>Questo passaggio è quindi seguito da una serie di processi di copia dei file .nupkg alle condivisioni di file di infrastruttura interni. ad esempio:<br><br>**Copia i file di 1 da content\PerfCollector\VirtualMachines a \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\PerfCounterConfiguration**  | Il problema è causato da file di log esaurisca i dischi in una macchina virtuale di infrastruttura e un problema in Windows Server Scale-Out File Server (orizzontale) che verrà recapitato in un aggiornamento successivo. | Per assistenza, contattare il servizio clienti e supporto tecnico. | 
| Quando si esegue un aggiornamento, un errore simile al seguente può verificarsi durante il passaggio "passaggio: esecuzione del passaggio 2.13.2 - aggiornamento *VM_Name*" del piano di azione di aggiornamento. (Nome della macchina virtuale può variare).<br><br>**ActionPlanInstanceWarning ece/MachineName: WarningMessage:Task: chiamata di interfaccia 'LiveUpdate' del ruolo 'Cloud\Fabric\WAS' non è riuscita:<br>'LiveUpdate' di tipo di ruolo 'WAS' ha generato un'eccezione:<br>errore durante l'archiviazione inizializzazione: si è verificato un errore durante il tentativo di effettuare una chiamata API al servizio Microsoft Storage: {"Messaggio": "un timeout durante la comunicazione con Service Fabric. Tipo di eccezione: TimeoutException. Messaggio eccezione: timeout dell'operazione. "}**  | Il problema è causato da un timeout dei / o in Windows Server che verrà risolto in un aggiornamento successivo. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, potrebbe verificarsi un errore simile al seguente durante il passaggio "passaggio 21 macchine virtuali di riavviare SQL server".<br><br>**Tipo 'LiveUpdateRestart' del ruolo 'VirtualMachines' ha generato un'eccezione:<br>VerboseMessage: esecuzione di query [VirtualMachines:LiveUpdateRestart] per la macchina virtuale MachineName-Sql01. - 13/10/2017 5:11:50 PM VerboseMessage: [macchine virtuali: LiveUpdateRestart] macchina virtuale è contrassegnato come HighlyAvailable. -VerboseMessage 5:11:50 PM 10/13/2017: [VirtualMachines:LiveUpdateRestart] ms. Internal.ServerClusters.ExceptionHelp.Build ms. Internal.ServerClusters.ClusterResource.BeginTakeOffline (force booleano) in Microsoft.FailoverClusters.PowerShell.StopClusterResourceCommand.BeginTimedOperation() in Microsoft.FailoverClusters.PowerShell.TimedCmdlet.WrappedProcessRecord() in Microsoft.FailoverClusters.PowerShell.FCCmdlet.ProcessRecord() - 13/10/2017 5:11:50 PM WarningMessage:Task: chiamata di interfaccia 'LiveUpdateRestart' del ruolo ' Cloud\Fabric\VirtualMachines' non riuscita:** | Questo problema può verificarsi se non è possibile riavviare la macchina virtuale. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, potrebbe verificarsi un errore simile al seguente:<br><br>**2017-10-22T01:37:37.5369944Z 'Shutdown' di tipo di ruolo 'SQL' ha generato un'eccezione: errore durante la sospensione .at nodo 's45r1004 Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_ App1\EnterpriseCloudEngineServicePkg.code.1.0.597.18\CloudDeployment\Roles\SQL\SQL.psm1: riga 542at arresto C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\ EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Classes\SQL\SQL.psm1: riga 50at <ScriptBlock>, <No file>: riga 18at <ScriptBlock>, <No file>: riga 16** | Questo problema può verificarsi se la macchina virtuale non può essere inserita in uno stato sospeso per svuotare i ruoli. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, potrebbero verificarsi degli errori seguenti:<br><br>**Tipo 'Convalida' del ruolo 'ADFS' ha generato un'eccezione: convalida per il ruolo ADFS/grafico non riuscita con errore: errore durante la verifica endpoint probe ADFS *endpoint_URI*: eccezione durante la chiamata "GetResponse" con "0" argomenti: "il server remoto ha restituito un errore: non disponibile (503) del Server. "a Invoke-ADFSGraphValidation**<br><br>**Tipo 'Convalida' del ruolo 'ADFS' ha generato un'eccezione: la convalida per il ruolo ADFS/grafico non riuscita con errore: errore durante il recupero delle proprietà ADFS: Impossibile connettersi a NET.TCP: 1500/criteri. Il tentativo di connessione è durata intervallo di tempo di 00:00:02.0498923. Codice di errore TCP 10061: Impossibile stabilire la connessione perché il computer di destinazione non attivamente 127.0.0.1:1500. in ADFSGraphValidation Invoke** | Il piano di azione di aggiornamento non è possibile convalidare l'integrità di Active Directory Federation Services (ADFS). | Per assistenza, contattare Microsoft CSS.

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

In questa sezione sono presenti problemi noti per compilare 20171020.1 di post-installazione.

### <a name="portal"></a>di Microsoft Azure

- Potrebbe non essere possibile visualizzare le risorse di calcolo o di archiviazione nel portale di amministrazione. Ciò indica che si è verificato un errore durante l'installazione dell'aggiornamento e che l'aggiornamento è stato segnalato in modo non corretto come esito positivo. Se si verifica questo problema, contattare Microsoft CSS per assistenza.
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona dell'ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Gli utenti sfogliare il marketplace completo senza una sottoscrizione e di visualizzare gli elementi di amministrazione come piani e alle offerte. Questi elementi sono non funzionale agli utenti.
- Il **spostare** pulsante è disabilitato quando si visualizzano le proprietà di un gruppo di risorse. Questo comportamento è previsto. Spostare i gruppi di risorse tra le sottoscrizioni non è attualmente supportato.
- Non è in grado di visualizzare le autorizzazioni per la sottoscrizione utilizzando i portali di Stack di Azure. In alternativa, è possibile verificare le autorizzazioni tramite PowerShell.
-  Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa, si verifichi uno o più dei seguenti problemi:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, non sarà possibile visualizzare i nomi degli elementi.
   - Se si dispone di più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

   Per risolvere i problemi ultimi due, è possibile digitare il nome della sottoscrizione o del gruppo di risorse (se si conosce) oppure è possibile usare PowerShell.
  
### <a name="backup"></a>Backup

- Non abilitare il backup di infrastruttura nel **backup infrastruttura** blade.

### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e

- Se si riavvia un'istanza del ruolo di infrastruttura, si potrebbe ricevere un messaggio che indica che il riavvio non riuscito. Tuttavia, il riavvio è effettivamente riuscito.

### <a name="services"></a>Services

**Marketplace**
- Quando si tenta di aggiungere elementi a marketplace Stack Azure utilizzando il **Aggiungi da Azure** opzione, non tutti gli elementi potrebbero essere visibili per il download.
- Non sussiste alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set, utilizzando un modello di scalabilità.
- Un tenant è necessario registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.
- Se si elimina utente sottoscrizioni nelle risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente. 

**SQL o MySQL**
- È possibile richiedere un'ora prima che i tenant possono creare database in un nuovo SQL o MySQL SKU. 
- Creazione di elementi direttamente nel server che non vengono eseguite dal provider di risorse di hosting MySQL e SQL Server non è supportata e può comportare uno stato non corrispondente.

**Calcolo**
- Gli utenti assegnati l'opzione per creare una macchina virtuale con l'archiviazione con ridondanza geografica. Questa configurazione causa l'errore durante la creazione macchina virtuale.
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
 
**Rete**
- Tramite il portale, è possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico. In alternativa, è possibile utilizzare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si tenta di aggiungere una regola NAT, dopo aver creato il bilanciamento del carico.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedure (FRU) di unità sostituibile sul campo

- Durante l'esecuzione dell'aggiornamento, le immagini non in linea non sono corretti. Se è necessario sostituire un nodo di unità di scala, rivolgersi al fornitore dell'hardware OEM per assicurarsi che il nodo sostituito con il livello di patch più recente.

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento 1710 [qui](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica della gestione degli aggiornamenti nello Stack di Azure, vedere [gestire gli aggiornamenti in panoramica di Azure Stack](azure-stack-updates.md).
- Per informazioni su come applicare gli aggiornamenti, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).