---
title: Soluzione Gestione aggiornamenti in Azure | Microsoft Docs
description: Questo articolo illustra l'uso di questa soluzione per gestire gli aggiornamenti per i computer Windows e Linux.
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: gwallace
ms.openlocfilehash: bb20137cc3ac8daf82ee21300be6981e09ce3fe0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="update-management-solution-in-azure"></a>Soluzione Gestione aggiornamenti in Azure

La soluzione Gestione aggiornamenti in Automazione di Azure consente di gestire gli aggiornamenti della sicurezza del sistema operativo per i computer Windows e Linux distribuiti in Azure, in ambienti locali o in altri provider di servizi cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

È possibile abilitare Gestione aggiornamenti per le macchine virtuali direttamente dall'account [Automazione di Azure](automation-offering-get-started.md).
Per informazioni su come abilitare la Gestione aggiornamenti per le macchine virtuali dall'account Automazione, vedere [Gestire gli aggiornamenti per più macchine virtuali](manage-update-multi.md).

## <a name="solution-overview"></a>Panoramica della soluzione

I computer gestiti da Gestione aggiornamenti usano le configurazioni seguenti per le valutazioni e le distribuzioni degli aggiornamenti:

* Microsoft Monitoring Agent per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per runbook di Automazione
* Microsoft Update o Windows Server Update Services per computer Windows

Il diagramma seguente offre una visualizzazione concettuale del comportamento e del flusso dei dati e indica in che modo la soluzione valuta e applica gli aggiornamenti della sicurezza a tutti i computer Windows Server e Linux connessi in un'area di lavoro.    

