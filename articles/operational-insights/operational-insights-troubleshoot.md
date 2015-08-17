<properties
   pageTitle="Risolvere i problemi con Operational Insights"
   description="Informazioni sulla risoluzione dei problemi con Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

#Risolvere i problemi con Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Per semplificare la risoluzione dei problemi, è possibile usare le informazioni disponibili nelle sezioni seguenti. Se il problema specifico non è incluso in questo articolo, vedere il [blog del team di Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnosticare problemi di connessione per Operational Insights

Poiché Microsoft Azure Operational Insights si basa sui dati spostati verso il cloud e dal cloud, i problemi di connessione possono causare un blocco del servizio. Usare le seguenti informazioni per comprendere e risolvere i problemi di connessione.


**Messaggio di errore:** la connettività Internet è stata verificata, ma non è possibile stabilire la connessione al servizio Operational Insights. Riprovare più tardi.

**Cause possibili:** il servizio Operational Insights è in fase di manutenzione. Attendere finché non viene eseguita la manutenzione di Operational Insights. - La rete ha bloccato Operational Insights. Contattare l'amministratore di rete e richiedere l'accesso a Operational Insights oppure usare un altro server come gateway.

**Messaggio di errore:** non è possibile stabilire una connessione Internet. Controllare le impostazioni del proxy.

**Cause possibili:** il server non è connesso a Internet. Controllare lo stato della connettività Internet e connettere il server a Internet. - L’impostazione proxy non è corretta. Per informazioni su come configurare o modificare le impostazioni del proxy, vedere [Configurare le impostazioni di proxy e firewall](operational-insights-proxy-firewall.md). - Il server proxy richiede l’autenticazione. Per informazioni su come configurare Operations Manager per l'uso di un server proxy, vedere [Configurare le impostazioni di proxy e firewall](operational-insights-proxy-firewall.md).


## Risolvere i problemi di individuazione di SQL Server

Se si esegue Microsoft SQL Server 2008 R2 e, nonostante sia stato distribuito l'agente Operations Manager, non vengono visualizzati gli avvisi relativi a questo server, potrebbe essersi verificato un problema di individuazione.

Per verificare se questa è l'origine del problema, controllare se sono presenti i seguenti problemi:

- Nel log eventi di Operations Manager viene visualizzato l'ID evento 4001. Questo evento indica la presenza di una classe non valida.

- Quando in Gestione configurazione SQL Server si visualizzano i servizi SQL Server, appare il messaggio di errore "La chiamata di procedura remota non è riuscita. [0x0800706be]"

Se sono presenti entrambi i problemi, sarà necessario installare SQL Server 2008 R2 Service Pack 2. Per scaricare questo Service Pack, vedere [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) nell'Area download Microsoft.

Dopo l'installazione del Service Pack, i dati di Operational Insights per il server dovrebbero essere visualizzati entro 24 ore.

## Risolvere i problemi degli agenti o del flusso di dati di Operations Manager verso Operational Insights

Le procedure seguenti possono essere usate come guida per semplificare la risoluzione dei problemi degli agenti connessi direttamente o delle distribuzioni di Operations Manager configurate per la segnalazione di dati ad Azure Operational Insights.

### Procedura 1: Verificare il download dei Management Pack nell'ambiente di Operations Manager
>[AZURE.NOTE]Se si usa solo l'agente diretto, è possibile passare alla procedura successiva.

Il numero dei Management Pack visualizzati dipende dalle soluzioni (denominate in precedenza Intelligence Pack) abilitati dal portale di Operational Insights. Cercare la parola chiave "Advisor" o "Intelligence" nel nome. È possibile verificare i Management Pack usando PowerShell di Operations Manager:

```Powershell
Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
```

>[AZURE.NOTE]Se è necessario risolvere i problemi della soluzione Capacity, verificare il *numero* dei Management Pack disponibili il cui nome contiene il termine capacity. Sono presenti due Management Pack con lo stesso nome visualizzato (ma ID interno diverso) inclusi nello stesso bundle di Managament Pack. Se uno dei due non viene importato (spesso a causa di una dipendenza da VMM mancante), non verrà importato nemmeno l'altro Management Pack e non verranno eseguiti altri tentativi di importazione.

Dovrebbero essere visualizzati i tre Management Pack seguenti correlati a 'capacity' - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Storage Data

Se ne vengono visualizzati solo uno o due, non tutti e tre, rimuoverli e attendere 5/10 minuti per un nuovo download e una nuova importazione da parte di Operations Manager e nel frattempo verificare se nel registro eventi vengono visualizzati errori.

### Procedura 2: Verificare se le soluzioni corrette vengono scaricate nell'agente diretto
>[AZURE.NOTE]Se si usa solo Operations Manager, è possibile ignorare questa procedura.

