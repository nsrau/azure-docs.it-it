---
title: Aggiornamento di Azure Stack 1710 (Build 20171020.1) | Microsoft Docs
description: Scopri quali sono le novità dell'aggiornamento 1710 per Azure Stack integrati sistemi, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377191"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Aggiornamento di Azure Stack 1710 (Build 20171020.1)

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo vengono descritti i miglioramenti e correzioni in questo pacchetto di aggiornamento, problemi noti per questa versione e dove scaricare l'aggiornamento. Noti problemi sono divisi in problemi noti relativi al processo di aggiornamento direttamente e problemi noti con la compilazione (post-installazione).

> [!IMPORTANT]
> Questo pacchetto di aggiornamento è solo per i sistemi integrati di Azure Stack. Questo pacchetto di aggiornamento non sono applicabili per Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Miglioramenti e correzioni

Questo aggiornamento include i seguenti miglioramenti alla qualità e le correzioni per Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Correzioni e miglioramenti di Windows Server 2016

- Gli aggiornamenti per Windows Server 2016: dal 10 ottobre 2017, ovvero KB4041691 (Build del sistema operativo 14393.1770. Visualizzare [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) per altre informazioni.

### <a name="additional-quality-improvements-and-fixes"></a>Correzioni e miglioramenti alla qualità aggiuntive

- Aggiunti i cmdlet di PowerShell con privilegi di endpoint per la risoluzione dei problemi e aggiornare il server di protocollo NTP (Network Time).
- Aggiunta del supporto per l'aggiornamento dei moduli di endpoint di endpoint con privilegi Just Enough Administration (JEA) e cmdlet elenco elementi consentiti. 
- Correzione di errori di lingua locale nell'endpoint con privilegi.
- Aggiunta la possibilità di ruotare le credenziali di gateway.
- Rimuovere l'account di amministratore locale CBLocalAdmin. 
- Risolto il contenuto del modello di avviso di heartbeat per rendere heartbeat che gli avvisi lavoro correttamente dopo un aggiornamento.
- Risolto il provider di risorse dell'infrastruttura di affrontare i timeout durante le operazioni di FRU. 
- È stata aggiunta la possibilità per gli sviluppatori cloud per usare i profili di API di Azure Resource Manager in Azure Stack.
- Disabilitato il servizio Windows Update nella macchina virtuale distribuzione (DVM). 
- Rimuovere il nodo accensione e spegnimento azioni dall'interfaccia utente.
- Varie altre correzioni delle prestazioni e stabilità. 
 
## <a name="known-issues-with-the-update-process"></a>Problemi noti con il processo di aggiornamento

In questa sezione contiene i problemi noti che possono verificarsi durante l'installazione dell'aggiornamento 1710.

> [!IMPORTANT]
> Se l'aggiornamento non riesce, in un secondo momento provando a riprendere l'aggiornamento è necessario usare il `Resume-AzureStackUpdate` cmdlet dall'endpoint con privilegi. L'aggiornamento non vengono riattivate usando il portale dell'amministratore. (Si tratta di un problema noto in questa versione). Per altre informazioni, vedere [monitorare gli aggiornamenti in Azure Stack tramite l'endpoint con privilegi](azure-stack-monitor-update.md).