![Flusso del processo di gestione dell'aggiornamento](media/automation-update-management/update-mgmt-updateworkflow.png)

Quando un computer ha completato l'analisi di conformità degli aggiornamenti, l'agente inoltra le informazioni in blocco a Log Analytics. In un computer Windows, l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita. Oltre all'analisi pianificata, l'analisi di conformità degli aggiornamenti viene avviata entro 15 minuti in caso di riavvio di Microsoft Monitoring Agent (MMA) e prima e dopo l'installazione degli aggiornamenti. Con un computer Linux, l'analisi di conformità viene eseguita ogni 3 ore per impostazione predefinita e avviata entro 15 minuti in caso di riavvio dell'agente MMA.

La soluzione genera report sullo stato di aggiornamento del computer in base all'origine configurata per la sincronizzazione. Se il computer Windows è configurato per l'invio di report a WSUS, a seconda della data/ora in cui WSUS ha eseguito l'ultima sincronizzazione con Microsoft Update i risultati possono differire da quanto visualizzato da Microsoft Update. Lo stesso vale per i computer Linux configurati per l'invio di report a un repository locale rispetto a un repository pubblico.

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. A differenza degli aggiornamenti obbligatori, gli aggiornamenti classificati come *facoltativi* non sono inclusi nell'ambito della distribuzione per i computer Windows. La distribuzione pianificata definisce quali computer di destinazione ricevono gli aggiornamenti applicabili, specificando i computer in modo esplicito oppure selezionando un [gruppo di computer](../log-analytics/log-analytics-computer-groups.md) in base alle ricerche log di un determinato set di computer. Si specifica anche una pianificazione per approvare e definire un periodo di tempo entro il quale è possibile installare gli aggiornamenti. Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Questi runbook non richiedono alcuna configurazione e non possono essere visualizzati. Quando si crea una distribuzione degli aggiornamenti, viene creata una pianificazione che avvia un runbook di aggiornamento master alla data e ora specificata per i computer inclusi. Questo runbook master avvia un runbook figlio in ogni agente che esegue l'installazione degli aggiornamenti necessari.

Alla data e ora specificate nella distribuzione degli aggiornamenti, i computer di destinazione eseguono la distribuzione in parallelo. Viene prima di tutto eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari, quindi viene eseguita l'installazione. Per i computer client WSUS, la distribuzione degli aggiornamenti ha esito negativo se gli aggiornamenti non sono approvati in WSUS.

## <a name="clients"></a>Client

### <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati: 

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2008 e versioni successive     | Supporta solo le valutazioni degli aggiornamenti         |
|Windows Server 2008 R2 SP1 e versioni successive     |Sono richiesti .NET Framework 4.5 e WMF 5.0 o versioni successive per Windows Server 2008 R2 SP1<br>Nano Server non supportato         |
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 12.04 LTS e versioni x86/x64 più recenti       |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

### <a name="unsupported-client-types"></a>Tipi di client non supportati

La tabella seguente elenca i sistemi operativi non supportati:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client (Windows 7, Windows 10 e così via) non sono supportati.        |
|Nano Server     | Nano Server fa parte di Windows 2016.        |

### <a name="client-requirements"></a>Requisiti per i client

#### <a name="windows"></a>Windows

Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update. Inoltre, l'agente Windows non può essere gestito contemporaneamente da System Center Configuration Manager. L'[agente Windows](../log-analytics/log-analytics-agent-windows.md) è obbligatorio. Questo agente viene installato automaticamente in caso di onboarding di una macchina virtuale di Azure.

#### <a name="linux"></a>Linux

Per Linux, il computer deve avere accesso a un repository degli aggiornamenti, che può essere pubblico o privato. Un agente di OMS per Linux configurato per l'invio di report a più aree di lavoro di Log Analytics non è supportato con questa soluzione.

Per altre informazioni su come installare l'agente di OMS per Linux e scaricare la versione più recente, vedere l'[agente di Operations Management Suite per Linux](https://github.com/microsoft/oms-agent-for-linux). Per informazioni su come installare l'agente OMS per Windows, vedere l'[agente Operations Management Suite per Windows](../log-analytics/log-analytics-windows-agent.md).  

## <a name="permissions"></a>Autorizzazioni
Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per altre informazioni su queste autorizzazioni, vedere [Controllo degli accessi in base al ruolo - Gestione degli aggiornamenti](automation-role-based-access-control.md#update-management). 

## <a name="solution-components"></a>Componenti della soluzione
Questa soluzione è costituita dalle risorse seguenti che vengono aggiunte all'account di Automazione e agli agenti direttamente connessi o al gruppo di gestione connesso di Operations Manager.

### <a name="hybrid-worker-groups"></a>Gruppi di ruoli di lavoro ibridi
Dopo aver abilitato questa soluzione, qualsiasi computer Windows direttamente connesso all'area di lavoro di Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione. Per ogni computer Windows gestito dalla soluzione, il ruolo di lavoro è elencato nella pagina dei gruppi di ruoli di lavoro ibridi come gruppo di ruoli di lavoro ibridi per il sistema per l'account di Automazione secondo la convenzione di denominazione *Hostname FQDN_GUID*. Non è possibile applicare runbook a questi gruppi nell'account perché hanno esito negativo. Questi gruppi servono solo per supportare la soluzione di gestione.

È tuttavia possibile aggiungere i computer Windows a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione di System Center Operations Manager è connesso all'area di lavoro di Log Analytics, in Operations Manager verranno installati i Management Pack seguenti. Questi Management Pack vengono installati anche su computer Windows direttamente connessi dopo l'aggiunta di questa soluzione. Non sono richieste attività di configurazione o gestione con questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Verificare l'onboarding di computer non di Azure

Per verificare che i computer direttamente connessi comunichino con Log Analytics, dopo alcuni minuti è possibile eseguire la ricerca log seguente:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

In un computer Windows la connettività degli agenti con Log Analytics può essere verificata nel modo seguente:

1.  Aprire Microsoft Monitoring Agent nel Pannello di controllo. Nella scheda **Analisi dei log di Azure (OMS)** l'agente visualizza il messaggio **The Microsoft Monitoring Agent has successfully connected to the Microsoft Operations Management Suite service** (Microsoft Monitoring Agent ha eseguito la connessione al servizio Microsoft Operations Management Suite).   
2.  Aprire il registro eventi di Windows, passare a **Registri applicazioni e servizi\Operations Manager** e cercare gli ID evento 3000 e 5002 del connettore del servizio di origine. Questi eventi indicano che il computer ha eseguito la registrazione all'area di lavoro di Log Analytics e sta ricevendo la configurazione.  

Se l'agente non può comunicare con Log Analytics ed è configurato per la comunicazione con Internet tramite firewall o server proxy, verificare che il firewall o il server proxy sia configurato correttamente come descritto nei paragrafi relativi alla [configurazione di rete per l'agente Windows](../log-analytics/log-analytics-agent-windows.md) o alla [configurazione di rete per l'agente Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se i sistemi Linux sono configurati per la comunicazione con un proxy o il gateway OMS e si sta eseguendo l'onboarding di questa soluzione, aggiornare le autorizzazioni *proxy.conf* per concedere al gruppo omiuser le necessarie autorizzazioni di lettura per il file eseguendo i comandi seguenti:  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Gli agenti Linux appena aggiunti visualizzeranno lo stato **Aggiornato** dopo l'esecuzione di una valutazione. Il processo può richiedere fino a 6 ore.

Per verificare che un gruppo di gestione di Operations Manager comunichi con Log Analytics, vedere [Convalidare l'integrazione di Operations Manager con OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Raccolta dei dati

### <a name="supported-agents"></a>Agenti supportati
La tabella seguente descrive le origini connesse che sono supportate da questa soluzione.

| Origine connessa | Supportato | DESCRIZIONE |
| --- | --- | --- |
| Agenti di Windows |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br>Non è necessaria una connessione diretta dall'agente Operations Manager a Log Analytics. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro di Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta
Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti, l'API Windows viene chiamata per eseguire una query per la data/ora dell'ultimo aggiornamento e determinare se lo stato è stato modificato. In caso affermativo viene avviata un'analisi di conformità. Per ogni computer Linux gestito viene eseguita un'analisi ogni 3 ore.

La visualizzazione dei dati aggiornati dei computer gestiti nel dashboard può richiedere da 30 minuti a 6 ore.   

## <a name="viewing-update-assessments"></a>Visualizzazione della valutazione degli aggiornamenti
Fare clic su **Gestione aggiornamenti** nell'account di automazione per visualizzare lo stato dei computer.

Questa visualizzazione fornisce informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate.

È possibile eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione selezionando l'elemento nell'elenco. Verrà visualizzata la pagina **Ricerca log** con una query per l'elemento selezionato.

## <a name="installing-updates"></a>Installazione degli aggiornamenti

Dopo aver valutato gli aggiornamenti per tutti i computer Linux e Windows nell'area di lavoro, è possibile installare gli aggiornamenti necessari creando una *distribuzione degli aggiornamenti*. Una distribuzione degli aggiornamenti è un'installazione pianificata di aggiornamenti necessari per uno o più computer. Specificare la data e l'ora della distribuzione, oltre a un computer o gruppo di computer da includere nell'ambito della distribuzione. Per altre informazioni sui gruppi di computer, vedere [Gruppi di computer in Log Analytics](../log-analytics/log-analytics-computer-groups.md). Quando si includono gruppi di computer nella distribuzione degli aggiornamenti, l'appartenenza ai gruppi viene valutata una sola volta al momento della creazione della pianificazione. Le modifiche successive a un gruppo non vengono riflesse. Per risolvere questo problema, eliminare la distribuzione degli aggiornamenti pianificata e ricrearla.

> [!NOTE]
> Per impostazione predefinita, le VM di Windows distribuite da Azure Marketplace ricevono aggiornamenti automatici dal servizio Windows Update. Questo comportamento rimane invariato dopo l'aggiunta di questa soluzione o delle VM di Windows all'area di lavoro. Se gli aggiornamenti non vengono gestiti attivamente con questa soluzione, è applicabile il comportamento predefinito, ovvero gli aggiornamenti vengono applicati automaticamente.

Per evitare che gli aggiornamenti vengano applicati di fuori di una finestra di manutenzione in Ubuntu, riconfigurare il pacchetto Unattended-Upgrade per disabilitare gli aggiornamenti automatici. Per informazioni sulla configurazione, vedere l'[argomento Aggiornamenti automatici nella Guida a Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux (RHEL) su richiesta disponibili in Azure Marketplace vengono registrate per accedere al servizio [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) distribuito in Azure. Altre distribuzioni di Linux devono essere aggiornate dal repository di file online per le distribuzioni seguendo i metodi supportati.  

## <a name="viewing-missing-updates"></a>Visualizzazione degli aggiornamenti mancanti

Fare clic su **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento è elencato e visualizza informazioni relative al numero di computer che richiedono l'aggiornamento e al sistema operativo, oltre a un collegamento per maggiori informazioni. È possibile selezionare ogni aggiornamento e la pagina **Ricerca log** mostra maggiori dettagli sugli aggiornamenti.

## <a name="viewing-update-deployments"></a>Visualizzazione delle distribuzioni degli aggiornamenti

Fare clic su **Distribuzioni di aggiornamenti** per visualizzare l'elenco delle distribuzioni degli aggiornamenti esistenti. Facendo clic su una qualsiasi delle distribuzioni degli aggiornamenti nell'elenco viene aperta la pagina **Esecuzione di distribuzione aggiornamenti** per la specifica distribuzione degli aggiornamenti.

![Panoramica dei risultati della distribuzione degli aggiornamenti](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Creazione di una distribuzione degli aggiornamenti

Creare una nuova distribuzione degli aggiornamenti facendo clic sul pulsante **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata per aprire la pagina **Nuova distribuzione di aggiornamenti**. È necessario specificare i valori per le proprietà nella tabella seguente:

| Proprietà | DESCRIZIONE |
| --- | --- |
| NOME |Nome univoco che identifica la distribuzione degli aggiornamenti. |
|Sistema operativo| Linux o Windows|
| Computer da aggiornare |Selezionare una ricerca salvata o scegliere un computer dall'elenco a discesa e selezionare i singoli computer |
|Classificazione aggiornamento|Selezionare tutte le classificazioni degli aggiornamenti necessarie|
|Aggiornamenti da escludere|Immettere tutte le KB da escludere senza il prefisso "KB"|
|Impostazioni di pianificazione|Selezionare l'ora di inizio e selezionare Una sola volta o Ricorrente per la ricorrenza|
| Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti e superiore a 6 ore |

## <a name="search-logs"></a>Eseguire ricerche nei log

Oltre ai dettagli forniti nel portale, è possibile eseguire ricerche nei log. Con la pagina **Rilevamento modifiche** aperta, fare clic su **Log Analytics**. Verrà visualizzata la pagina **Ricerca log**.

### <a name="sample-queries"></a>Query di esempio

La tabella seguente contiene esempi di ricerche log per i record di aggiornamento raccolti da questa soluzione:

| Query | DESCRIZIONE |
| --- | --- |
|Aggiornamento<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |Tutti i computer con aggiornamenti mancanti<br>Aggiungere uno dei valori seguenti per limitare il sistema operativo:<br>OSType = "Windows"<br>OSType == "Linux" |
| Aggiornamento<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |Aggiornamenti mancanti per un computer specifico. Sostituire il valore con il nome computer.|
| Event<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |Eventi di errore per computer con aggiornamenti critici o della sicurezza necessari mancanti |
| Aggiornamento<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |Aggiornamenti distinti mancanti tra tutti i computer | 
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |Computer con aggiornamenti non riusciti in un'operazione di aggiornamento<br>Aggiungere uno dei valori seguenti per limitare il sistema operativo:<br>OSType = "Windows"<br>OSType == "Linux" | 
| Aggiornamento<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |Elenco di tutti i computer Linux con un aggiornamento pacchetto critico o della sicurezza disponibile | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|Computer che sono stati aggiornati in questa operazione di aggiornamento. Sostituire il valore con il nome della distribuzione degli aggiornamenti | 

## <a name="integrate-with-system-center-configuration-manager"></a>Integrazione con System Center Configuration Manager

I clienti che hanno investito in System Center Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software come parte del loro ciclo di Gestione aggiornamenti software.

Per informazioni su come integrare la soluzione Gestione aggiornamenti di OMS con System Center Configuration Manager, vedere [Integrare System Center Configuration Manager con Gestione aggiornamenti OMS](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Applicazione di patch a computer Linux

Le sezioni seguenti illustrano i potenziali problemi relativi all'applicazione di patch a computer Linux.

### <a name="package-exclusion"></a>Esclusione di pacchetti

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello del sistema operativo possono avvenire tramite pacchetti. Questo può causare esecuzioni di Gestione aggiornamenti in cui viene modificato il numero di versione del sistema operativo. Poiché Gestione aggiornamenti usa gli stessi metodi di aggiornamento dei pacchetti che un amministratore userebbe in locale nel computer Linux, questo comportamento è intenzionale.

Per evitare l'aggiornamento della versione del sistema operativo tramite esecuzioni di Gestione aggiornamenti, usare la funzionalità **Esclusione**.

In Red Hat Enterprise Linux il nome del pacchetto da escludere sarebbe: redhat-release-server.x86_64

![Pacchetti da escludere per Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Patch di sicurezza non applicate

Durante la distribuzione degli aggiornamenti in un computer Linux, è possibile selezionare le classificazioni degli aggiornamenti. In questo modo è possibile filtrare gli aggiornamenti che vengono applicati ai computer che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento. Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, alcuni aggiornamenti possono essere contrassegnati in Gestione aggiornamenti come aggiornamenti con impatto sulla protezione anche se il computer locale non dispone di queste informazioni. Di conseguenza, se si applicano aggiornamenti critici in un computer Linux, è possibile che ci siano aggiornamenti non contrassegnati come aggiornamenti con impatto sulla protezione del computer che non vengono applicati. Tuttavia, Gestione aggiornamenti potrebbe continuare a segnalare tale computer come non conforme dal momento che contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti in base alla classificazione degli aggiornamenti potrebbe non funzionare in openSUSE Linux a causa del diverso modello di applicazione di patch usato.

## <a name="troubleshooting"></a>risoluzione dei problemi

Questa sezione fornisce informazioni sulla risoluzione dei problemi della soluzione Gestione aggiornamenti.

Se si riscontrano problemi durante il tentativo di caricare la soluzione o un macchina virtuale, cercare nel log eventi di **Registri applicazioni e servizi\Operations Manager** gli eventi con ID 4502 e il messaggio di evento contenente **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. La tabella seguente evidenzia i messaggi di errore specifici e una possibile risoluzione per ognuno.  

| Message | Motivo | Soluzione |   
|----------|----------|----------|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.InvalidOperationException: {"Message":"Machine is already<br>registered to a different account. "} | Il computer è già caricato in un'altra area di lavoro per Gestione aggiornamenti | Eseguire la pulizia degli elementi obsoleti [eliminando il gruppo di runbook ibridi](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|  
| Unable to  Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.Net.Http.HttpRequestException: An error occurred while sending the request. ---><br>System.Net.WebException: The underlying connection<br>was closed: An unexpected error<br>occurred on a receive. ---> System.ComponentModel.Win32Exception:<br>The client and server cannot communicate,<br>because they do not possess a common algorithm | Proxy/Gateway/Firewall che blocca la comunicazione | [Esaminare i requisiti di rete](automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. | Proxy/Gateway/Firewall che blocca la comunicazione | [Esaminare i requisiti di rete](automation-offering-get-started.md#network-planning)| 
| The certificate presented by the service <wsid>.oms.opinsights.azure.com<br>was not issued by a certificate authority<br>used for Microsoft services. Contatto<br>your network administrator to see if they are running a proxy that intercepts<br>TLS/SSL communication. |Proxy/Gateway/Firewall che blocca la comunicazione | [Esaminare i requisiti di rete](automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>Failed to create a self-signed certificate. ---><br>System.UnauthorizedAccessException: Access is denied. | Errore di generazione del certificato autofirmato | Verificare che l'account di sistema abbia<br>l'accesso in lettura alla cartella:<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

## <a name="next-steps"></a>Passaggi successivi

Continuare con l'esercitazione sulla gestione degli aggiornamenti per le macchine virtuali Windows.

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-troubleshoot-changes.md)

* Usare le ricerche log in [Log Analytics](../log-analytics/log-analytics-log-searches.md) per visualizzare dati dettagliati sugli aggiornamenti.
* [Creare avvisi](../log-analytics/log-analytics-alerts.md) quando aggiornamenti critici vengono rilevati come mancanti nei computer oppure quando gli aggiornamenti automatici sono disabilitati per un computer.