Nell'agente diretto il criterio per la raccolta di soluzioni viene memorizzato nella cache nel percorso **C:\\Programmi\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procedura 3: Verificare se i dati vengono inviati al servizio Advisor (o se almeno viene effettuato il tentativo)
In base all'uso degli agenti connessi direttamente o di Operations Manager, è possibile eseguire la procedura seguente nel computer dell'agente diretto o nel server di gestione di Operations Manager:

1. - Aprire ’Performance Monitor’
2. - Selezionare ’Gruppi di gestione Servizio integrità’
3. - Aggiungere tutti i contatori che iniziano con ’HTTP’

Se la configurazione è corretta, dovrebbero essere visualizzate attività per i contatori, durante il caricamento di eventi e altri elementi di dati (in base alle soluzioni caricate nel portale e ai criteri configurati per la raccolta di log). Questi contatori non devono essere necessariamente occupati in modo continuativo. Se vengono visualizzate pochissime attività, è possibile che non siano stati caricate molte soluzioni o che i criteri della raccolta siano molto semplici.

### Procedura 4: Cercare errori nel server di gestione o nei registri eventi dell'agente diretto
Come passaggio finale, se il problema persiste e non viene segnalata la ricezione di dati da parte del servizio, cercare eventuali errori nel **Visualizzatore eventi**.