| Sintomo  | Causa  | Risoluzione |
|---------|---------|---------|
|Quando si esegue un aggiornamento, un errore simile al seguente<br>possono verificarsi durante il passaggio "Archiviazione host riavvia nodo di archiviazione"<br> del piano di azione di aggiornamento.<br><br>**{"name": "Riavviare archiviazione host", "description": "riavviare<br> host di archiviazione. errorMessage","": "digitare"Restart"del ruolo<br> 'BareMetal' ha generato un'eccezione: \n\n computer<br> HostName-05 viene ignorata. Non è possibile recuperare il valore di LastBootUpTime<br> tramite il servizio WMI con il messaggio di errore seguente:<br> server RPC non disponibile.<br> (Eccezione da HRESULT: 0x800706BA). riavviare \nat-Host** | Questo problema è causato da un driver difettoso potenziali presente in alcune configurazioni. | 1. Accedere all'interfaccia web di baseboard management controller (BMC) e riavviare l'host viene identificato nel messaggio di errore.<br><br>2. Riprendere l'aggiornamento usando l'endpoint con privilegi. |
| Quando si esegue un aggiornamento, il processo di aggiornamento viene visualizzato al posto<br> e non fare progressi dopo il passaggio "passaggio: esecuzione del passaggio 2.4 - installazione<br> aggiornare"del piano di azione di aggiornamento.<br><br>Questa operazione è quindi seguita da una serie di processi di copia del pacchetto. nupkg<br> file alle condivisioni di file di infrastruttura interna. Ad esempio: <br><br>**Copia dei file 1 da content\PerfCollector\VirtualMachines al <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>In alternativa, viene visualizzato il messaggio:<br><br>**WarningMessage:Task: Chiamata dell'interfaccia 'LiveUpdate'<br> del ruolo 'Cloud\Fabric\VirtualMachines' non è riuscita:<br> digitare 'LiveUpdate' del ruolo 'VirtualMachines' ha generato un<br> eccezione: non c'è spazio sufficiente sul disco .**  | Il problema è causato da file di log riempiono i dischi in una macchina virtuale di infrastruttura e un problema in Windows Server Scale-Out File Server di scalabilità orizzontale (SOFS) che verrà distribuito in un prossimo aggiornamento. | Per assistenza, contattare il servizio clienti e supporto (CSS). | 
| Quando si esegue un aggiornamento, un errore simile al seguente<br> possono verificarsi durante il passaggio "passaggio: esecuzione del passaggio 2.13.2 - aggiornamento<br> *VM_Name*"del piano di azione di aggiornamento. (La macchina virtuale<br> nome può variare.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: chiamata dell'interfaccia 'LiveUpdate' di<br> ruolo 'Cloud\Fabric\WAS' non è riuscita: digitare 'LiveUpdate' del ruolo<br> 'WAS' ha generato un'eccezione: errore durante archiviazione<br> inizializzazione: si è verificato un errore durante il tentativo di stabilire un'API<br> chiamate al servizio Microsoft Storage: {"Message": "un timeout<br> si è verificato durante la comunicazione con Service Fabric.<br> Tipo di eccezione: eccezione TimeoutException.<br> Messaggio eccezione: timeout dell'operazione. "}**  | Il problema è causato da un timeout dei / o in Windows Server che verrà risolto in un aggiornamento successivo. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, un errore simile al seguente<br> possono verificarsi durante il passaggio "passaggio 21 riavviare SQL server VMs".<br><br>**Digitare 'LiveUpdateRestart' del ruolo 'VirtualMachines' ha generato un<br> eccezione: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> esegue una query per MachineName VM-Sql01. - 10 o 13/2017 5: 11:50 VerboseMessage: [VirtualMachines: LiveUpdateRestart]<br> macchina virtuale è contrassegnata come HighlyAvailable. - 10 o 13/2017 5: 11:50<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] in<br>MS. Internal.ServerClusters.ExceptionHelp.Build in<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(forza booleano) a failoverclusters.<br> In StopClusterResourceCommand.BeginTimedOperation() <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() in failoverclusters.<br> FCCmdlet.ProcessRecord() - 10 o 13/2017 5: 11:50 avviso<br>messaggi: attività: chiamata dell'interfaccia 'LiveUpdateRestart' di<br> ruolo 'Cloud\Fabric\VirtualMachines' non è riuscita:** | Questo problema può verificarsi se non è riuscita a riavviare la macchina virtuale. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, potrebbe verificarsi un errore simile al seguente:<br><br>**2017-10-tipo 'Shutdown' del ruolo 'SQL' 22T01:37:37.5369944Z<br> ha generato un'eccezione: nodo sospensione si è verificato un errore<br> .at 's45r1004-Sql01' Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications \ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 alla<br> Arresto del sistema, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>\EnterpriseCloudEngineApplicationType applicazioni&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: riga 50 < ScriptBlock&#62;,<br> <No file>: riga 18 in < ScriptBlock&#62;, < nessun file&#62;: 16 di riga** | Questo problema può verificarsi se la macchina virtuale non può essere riportata in uno stato sospeso per svuotare i ruoli. | Per assistenza, contattare Microsoft CSS.
| Quando si esegue un aggiornamento, potrebbe verificarsi uno degli errori seguenti:<br><br>**Tipo 'Validate' del ruolo 'ADFS' ha generato un'eccezione: convalida<br> per il ruolo di ad FS, grafo non è riuscita con errore: errore durante il controllo ad FS<br> probe dell'endpoint *endpoint_URI*: chiamata di eccezione<br> " Metodo GetResponse"con"0"argomenti:" il server remoto<br> ha restituito un errore: non disponibile (503) del Server. "Invoke-<br>ADFSGraphValidation**<br><br>**Tipo 'Validate' del ruolo 'ADFS' ha generato un'eccezione: convalida<br> per il ruolo di ad FS, grafo non è riuscita con errore: errore durante il recupero<br> delle proprietà di ad FS: Impossibile connettersi al <br>NET.TCP://localhost:8001/NetTcp: 1500/criteri. Il tentativo di connessione sposata<br> per un intervallo di tempo di 00:00:02.0498923. Codice di errore TCP<br> 10061: è stato possibile stabilire alcuna connessione perché la destinazione<br> rifiuto persistente del computer 127.0.0.1:1500.<br> a Invoke-ADFSGraphValidation** | Il piano di azione di aggiornamento non è possibile convalidare l'integrità di Active Directory Federation Services (ADFS). | Per assistenza, contattare Microsoft CSS.

## <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

In questa sezione contiene i problemi noti con 20171020.1 di compilazione post-installazione.

### <a name="portal"></a>Portale

- Potrebbe non essere possibile visualizzare le risorse di calcolo o archiviazione nel portale di amministrazione. Ciò indica che si è verificato un errore durante l'installazione dell'aggiornamento e che l'aggiornamento è stato segnalato in modo errato come esito positivo. Se si verifica questo problema, contattare Microsoft CSS per ottenere assistenza.
- È possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, selezionare l'icona a forma di ingranaggio in alto a destra del portale e quindi selezionare **ripristinare le impostazioni predefinite**.
- Quando si visualizzano le proprietà di un gruppo di risorse, il **spostare** pulsante è disabilitato. Questo comportamento è previsto. Lo spostamento di gruppi di risorse tra sottoscrizioni non è attualmente supportato.
- Per qualsiasi flusso di lavoro in cui si seleziona una sottoscrizione, gruppo di risorse o alla posizione in un elenco a discesa che si verifichi uno o più dei problemi seguenti:

   - È possibile visualizzare una riga vuota nella parte superiore dell'elenco. È comunque possibile selezionare un elemento come previsto.
   - Se l'elenco di elementi nell'elenco a discesa è breve, potrebbe non essere in grado di visualizzare tutti i nomi degli elementi.
   - Se si hanno più sottoscrizioni utente, l'elenco di riepilogo a discesa gruppo di risorse può essere vuoto. 

   Per risolvere i problemi di ultimi due, è possibile digitare il nome della sottoscrizione o gruppo di risorse (se lo si conosce) oppure è possibile usare invece PowerShell.
- Eliminazione utente diverso, le sottoscrizioni le risorse orfane. In alternativa, eliminare prima le risorse utente o l'intero gruppo di risorse e quindi eliminare le sottoscrizioni dell'utente.
- Non si è in grado di visualizzare le autorizzazioni per la sottoscrizione tramite il portale di Azure Stack. In alternativa, è possibile verificare le autorizzazioni usando PowerShell.
- Il **integrità dei servizi** pannello non viene caricato. Quando si apre il pannello del servizio integrità nel portale di amministratore o utente, Azure Stack viene visualizzato un errore e non carica informazioni. Si tratta di un comportamento previsto. Anche se è possibile selezionare e aprire l'integrità del servizio, questa funzionalità non è ancora disponibile, ma verrà implementata in una futura versione di Azure Stack.
 

### <a name="backup"></a>Backup

- Non abilitare il backup dell'infrastruttura nel **backup Infrastructure** pannello.

### <a name="health-and-monitoring"></a>Monitoraggio dell'integrità e

- Se si riavvia un'istanza del ruolo di infrastruttura, si potrebbe ricevere un messaggio che indica che il riavvio non è riuscita. Tuttavia, il riavvio è effettivamente riuscito.

### <a name="marketplace"></a>Marketplace
- Quando si tenta di aggiungere elementi al marketplace Azure Stack tramite il **Add da Azure** opzione, non tutti gli elementi potrebbero essere visibili per il download.
- Gli utenti possono esplorare il marketplace completo senza una sottoscrizione ed è possono visualizzare elementi amministrativi quali offerte e piani. Questi elementi sono non funzionale per gli utenti.

### <a name="compute"></a>Calcolo
- Agli utenti vengano offerta la possibilità di creare una macchina virtuale con archiviazione con ridondanza geografica. Questa configurazione, la creazione della macchina virtuale avere esito negativo.
- È possibile configurare una macchina virtuale set di disponibilità solo con un dominio di errore di uno e un dominio di aggiornamento di uno.
- Non vi è alcuna esperienza di marketplace per creare il set di scalabilità di macchine virtuali. È possibile creare un set con un modello di scalabilità.
- Impostazioni di scalabilità per il set di scalabilità di macchine virtuali non sono disponibili nel portale. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). A causa delle differenze di versione di PowerShell, è necessario usare il `-Name` parametro anziché `-VMScaleSetName`.
 
### <a name="networking"></a>Rete
- È possibile creare un servizio di bilanciamento del carico con un indirizzo IP pubblico usando il portale. In alternativa, è possibile usare PowerShell per creare il servizio di bilanciamento del carico.
- Quando si crea un servizio di bilanciamento del carico di rete, è necessario creare una regola di translation (NAT) indirizzo di rete. In caso contrario, si riceverà un errore quando si prova ad aggiungere una regola NAT, dopo aver creato il servizio di bilanciamento del carico.
- Dopo aver creata la macchina virtuale e associata a quell'indirizzo IP non è possibile annullare l'associazione un indirizzo IP pubblico da una macchina virtuale (VM). Dissociazione sembrerà funzionare correttamente, ma l'indirizzo IP pubblico assegnato in precedenza rimane associato alla VM originale. Questo comportamento si verifica anche se si riassegna una nuova macchina virtuale l'indirizzo IP (noto come un *scambio VIP*). Tutti i successivi tentativi di connettersi tramite il risultato di indirizzi IP in una connessione alla macchina virtuale di origine associata e non a quello nuovo. Attualmente, è necessario usare solo nuovi indirizzi IP pubblici per la creazione di nuove VM.
 
### <a name="sqlmysql"></a>SQL o MySQL
- Può richiedere fino a un'ora prima che i tenant possono creare i database in un nuovo SKU MySQL o SQL. 
- Creazione di elementi direttamente in SQL e server che non vengono eseguite dal provider di risorse di hosting MySQL non è supportata e può comportare uno stato non corrispondente.
 
### <a name="app-service"></a>Servizio app
- Un utente deve registrare il provider di risorse di archiviazione prima di creare la prima funzione di Azure nella sottoscrizione.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procedure (FRU) di unità sostituibile sul campo

- Durante l'operazione di aggiornamento, le immagini non in linea non sono corretti. Se è necessario sostituire un nodo di unità di scala, rivolgersi al fornitore dell'hardware OEM per assicurarsi che il nodo sostituito con il livello di patch più recente.

## <a name="download-the-update"></a>Scaricare l'aggiornamento

È possibile scaricare il pacchetto di aggiornamento 1710 [qui](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica della gestione degli aggiornamenti in Azure Stack, vedere [gestire gli aggiornamenti nella panoramica di Azure Stack](azure-stack-updates.md).
- Per informazioni su come applicare gli aggiornamenti, vedere [applicare gli aggiornamenti in Azure Stack](azure-stack-apply-updates.md).