Aprire **Visualizzatore eventi** –> **Applicazioni e servizi** –> **Operations Manager** e filtrare in base alle origini eventi: **Advisor**, **Moduli del servizio di integrità**, **HealthService** e **Connettore del servizio** (l’ultimo si applica solo all'agente diretto).

Molti di questi eventi saranno simili in Operations Manager e nell'agente diretto e i passaggi per la risoluzione dei problemi saranno analoghi. L'unica differenza tra Operations Manager e l'agente diretto è costituita dal processo di registrazione (GUI in Operations Manager, combinazione di ID/chiave dell'area di lavoro nell'agente diretto). Dopo la registrazione iniziale, però, vengono scambiati e usati certificati e tutti gli altri aspetti della comunicazione con il servizio sono uguali.

Molti di questi eventi sono quindi applicabili a entrambi i tipi di infrastruttura di creazione report. Di seguito sono elencati gli eventi più comuni.

### Eventi con origine ’Moduli del servizio di integrità’
##### EventID 2138
Il proxy richiede l'autenticazione. Eseguire i passaggi necessari per [configurare i server proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

##### EventID 2137
Operations Manager non riesce a leggere il certificato di autenticazione. Per risolvere il problema relativo ai certificati o agli account RunAs, eseguire di nuovo la registrazione guidata di Advisor.

##### EventID 2132
Corrisponde a **Non autorizzato**. È possibile che si sia verificato un problema con il certificato e/o con la registrazione al servizio. Provare a eseguire di nuovo la registrazione guidata per risolvere i problemi dei certificati e degli account RunAs. Verificare anche che il proxy sia stato impostato in modo da consentire le esclusioni. Per altre informazioni, vedere [Configurare i server proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### EventID 2129
Errore di connessione a causa di una negoziazione SSL non riuscita. Verificare che i sistemi siano configurati per l'uso di TLS e non SSL. È possibile che nel server siano presenti alcune impostazioni SSL anomale relative alla crittografia, in **Avanzate** di Internet Explorer o nel Registro di sistema di Windows in corrispondenza della chiave seguente

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### EventID 2127
Invio dei dati non riuscito con codice errore. Se questo errore si verifica solo una volta ogni tanto, è possibile che si tratti di un problema poco rilevante. È consigliabile monitorare la frequenza con cui si verifica. Se lo stesso errore si ripete molto spesso (ogni 10 minuti circa per un periodo di tempo esteso), potrebbe trattarsi di un problema effettivo. In questo caso, controllare la configurazione della rete e le impostazioni del proxy illustrate in precedenza ed eseguire di nuovo la registrazione guidata. Se l'errore si verifica sporadicamente (ad esempio un paio di volte al giorno), non dovrebbe costituire un problema, poiché i dati verranno accodati e trasmessi di nuovo. Se questa è la frequenza con cui si verifica l'errore, è probabile che si tratti semplicemente di un timeout della rete.

Alcuni codici errore HTTP hanno significati speciali, ad esempio

- la PRIMA volta che un agente diretto di MMA o un server di gestione prova a inviare dati al servizio, otterrà un errore di tipo 500 con codice errore interno 404, ovvero non trovato. Ciò indica che l'area di archiviazione da usare per la nuova area di lavoro non è ancora pronta e che ne è in corso il provisioning. Al successivo tentativo l'area di lavoro sarà pronta e il flusso inizierà a funzionare correttamente (in condizioni normali).
- Il codice errore 403 può indicare un problema di autorizzazione/credenziali e così via.

##### EventID 2128
Risoluzione del nome DNS non riuscita. Il server non riesce a risolvere l'indirizzo Internet a cui deve inviare dati. È possibile che il problema dipenda dalle impostazioni del resolver DNS nel computer, da impostazioni non corrette del proxy o da un errore (temporaneo) del DNS sul provider. Analogamente all'evento precedente, può costituire un problema in base alla frequenza con cui si verifica, ovvero costantemente o una volta ogni tanto.

##### EventID 2130
Timeout. Analogamente all'evento precedente, può costituire un problema in base alla frequenza con cui si verifica, ovvero costantemente o "una volta ogni tanto".

### Eventi con origine 'HealthService'
##### EventID 4511
Impossibile caricare il modulo "System.PublishDataToEndPoint": file non trovato. Inizializzazione di un modulo di tipo "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") non riuscita con codice di errore Impossibile trovare il file specificato. Questo errore indica che nel computer sono presenti file DLL obsoleti, che non includono i moduli necessari. Per risolvere il problema, aggiornare i server di gestione con l'aggiornamento cumulativo più recente.

##### EventID 4502
Arresto anomalo del modulo. Se viene visualizzato questo errore per flussi di lavoro con nomi simili a **CollectInstanceSpace** o **CollectTypeSpace**, è possibile che il server non riesca a inviare alcuni dati di configurazione. Può costituire un problema in base alla frequenza con cui si verifica, ovvero costantemente o "una volta ogni tanto". Se si verifica più di una volta all'ora, si tratta decisamente di un problema. Se questa operazione non riesce solo una o due volte al giorno, non costituisce un problema. In base alla modalità dell'errore effettiva del modulo (la descrizione include informazioni dettagliate) è possibile che si tratti di un errore locale, ad esempio dalla raccolta al database, oppure di un problema relativo all'invio al cloud. Verificare le impostazioni della rete e del proxy e, se il problema persiste, provare a riavviare HealthService.

##### EventID 4501
Arresto anomalo del modulo "System.PublishDataToEndPoint". Un modulo di tipo "System.PublishDataToEndPoint" ha segnalato un errore di tipo 87L. Il modulo era in esecuzione come parte della regola "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" per l'istanza "Operations Manager Management Group" con ID "{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" nel gruppo di gestione "SCOMTEST". Questo errore *non* dovrebbe essere più visualizzato con questo esatto flusso di lavoro, modulo ed errore. Si tratta di [un bug* ora risolto*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Se questo errore viene visualizzato di nuovo, segnalarlo tramite il canale di supporto Microsoft preferito.


### Eventi dall'origine ’Connettore del servizio’
##### EventID 4002
Il servizio ha restituito il codice di stato HTTP 403 in risposta a una query. Contattare l'amministratore del servizio per verificarne l'integrità. L'esecuzione della query verrà tentata di nuovo in seguito. È possibile che il codice di stato 403 venga visualizzato durante la fase di registrazione iniziale dell'agente. Verrà visualizzato un URL analogo al seguente: https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest Il codice errore 403 corrisponde a "non consentito": si tratta in genere di un valore WorkspaceId o una chiave non copiati correttamente oppure l'orologio del computer non è sincronizzato correttamente. Provare a effettuare la sincronizzazione con un riferimento temporale più attendibile e usare la verifica della connettività nell'applet del Pannello di controllo per Microsoft Monitoring Agent per verificare che l'ID e la chiave dell'area di lavoro siano validi.


### Procedura 5: Cercare gli agenti per inviare i rispettivi dati e indicizzarli nel portale
Accedere al portale di Operational Insights, dalla pagina di panoramica passare al riquadro piccolo **Servers and Usage**. Verrà indicato se i gruppi di gestione (e i rispettivi agenti) e gli agenti diretti segnalano i dati nella ricerca log. Il numero di agenti nel riquadro deriva dai dati. Se i computer non forniscono alcuna segnalazione per due settimane, non verranno più rilevati.

Il drill-down passa alla ricerca log e mostra il timestamp dei dati indicizzati più recenti per ogni computer. Sarà quindi possibile esaminare i dati in modo dettagliato. La pianificazione e la velocità di caricamento dei dati possono variare in base alla quantità di raccolta di dati configurata e dalle soluzioni selezionate.

Questa pagina include anche informazioni sulle misurazioni (queste informazioni non usano l'indice di ricerca log ma il sistema di fatturazione e vengono aggiornate ogni due ore circa) relative alle quantità di dati inviati al servizio suddivise in base alle diverse soluzioni.

<!---HONumber=August15_HO6-->