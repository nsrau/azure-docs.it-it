<properties 
	pageTitle="Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione | Azure" 
	description="Informazioni su come usare funzionalità quali l'analisi dell'archiviazione, la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi correlati al servizio Archiviazione di Azure." 
	services="storage" 
	documentationCenter="" 
	authors="dominicbetts" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/16/2015" 
	ms.author="v-dobett"/>

# Monitoraggio, diagnosi e risoluzione dei problemi del servizio Archiviazione Microsoft Azure 

La diagnosi e la risoluzione dei problemi di un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse rispetto agli ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi componenti. In genere, il traffico in rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione, ad esempio tabelle , BLOB, code o file di Archiviazione di Microsoft Azure, oltre ad altri archivi di dati quali i database relazionali e di documenti.

Per gestire in modo efficace tali applicazioni, è necessario monitorarle attivamente e capire in che modo diagnosticare e risolvere i problemi correlati a tutti gli aspetti delle applicazioni e delle tecnologie che impiegano. L'utente del servizio Archiviazione di Azure deve monitorare continuamente i servizi di archiviazione usati dall'applicazione per riscontrare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta insolitamente lenti, e usare la registrazione per raccogliere dati più dettagliati e analizzare i problemi in profondità. Le informazioni di diagnostica ottenute dal monitoraggio e dalla registrazione consentiranno di determinare la causa principale del problema riscontrato dall'applicazione. Sarà quindi possibile identificare il problema e determinare le misure appropriate per risolverlo. Il servizio Archiviazione di Azure è uno dei principali servizi di Azure ed è un componente importante della maggior parte delle soluzioni distribuite dai client nell'infrastruttura di Azure. Questo servizio include funzionalità che consentono di semplificare le attività di monitoraggio, diagnostica e risoluzione dei problemi di archiviazione nelle applicazioni basate su cloud. 

Per una guida interattiva alla risoluzione dei problemi end-to-end in applicazioni di Archiviazione di Azure, vedere la pagina relativa alla [risoluzione dei problemi end-to-end usando Metriche e Registrazione di Archiviazione di Azure, AzCopy e Analizzatore messaggi](../articles/storage-e2e-troubleshooting/).

+ [Introduzione]
	+ [Organizzazione di questa guida]
+ [Monitoraggio del servizio di archiviazione]
	+ [Monitoraggio dello stato del servizio]
	+ [Monitoraggio della capacità]
	+ [Monitoraggio della disponibilità]
	+ [Monitoraggio delle prestazioni]
+ [Diagnosi dei problemi di archiviazione]
	+ [Problemi di stato del servizio]
	+ [Problemi di prestazioni]
	+ [Diagnostica degli errori]
	+ [Problemi dell'emulatore di archiviazione]
	+ [Strumenti di registrazione dell'archiviazione]
	+ [Uso degli strumenti di registrazione in rete]
+ [Traccia end-to-end]
	+ [Correlazione dei dati di log]
	+ [ID richiesta client]
	+ [ID richiesta server]
	+ [Timestamp]
+ [Guida alla risoluzione dei problemi]
	+ [Le metriche indicano un valore AverageE2ELatency alto e un valore AverageServerLatency basso]
	+ [Le metriche indicano un valore AverageE2ELatency basso e un valore AverageServerLatency basso, ma il client riscontra una latenza elevata]
	+ [Le metriche indicano un valore AverageServerLatency alto]
	+ [Si stanno verificando ritardi imprevisti nel recapito dei messaggi di una coda]
	+ [Le metriche indicano un aumento di PercentThrottlingError]
	+ [Le metriche indicano un aumento di PercentTimeoutError]
	+ [Le metriche indicano un aumento di PercentNetworkError]
	+ [Il client sta ricevendo messaggi HTTP 403 (Accesso negato)]
	+ [Il client sta ricevendo messaggi HTTP 404 (Non trovato)]
	+ [Il client sta ricevendo messaggi HTTP 409 (Conflitto)]
	+ [Le metriche indicano un valore PercentSuccess basso o le voci del log di analisi contengono operazioni con stato della transazione ClientOtherErrors]
	+ [Le metriche della capacità indicano un aumento imprevisto dell'uso della capacità di archiviazione]
	+ [Si stanno verificando riavvii imprevisti delle macchine virtuali con un numero elevato di VHD allegati]
	+ [Il problema è dovuto all'uso dell'emulatore di archiviazione per attività di sviluppo o test]
	+ [Si stanno verificando problemi di installazione di Microsoft Azure SDK per .NET]
	+ [Si è verificato un problema diverso con un servizio di archiviazione]
+ [Appendici]
	+ [Appendice 1: Uso di Fiddler per l'acquisizione del traffico HTTP e HTTPS]
	+ [Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]
	+ [Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]
	+ [Appendice 4: Uso di Excel per la visualizzazione di metriche e dati di log]
	+ [Appendice 5: Monitoraggio con Application Insights per Visual Studio Online]

## <a name="introduction"></a>Introduzione

Questa guida spiega come usare funzionalità quali Analisi archiviazione di Azure, la registrazione lato client nella libreria client di Archiviazione di Azure e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi correlati al servizio Archiviazione di Azure.

![][1]

*Figura 1 Monitoraggio, diagnostica e risoluzione dei problemi*

La guida è destinata in particolare agli sviluppatori dei servizi online che usano il servizio Archiviazione di Azure e ai professionisti IT responsabili della gestione di tale servizio online. Gli obiettivi della guida sono:

- Consentire all'utente di mantenere l'integrità e le prestazioni degli account del servizio Archiviazione di Azure
- Fornire all'utente le procedure e gli strumenti necessari per decidere se un problema riscontrato in un'applicazione è correlato al servizio Archiviazione di Azure
- Fornire all'utente un supporto interattivo per la risoluzione dei problemi correlati al servizio Archiviazione di Azure

### <a name="how-this-guide-is-organized"></a>Organizzazione di questa guida

La sezione "[Monitoraggio del servizio di archiviazione]" descrive le modalità di controllo dell'integrità e delle prestazioni del servizio Archiviazione di Azure tramite le metriche di Analisi archiviazione di Azure (Metriche di archiviazione).

La sezione "[Diagnosi dei problemi di archiviazione]" descrive in che modo vengono diagnosticati i problemi usando la funzione di registrazione di Analisi archiviazione di Azure (Registrazione dell'archiviazione). Descrive inoltre in che modo si abilita la registrazione lato client usando gli strumenti di una delle librerie client, ad esempio la libreria del client di archiviazione per .NET o Azure SDK per Java.

La sezione "[Traccia end-to-end]" descrive come si possono correlare le informazioni contenute in vari file di log e i dati delle metriche.

La sezione "[Guida alla risoluzione dei problemi]" fornisce indicazioni utili per identificare alcuni dei più frequenti problemi relativi all'archiviazione.

La sezione "[Appendici]" include informazioni sull'uso di altri strumenti quali Wireshark e Netmon per l'analisi dei dati dei pacchetti di rete, Fiddler per l'analisi dei messaggi HTTP/HTTPS e Microsoft Message Analyzer per la correlazione dei dati di log.


## <a name="monitoring-your-storage-service"></a>Monitoraggio del servizio di archiviazione

Per chi ha familiarità con il monitoraggio delle prestazioni di Windows, le metriche di Archiviazione di Azure corrispondono ai contatori di Windows Performance Monitor. Metriche di archiviazione comprende un set completo di metriche (contatori nella terminologia di Performance Monitor di Windows), tra cui la disponibilità del servizio, il numero totale di richieste al servizio e la percentuale di richieste eseguite correttamente. Per l'elenco completo delle metriche disponibili, vedere <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schema di tabella della metrica di Analisi di archiviazione</a> su MSDN. È possibile specificare se si desidera che il servizio di archiviazione raccolga e aggreghi le metriche ogni ora o ogni minuto. Per altre informazioni sulle modalità di abilitazione delle metriche e sul monitoraggio degli account di archiviazione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">Abilitazione di Metriche di archiviazione e visualizzazione dei dati di metrica</a> su MSDN.

È possibile scegliere quali metriche orarie visualizzare nel portale di Azure e configurare le regole che inviano una notifica agli amministratori ogni volta che una metrica oraria supera una soglia particolare. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure</a>. Il servizio di archiviazione raccoglie le metriche usando l'approccio  migliore, ma è possibile che  non registri tutte le operazioni di archiviazione.

La figura 2 mostra la pagina Monitoraggio del portale in cui sono visualizzate metriche quali la disponibilità, le richieste totali e i numeri di latenza media per un account di archiviazione. È stata anche configurata una regola di notifica per inviare un avviso a un amministratore nel caso in cui la disponibilità scenda al di sotto di un determinato livello. Dalla visualizzazione di questi dati, un'area di analisi possibile è la percentuale di successo dei servizi della tabella che risulta inferiore al 100%. Per altre informazioni, vedere la sezione "[Le metriche indicano un valore PercentSuccess basso o le voci del log di analisi contengono operazioni con stato della transazione ClientOtherErrors]".

![][2]

*Figura 2 Visualizzazione delle metriche di archiviazione nel portale*

È necessario monitorare continuamente le applicazioni Azure per assicurarsi che siano integre e funzionino come previsto.

- Stabilire alcune metriche per applicazione di base che consentano di confrontare i dati correnti e identificare eventuali modifiche significative del comportamento dell'archiviazione di Azure e dell'applicazione. In molti casi i valori delle metriche di base saranno specifici della singola applicazione e dovranno essere definiti quando si testano le prestazioni dell'applicazione.
- Registrare metriche al minuto e usarle per monitorare attivamente eventuali anomalie ed errori imprevisti, ad esempio picchi dei conteggi degli errori o della frequenza delle richieste. 
- Registrare metriche orarie e usarle per monitorare valori medi come la media dei conteggi degli errori e della frequenza delle richieste. 
- Esaminare i potenziali problemi usando gli strumenti di diagnostica descritti più avanti nella sezione "[Diagnosi dei problemi di archiviazione]."

I grafici della figura 3 illustrano in che modo il calcolo della media delle metriche orarie può nascondere dei picchi di attività. Le metriche orarie sembrano indicare una frequenza costante delle richieste, mentre le metriche al minuto rivelano le fluttuazioni che si verificano in realtà.

![][3]

La parte rimanente di questa sezione indica quali metriche è necessario monitorare e perché. 

### <a name="monitoring-service-health"></a>Monitoraggio dello stato del servizio

Usare il portale di Microsoft Azure all'indirizzo <a href="https://portal.azure.com" target="_blank">https://portal.azure.com</a> per visualizzare lo stato del servizio di archiviazione (e altri servizi di Azure) in tutte le aree Azure del mondo. Questo consente all'utente di vedere immediatamente se un problema al di fuori del proprio controllo incide sul servizio di archiviazione nell'area usata per l'applicazione. 

Il portale di Azure può inoltre fornire notifiche sugli incidenti che influiscono sui vari servizi di Azure.
Nota: queste informazioni in precedenza erano disponibili, insieme ai dati cronologici, sul dashboard del servizio di Azure all'indirizzo <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>.

Benché il portale raccolga le informazioni sullo stato dall'interno dei data center di Azure (monitoraggio da interno a esterno), si può anche considerare l'adozione di un approccio da esterno a interno per generare transazioni sintetiche che accedano periodicamente all'applicazione Web in hosting su Azure da più postazioni. I servizi offerti da <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">Keynote</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a> e Application Insights per Visual Studio Online sono esempi di tale approccio da esterno a interno. Per altre informazioni su Application Insights per Visual Studio Online, vedere la sezione "[Appendice 5: Monitoraggio con Application Insights per Visual Studio Online]."

### <a name="monitoring-capacity"></a>Monitoraggio della capacità

Lo strumento Metriche di archiviazione memorizza solo le metriche di capacità per il servizio BLOB poiché gli oggetti BLOB normalmente raccolgono la proporzione maggiore di dati archiviati. Attualmente non è possibile usare Metriche di archiviazione per monitorare la capacità di tabelle e code. Questi dati sono contenuti nella tabella **$MetricsCapacityBlob** se è stato attivato il monitoraggio per il servizio BLOB. Lo strumento Metriche di archiviazione registra i dati una volta al giorno ed è possibile usare il valore di **RowKey** per determinare se la riga contiene un'entità che fa riferimento ai dati utente (valore **data**) o dati analitici (valore **analytics**). Ogni entità archiviata contiene informazioni sulla quantità di spazio di archiviazione usato (valore **Capacity** in byte) e il numero attuale di contenitori (**ContainerCount**) e oggetti BLOB (**ObjectCount**) usati nell'account di archiviazione. Per altre informazioni sulle metriche di capacità archiviate nella tabella **$MetricsCapacityBlob**, vedere <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schema di tabella della metrica di Analisi di archiviazione</a> su MSDN.

> [AZURE.NOTE] È necessario monitorare questi valori per essere avvisati tempestivamente quando si stanno raggiungendo i limiti di capacità dell'account di archiviazione. Nel portale di Azure, nella pagina **Monitoraggio** dell'account di archiviazione, è possibile aggiungere regole di avviso per ricevere una notifica nel caso in cui l'archiviazione degli aggregati superi o sia inferiore alle soglie specificate.

Per sapere come stimare le dimensioni dei vari oggetti di archiviazione, ad esempio gli oggetti BLOB, vedere il post del blog su <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">larghezza di banda, transazioni e capacità in relazione alla fatturazione del servizio Archiviazione di Azure</a>.

### <a name="monitoring-availability"></a>Monitoraggio della disponibilità

Per verificare la disponibilità dei servizi di archiviazione nel proprio account di archiviazione, è necessario monitorare il valore della colonna della **disponibilità** nelle tabelle delle metriche orarie o al minuto: **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La colonna della **disponibilità** contiene un valore percentuale che indica la disponibilità del servizio o dell'operazione API rappresentata dalla riga. Il valore **RowKey** indica se la riga contiene metriche per il servizio nel suo insieme o per un'operazione API specifica. 

Tutti i valori inferiori al 100% indicano che alcune richieste di archiviazione hanno esito negativo. È possibile vedere perché le richieste hanno esito negativo esaminando le altre colonne di dati della metrica che indicano il numero di richieste con tipi diversi di errore, ad esempio **ServerTimeoutError**. La **disponibilità** può scendere momentaneamente al di sotto del 100% per diversi motivi, ad esempio per un timeout temporaneo del server mentre il servizio sposta le partizioni per bilanciare meglio il carico della richiesta. La logica di ripetizione dell'applicazione client dovrebbe gestire tali condizioni intermittenti. La pagina <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> contiene i tipi di transazione che le metriche di archiviazione includono nel calcolo della **disponibilità**.

Nel portale di Azure, nella pagina **Monitoraggio** dell'account di archiviazione, è possibile aggiungere regole di avviso per ricevere una notifica nel caso in cui la **disponibilità** di un servizio scenda al di sotto di una soglia specificata.

La sezione "[Guida alla risoluzione dei problemi]" di questa guida descrive alcuni dei problemi di disponibilità che si riscontrano più di frequente nel servizio di archiviazione.

### <a name="monitoring-performance"></a>Monitoraggio delle prestazioni

Per monitorare le prestazioni dei servizi di archiviazione è possibile usare le seguenti metriche estratte dalle tabelle delle metriche orarie e al minuto.

- I valori in **AverageE2ELatency** e **AverageServerLatency** indicano il tempo medio impiegato dal servizio di archiviazione o dal tipo di operazione API per elaborare le richieste. **AverageE2ELatency** è una misura della latenza end-to-end che include il tempo impiegato a leggere la richiesta e inviare la risposta sommato al tempo impiegato per elaborare la richiesta. Di conseguenza, questa misura include la latenza di rete quando la richiesta raggiunge il servizio di archiviazione; **AverageServerLatency** è una misura del tempo di elaborazione ed esclude quindi qualsiasi latenza di rete relativa alla comunicazione con il client. Vedere la sezione "[Le metriche indicano un valore AverageE2ELatency alto e un valore AverageServerLatency basso]" più avanti in questa guida per sapere perché esiste una differenza significativa tra i due valori.
- I valori presenti nelle colonne **TotalIngress** e **TotalEgress** mostrano la quantità totale di dati, in byte, in entrata e in uscita dal servizio di archiviazione o tramite un tipo specifico di operazione API.
- I valori presenti nella colonna **TotalRequests** indicano il numero totale di richieste ricevute dal servizio di archiviazione o dall'operazione API. **TotalRequests** è il numero totale di richieste ricevute dal servizio di archiviazione. 

In genere, questi valori vengono monitorati per verificare la presenza di eventuali cambiamenti che possono indicare l'esistenza di problemi da sottoporre ad analisi.

Nel portale di Azure, nella pagina **Monitoraggio** dell'account di archiviazione, è possibile aggiungere regole di avviso per ricevere una notifica nel caso in cui una delle metriche di prestazione del servizio sia inferiore o superiore a una soglia specificata.

La sezione "[Guida alla risoluzione dei problemi]" di questa guida descrive alcuni dei problemi che si riscontrano più di frequente nel servizio di archiviazione in relazione alle prestazioni.


## <a name="diagnosing-storage-issues"></a>Diagnosi dei problemi di archiviazione

Esistono alcuni sintomi che indicano la presenza di un problema dell'applicazione, tra cui:

- Un errore grave che causa l'arresto anomalo o interrompe l'esecuzione dell'applicazione
- Cambiamenti significativi dei valori di base delle metriche monitorate, come descritto nella sezione precedente "[Monitoraggio del servizio di archiviazione]" 
- Segnalazione da parte degli utenti del fatto che una determinata operazione non viene completata come previsto o che non è possibile usare una funzione
- Errori generati all'interno dell'applicazione che appaiono nei file di log o vengono segnalati con altri metodi

Normalmente i problemi correlati al servizio Archiviazione di Azure rientrano in una delle seguenti quattro grandi categorie:

- L'applicazione presenta un problema di prestazioni, segnalato dagli utenti o rivelato da modifiche nelle metriche delle prestazioni.
- Si è verificato un problema con l'infrastruttura di archiviazione di Azure in una o più aree. 
- L'applicazione ha riscontrato un errore, segnalato dagli utenti o rivelato dall'aumento delle metriche di conteggio degli errori monitorate.
- Durante lo sviluppo e i test può essere usato l'emulatore di archiviazione locale: è possibile che si verifichino alcuni problemi connessi specificamente all'uso dell'emulatore.

Nelle sezioni che seguono vengono descritte le procedure da adottare per la diagnosi e la risoluzione dei problemi in ciascuna delle quattro categorie. La sezione "[Guida alla risoluzione dei problemi]" più avanti in questa guida fornisce maggiori dettagli su alcuni dei problemi che si possono verificare più di frequente.

### <a name="service-health-issues"></a>Problemi di stato del servizio

I problemi di stato del servizio di solito sono al di fuori del controllo dell'utente. Il portale di Azure fornisce informazioni sui problemi che si possono verificare usando i servizi Azure, inclusi i servizi di archiviazione. Se si è scelta un'archiviazione con accesso in lettura e ridondanza geografica durante la creazione dell'account, nel caso in cui i dati non siano disponibili nella posizione primaria, l'applicazione può passare temporaneamente alla copia di sola lettura nella posizione secondaria. Per fare ciò, l'applicazione deve essere in grado di passare dalla posizione primaria a quella secondaria e viceversa nonché di gestire i dati di sola lettura in una modalità con funzionalità ridotta. Le librerie client del servizio Archiviazione di Azure consentono di definire criteri di ripetizione per leggere i dati da un'archiviazione secondaria nel caso in cui quella primaria non funzioni. L'applicazione deve inoltre essere in grado di stabilire se i dati nella posizione secondaria risultano coerenti. Per altre informazioni, vedere il post del blog relativo alle <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">opzioni di ridondanza e archiviazione ridondante con accesso in lettura e ridondanza geografica del servizio Archiviazione di Microsoft Azure</a>.

### <a name="performance-issues"></a>Problemi di prestazioni

Le prestazioni di un'applicazione possono essere soggettive, soprattutto dal punto di vista dell'utente. Di conseguenza, è importante avere a disposizione metriche di base che consentano di localizzare e identificare i problemi di prestazioni. Sono molti i fattori che possono influire sulle prestazioni di un servizio Archiviazione di Azure dal punto di vista dell'applicazione client. Tali fattori possono agire nel servizio di archiviazione, nel client o nell'infrastruttura di rete, quindi è importante disporre di una strategia in grado di identificare l'origine del problema di prestazioni.

Dopo aver identificato l'origine probabile della causa del problema in base alle metriche, è possibile usare i file di log per trovare informazioni dettagliate che consentano di diagnosticare e risolvere meglio il problema.

La sezione "[Guida alla risoluzione dei problemi]" più avanti in questa guida fornisce ulteriori informazioni su alcuni dei problemi che si possono verificare più di frequente in relazione alle prestazioni.

### <a name="diagnosing-errors"></a>Diagnostica degli errori

Gli utenti dell'applicazione possono notificare gli errori segnalati dall'applicazione client. Lo strumento Metriche di archiviazione registra inoltre i conteggi dei diversi tipi di errore riscontrati dai servizi di archiviazione, ad esempio **NetworkError**, **ClientTimeoutError** o **AuthorizationError**. Benché Metriche di archiviazione registrino solo i conteggi dei diversi tipi di errore, è possibile ottenere più dettagli sulle singole richieste esaminando i file di log di lato server, lato client e rete. In genere, il codice di stato HTTP restituito dal servizio di archiviazione fornisce un'indicazione del motivo dell'esito negativo della richiesta. 

> [AZURE.NOTE] Tenere presente che si possono verificare alcuni errori intermittenti: ad esempio, errori dovuti a condizioni temporanee della rete o a errori dell'applicazione.

Le seguenti risorse su MSDN sono utili per l'interpretazione dei codici di errore e di stato correlati all'archiviazione:

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codici di errore comuni dell'API REST</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Codici di errore del servizio BLOB</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Codici di errore del servizio di accodamento</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Codici di errore del servizio tabelle</a>

### <a name="storage-emulator-issues"></a>Problemi dell'emulatore di archiviazione

Azure SDK include un emulatore di archiviazione che può essere eseguito su una workstation di sviluppo. L'emulatore simula la maggior parte delle attività del servizio Archiviazione di Azure ed è utile in fase di sviluppo e test, poiché consente di eseguire le applicazioni che usano il servizio Archiviazione Azure senza che siano necessari una sottoscrizione di Azure e un account di archiviazione di Azure.

La sezione "[Guida alla risoluzione dei problemi]" di questa guida descrive alcuni dei problemi che si riscontrano più di frequente usando l'emulatore di archiviazione.

### <a name="storage-logging-tools"></a>Strumenti di registrazione dell'archiviazione

La registrazione dell'archiviazione consente di registrare sul lato server le richieste di archiviazione presenti nell'account di archiviazione di Azure. Per altre informazioni sulle modalità di abilitazione della registrazione lato server e dell'accesso ai dati registrati, vedere la sezione relativa all'<a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">uso della registrazione lato server</a> su MSDN.

La libreria del client di archiviazione per .NET consente di raccogliere i dati della registrazione lato client correlati alle operazioni di archiviazione eseguite dall'applicazione. Per altre informazioni sulle modalità di abilitazione della registrazione lato client e dell'accesso ai dati registrati, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">Registrazione lato client con la libreria client di archiviazione .NET</a> su MSDN.

> [AZURE.NOTE] In alcune circostanze, ad esempio in caso di mancata autorizzazione della firma di accesso condiviso, un utente può segnalare un errore per il quale non sono reperibili i dati della richiesta nei file di log dell'archiviazione lato server. È possibile usare le funzionalità di registrazione della libreria del client di archiviazione per verificare se la causa del problema è sul client o usare gli strumenti di monitoraggio della rete per esaminare la rete.

### <a name="using-network-logging-tools"></a>Uso degli strumenti di registrazione in rete

È possibile acquisire il traffico tra client e server per fornire informazioni dettagliate sui dati scambiati da client e server e sulle condizione della rete sottostante. Sono disponibili alcuni strumenti utili per la registrazione in rete:

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) è un proxy di debug Web gratuito che consente di esaminare le intestazioni e i dati di payload dei messaggi HTTP e HTTPS di richiesta e risposta. Per altre informazioni, vedere "[Appendice 1: Uso di Fiddler per l'acquisizione del traffico HTTP e HTTPS]".
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/it-it/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/it-it/download/details.aspx?id=4865</a>) e Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) sono strumenti gratuiti per l'analisi dei protocolli di rete che consentono di visualizzare informazioni dettagliate sui pacchetti per un'ampia gamma di prodotti di rete. Per altre informazioni su Wireshark, vedere "[Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]".
- Microsoft Message Analyzer è uno strumento di Microsoft che sostituisce Netmon e che, oltre ad acquisire i dati dei pacchetti di rete, consente di visualizzare e analizzare i dati di log acquisiti da altri strumenti. Per altre informazioni, vedere "[Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]".
- Per eseguire un test di base delle connessioni per verificare che il computer client sia in grado di connettersi al servizio Archiviazione di Azure in rete, non è possibile usare il normale strumento **ping** sul client. Tuttavia, è possibile usare lo strumento **tcping** per verificare la connettività. **Tcping** può essere scaricato all'indirizzo <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>.

In molti casi, i dati di log provenienti dalla registrazione dell'archiviazione e dalla libreria del client di archiviazione saranno sufficienti per diagnosticare un problema, ma in alcune situazione può essere necessario disporre di informazioni più dettagliate rispetto a quelle fornite da questi strumenti. Ad esempio, l'uso di Fiddler per visualizzare i messaggi HTTP e HTTPS consente di visualizzare i dati di intestazione e di payload inviati e ricevuti dai servizi di archiviazione e quindi di esaminare il modo in cui un'applicazione client ritenti le operazioni di archiviazione. Gli strumenti di analisi dei protocolli, come Wireshark, funzionano a livello di pacchetto e consentono di visualizzare i dati TCP, quindi di risolvere i problemi dovuti alla perdita di pacchetti e a errori di connessione. Message Analyzer è in grado di funzionare sia sul livello HTTP che sul livello TCP.

## <a name="end-to-end-tracing"></a>Traccia end-to-end

La funzionalità di traccia end-to-end basata sull'uso di una varietà di file di log è una tecnica utile per l'analisi dei potenziali problemi. È possibile usare le informazioni su data e ora estratte dai dati delle metriche come indicazione del punto da cui deve partire la ricerca nei file di log dei dettagli necessari per la risoluzione del problema.
	
### <a name="correlating-log-data"></a>Correlazione dei dati di log

Quando si visualizzano i file di log delle applicazioni client, le tracce di rete e la registrazione dell'archiviazione lato server, è essenziale poter creare una correlazione delle richieste in tutti i file di log. I file di log includono diversi campi che possono essere utili come identificatori di correlazioni. Il campo dell'ID richiesta client è il più utile ai fini della correlazione delle voci nei diversi file di log. Tuttavia, in alcuni casi può essere utile usare l'ID richiesta server o i timestamp. Le sezioni che seguono forniscono ulteriori dettagli su queste opzioni.

### <a name="client-request-id"></a>ID richiesta client

La libreria del client di archiviazione genera automaticamente un ID richiesta client univoco per ogni richiesta.

- Nel file di log lato client creato dalla libreria client di archiviazione, tale ID appare nel campo **ID richiesta client** di ogni voce del file log correlato alla richiesta.
- In una traccia di rete, ad esempio quella acquisita con Fiddler, l'ID richiesta client è visibile nei messaggi di richiesta come valore dell'intestazione HTTP **x-ms-client-request-id**.
- Nel log di registrazione dell'archiviazione lato server, l'ID richiesta client appare nella colonna relativa a tale ID.

> [AZURE.NOTE] È possibile che più richieste condividano lo stesso ID richiesta client poiché il client può assegnare questo valore, benché la libreria del client di archiviazione assegni 
> automaticamente un nuovo valore. Nel caso in cui il client ripeta il tentativo, tutte le ripetizioni condivideranno lo stesso ID richiesta client. Se il client invia un batch, il batch avrà un unico ID richiesta client.


### <a name="server-request-id"></a>ID richiesta server

Il servizio di archiviazione genera automaticamente gli ID richiesta server.

- Nel log di registrazione dell'archiviazione lato server, l'ID richiesta server appare nella colonna dell'**intestazione dell'ID richiesta**.
- In una traccia di rete, ad esempio quella acquisita con Fiddler, l'ID richiesta server appare nei messaggi di risposta come valore dell'intestazione HTTP **x-ms-request-id**.
- Nel log lato client creato dalla libreria del client di archiviazione, l'ID richiesta server appare nella colonna del **testo dell'operazione** per la voce del log che mostra i dettagli della risposta del server.

> [AZURE.NOTE] Il servizio di archiviazione assegna sempre un ID richiesta server univoco a ogni richiesta che riceve, quindi ogni tentativo ripetuto dal client e ogni operazione inclusa in un batch hanno un ID richiesta server univoco.

Se la libreria del client di archiviazione genera un'eccezione **StorageException** nel client, la proprietà **RequestInformation** contiene un oggetto **RequestResult** che include una proprietà **ServiceRequestID**. È inoltre possibile accedere a un oggetto **RequestResult** da un'istanza **OperationContext**.

L'esempio di codice che segue mostra come impostare un valore **ClientRequestId** personalizzato allegando un oggetto **OperationContext** alla richiesta al servizio di archiviazione. Mostra inoltre come recuperare il valore **ServerRequestId** dal messaggio di risposta. 

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid. 
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message); 
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID); 
        }
    }


### <a name="timestamps"></a>Timestamp

È possibile usare i timestamp per individuare le voci di log correlate, ma è importante fare attenzione a eventuali sfasamenti di orario tra il client e il server. Eseguire la ricerca delle voci corrispondenti sul lato server con 15 minuti in più o in meno in base al timestamp sul client. Ricordare che i metadati BLOB per gli oggetti BLOB contenenti metriche indicano l'intervallo di tempo per le metriche memorizzate nell'oggetto BLOB e questo è utile se si usano molti oggetti BLOB di metriche per lo stesso minuto o la stessa ora.

## <a name="troubleshooting-guidance"></a>Guida alla risoluzione dei problemi

Questa sezione fornisce un supporto per la diagnosi e la risoluzione di alcuni dei problemi più comuni che si possono verificare nell'applicazione usando il servizio Archiviazione di Azure. Usare l'elenco che segue per individuare le informazioni pertinenti a un problema specifico.

**Albero delle decisioni per la risoluzione dei problemi**

----------

Il problema riguarda le prestazioni di uno dei servizi di archiviazione?

- [Le metriche indicano un valore AverageE2ELatency alto e un valore AverageServerLatency basso]
- [Le metriche indicano un valore AverageE2ELatency basso e un valore AverageServerLatency basso, ma il client riscontra una latenza elevata]
- [Le metriche indicano un valore AverageServerLatency alto]
- [Si stanno verificando ritardi imprevisti nel recapito dei messaggi di una coda]

----------

Il problema riguarda la disponibilità di uno dei servizi di archiviazione?

- [Le metriche indicano un aumento di PercentThrottlingError]
- [Le metriche indicano un aumento di PercentTimeoutError]
- [Le metriche indicano un aumento di PercentNetworkError]

----------

L'applicazione client riceve una risposta HTTP 4XX (ad esempio 404) da un servizio di archiviazione?

- [Il client sta ricevendo messaggi HTTP 403 (Accesso negato)]
- [Il client sta ricevendo messaggi HTTP 404 (Non trovato)]
- [Il client sta ricevendo messaggi HTTP 409 (Conflitto)]

----------

[Le metriche indicano un valore PercentSuccess basso o le voci del log di analisi contengono operazioni con stato della transazione ClientOtherErrors]

----------

[Le metriche della capacità indicano un aumento imprevisto dell'uso della capacità di archiviazione]

----------

[Si stanno verificando riavvii imprevisti delle macchine virtuali con un numero elevato di VHD allegati]

----------

[Il problema è dovuto all'uso dell'emulatore di archiviazione per attività di sviluppo o test]

----------

[Si stanno verificando problemi di installazione di Microsoft Azure SDK per .NET]

----------

[Si è verificato un problema diverso con un servizio di archiviazione]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Le metriche indicano un valore AverageE2ELatency alto e un valore AverageServerLatency basso

L'illustrazione dello strumento di monitoraggio del portale mostra un esempio in cui il valore **AverageE2ELatency** è notevolmente superiore al valore **AverageServerLatency**.

![][4]

Si noti che il servizio di archiviazione calcola solo la metrica **AverageE2ELatency** per le richieste eseguite correttamente e, a differenza di **AverageServerLatency**, include il tempo impiegato dal client per inviare i dati e ricevere una conferma dal servizio di archiviazione. Di conseguenza, una differenza tra **AverageE2ELatency** e **AverageServerLatency** potrebbe essere dovuta al fatto che l'applicazione è lenta a rispondere oppure alle condizioni della rete.

> [AZURE.NOTE] È inoltre possibile visualizzare i valori **E2ELatency** e **ServerLatency** per le singole operazioni di archiviazione nei dati dei file di log della registrazione dell'archiviazione.

#### Analisi dei problemi di prestazioni del client

I motivi possibili per cui il client risponde lentamente includono la quantità limitata di connessioni o thread disponibili. Il problema può essere risolto modificando il codice del client in modo che sia più efficiente (ad esempio usando chiamate asincrone al servizio di archiviazione) o usando una macchina virtuale più grande (con più core e più memoria).

Per i servizi di tabelle e accodamento, l'algoritmo Nagle può inoltre causare la presenza di un valore **AverageE2ELatency** elevato rispetto al valore **AverageServerLatency**: per altre informazioni, vedere il post relativo al <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">comportamento dell'algoritmo Nagle nei confronti delle piccole richieste</a> nel blog del team di Archiviazione di Microsoft Azure. È possibile disabilitare l'algoritmo Nagle nel codice usando la classe **ServicePointManager** nello spazio dei nomi **System.Net**. Eseguire questa operazione prima di effettuare chiamate ai servizi di tabelle o accodamento nell'applicazione poiché non ha effetto sulle connessioni già aperte. L'esempio seguente è ricavato dal metodo **Application_Start** in un ruolo di lavoro.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Controllare i file di log lato client per vedere quante richieste vengono inviate dall'applicazione client e verificare se sono presenti colli di bottiglia delle prestazioni generali di .NET, tra cui CPU, garbage collection, uso della rete o memoria. Come punto di partenza per la risoluzione dei problemi delle applicazioni client .NET, vedere <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">Debug, traccia e profilatura</a> su MSDN.

#### Analisi dei problemi di latenza di rete

In genere la latenza end-to-end elevata causata dalla rete è dovuta a condizioni temporanee. Per l'analisi dei problemi transitori e persistenti della rete, ad esempio le interruzioni della trasmissione dei pacchetti, sono disponibili strumenti come Wireshark o Microsoft Message Analyzer.

Per altre informazioni sull'uso di Wireshark per la risoluzione dei problemi di rete, vedere "[Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]".

Per altre informazioni sull'uso di Microsoft Message Analyzer per la risoluzione dei problemi di rete, vedere "[Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]".

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Le metriche indicano un valore AverageE2ELatency basso e un valore AverageServerLatency basso, ma il client riscontra una latenza elevata

In questo scenario, la causa più probabile è un ritardo nelle richieste di archiviazione che arrivano al servizio di archiviazione. È necessario verificare perché le richieste del client non riescono a passare attraverso il servizio BLOB. 

I motivi possibili per cui il client ritarda l'invio delle richieste includono la quantità limitata di connessioni o thread disponibili. È inoltre necessario verificare se il client sta effettuando più tentativi e in tal caso esaminare il motivo. Questa verifica può essere eseguita a livello di codice mediante una ricerca nell'oggetto **OperationContext** associato alla richiesta e il recupero del valore **ServerRequestId**. Per altre informazioni, vedere l'esempio di codice nella sezione "[ID richiesta server]."

Se non si riscontrano problemi nel client, occorre verificare la presenza di potenziali problemi della rete, ad esempio la perdita di pacchetti. Per esaminare i possibili problemi della rete, sono disponibili strumenti come Wireshark o Microsoft Message Analyzer.

Per altre informazioni sull'uso di Wireshark per la risoluzione dei problemi di rete, vedere "[Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]".

Per altre informazioni sull'uso di Microsoft Message Analyzer per la risoluzione dei problemi di rete, vedere "[Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]".

### <a name="metrics-show-high-AverageServerLatency"></a>Le metriche indicano un valore AverageServerLatency alto

Nel caso in cui il valore **AverageServerLatency** sia elevato per le richieste di download di BLOB, è necessario usare il file di log della registrazione dell'archiviazione per verificare se esistono richieste ripetute per lo stesso oggetto BLOB (o insieme di oggetti BLOB). Per le richieste di caricamento di BLOB, è necessario verificare le dimensioni dei blocchi usate dal client (ad esempio, i blocchi inferiori a 64 Kb possono causare sovraccarichi a mano che anche le letture non vengano eseguite su blocchi inferiori a 64 Kb) e se più client stanno caricando blocchi nello stesso BLOB parallelo. Verificare anche le metriche al minuto per i picchi del numero di richieste che determinano il superamento degli obiettivi di scalabilità al secondo: vedere anche "[Le metriche indicano un aumento di PercentTimeoutError]." 

Se si riscontra un valore **AverageServerLatency** elevato per le richieste di download di BLOB quando sono presenti ripetute richieste allo stesso BLOB o insieme di BLOB, è necessario considerare la possibilità di memorizzare nella cache gli oggetti BLOB usando il servizio cache di Azure o la rete per la distribuzione di contenuti di Azure. Per le richieste di caricamento, è possibile ottimizzare la velocità usando blocchi di dimensioni maggiori. Per le query sulle tabelle, è inoltre possibile implementare la memorizzazione nella cache lato client sui client che eseguono le stesse operazioni di query e i cui dati non cambiano di frequente. 

Valori elevati di **AverageServerLatency** possono essere anche sintomo della presenza di tabelle o query progettate in modo non corretto, che causano operazioni di scansione o seguono l'antipattern append/prepend. Per altre informazioni, vedere "[Le metriche indicano un aumento di PercentThrottlingError]". 

> [AZURE.NOTE] Un elenco di controllo completo, che include anche altri problemi da prendere in considerazione, è disponibile nella sezione relativa alla progettazione di applicazioni basate su servizi di archiviazione scalabili ed efficienti. 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Si stanno verificando ritardi imprevisti nel recapito dei messaggi di una coda

Se si riscontra un ritardo tra l'orario in cui un'applicazione aggiunge un messaggio a una coda e l'orario in cui il messaggio diventa disponibile per la lettura dalla coda, è necessario prendere in considerazione la procedura che segue per diagnosticare il problema.

- Verificare che l'applicazione aggiunga correttamente il messaggio alla coda. Verificare che l'applicazione non ripeta il metodo **AddMessage** più volte prima di ottenere l'esito positivo. I file di log della libreria del client di archiviazione mostrano tutti i tentativi ripetuti delle operazioni di archiviazione.
- Verificare che non siano sfasamenti di orario tra il ruolo di lavoro che aggiunge il messaggio alla coda e il ruolo di lavoro che legge il messaggio dalla coda e che determina un apparente ritardo dell'elaborazione.
- Verificare se il ruolo di lavoro che legge i messaggi dalla coda presenta degli errori. Se un client di accodamento chiama il metodo **GetMessage** ma non risponde con una conferma, il messaggio resterà invisibile nella coda fino alla scadenza del periodo **invisibilityTimeout**. A questo punto, il messaggio diventa di nuovo disponibile per l'elaborazione.
- Verificare se la lunghezza della coda aumenta con il tempo. Ciò si può verificare se non sono disponibili ruoli di lavoro sufficienti per elaborare tutti i messaggi che altri ruoli di lavoro inseriscono nella coda. È inoltre necessario controllare le metriche, per verificare se le richieste di eliminazione hanno esito negativo, e il conteggio dei messaggi da rimuovere dalla coda, che potrebbe indicare l'esistenza di ripetuti tentativi non riusciti di eliminare il messaggio.
- Esaminare i file di log della registrazione dell'archiviazione per verificare se esistono operazioni di accodamento con valori **E2ELatency** e **ServerLatency** superiori al previsto in un periodo di tempo più lungo del consueto.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Le metriche indicano un aumento di PercentThrottlingError

Gli errori di limitazione si verificano quando si superano gli obiettivi di scalabilità di un servizio di archiviazione. In questo modo il servizio di archiviazione assicura che nessun client o tenant possa usare il servizio a spese di altri. Vedere <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Obiettivi di scalabilità e prestazioni di Azure</a> per maggiori dettagli sugli obiettivi di scalabilità per gli account di archiviazione e gli obiettivi di prestazioni per le partizioni all'interno degli account di archiviazione. 

Se la metrica **PercentThrottlingError** indica un aumento della percentuale di richieste non riuscite con errore di limitazione, è necessario esaminare una di queste condizioni:

- [Aumento temporaneo di PercentThrottlingError]
- [Aumento permanente di PercentThrottlingError]

Un aumento di **PercentThrottlingError** spesso si verifica contemporaneamente a un aumento del numero di richieste di archiviazione o quando si esegue un test di carico iniziale dell'applicazione. L'aumento si può manifestare anche nel client come messaggio di stato HTTP "503 Server Busy" o "500 Operation Timeout" delle operazioni di archiviazione.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento temporaneo di PercentThrottlingError

Se si notano picchi del valore di **PercentThrottlingError** che coincidono con periodi di intensa attività dell'applicazione, è necessario implementare una strategia di interruzione esponenziale (non lineare) per le ripetizioni dei tentativi nel client:questa operazione ridurrà il carico immediato sulla partizione e consentirà all'applicazione di contenere i picchi di traffico. Per altre informazioni sulle modalità di implementazione dei criteri di ripetizione con la libreria del client di archiviazione, vedere la sezione relativa allo <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">spazio dei nomi Microsoft.WindowsAzure.Storage.RetryPolicies</a> su MSDN.

> [AZURE.NOTE] Si possono verificare anche picchi del valore di **PercentThrottlingError** che non coincidono con periodi di attività intensa dell'applicazione: la causa più probabile è che il servizio di archiviazione sposti le partizioni in modo da migliorare il bilanciamento del carico.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente di PercentThrottlingError

Se si registra un valore costantemente elevato di **PercentThrottlingError** a seguito di un aumento permanente dei volumi delle transazioni o quando si eseguono i test iniziali del carico per l'applicazione, è necessario valutare in che modo l'applicazione sta usando le partizioni di memoria e se sta raggiungendo gli obiettivi di scalabilità per un account di archiviazione. Ad esempio, se si riscontrano errori di limitazione su una coda (che viene considerata come singola partizione), è opportuno prendere in considerazione l'uso di code aggiuntive per suddividere le transazioni in più partizioni. Se gli errori di limitazione si verificano su una tabella, può essere utile usare uno schema di partizionamento diverso per suddividere le transazioni in più partizioni, usando una gamma più ampia di valori delle chiavi di partizione. Una causa comune di questo problema è l'antipattern prepend/append in cui la data viene selezionata come chiave di partizione e quindi tutti i dati relativi a un determinato giorno vengono scritti su una sola partizione: Può essere opportuno prendere in considerazione una progettazione diversa della partizione o valutare se l'uso dell'archiviazione BLOB non sia una soluzione migliore. È inoltre necessario verificare se la limitazione avviene come risultato di picchi del traffico e in che modo adeguare il modello di richiesta in uso.

Se si distribuiscono le transazioni in più partizioni, tenere presenti i limiti di scalabilità impostati per l'account di archiviazione. Ad esempio, se sono state usate dieci code che elaborano ciascuna un massimo di 2000 messaggi da 1 KB al secondo, si raggiungerà il limite complessivo di 20.000 messaggi al secondo per l'account di archiviazione. Se è necessario elaborare più di 20.000 entità al secondo, è consigliabile usare più account di archiviazione. Tenere presente inoltre che le dimensioni delle richieste e delle entità influiscono sul momento in cui il servizio di archiviazione limita i client: più grandi sono le richieste e le entità, prima avviene la limitazione.

Anche una progettazione insufficiente delle query può causare il raggiungimento dei limiti di scalabilità per le partizioni delle tabelle. Ad esempio, una query con un filtro che seleziona solo l'uno per cento delle entità di una partizione ma esegue la scansione di tutte le entità della partizione dovrà accedere a ogni entità. Ogni lettura di entità viene aggiunta al calcolo del numero totale delle transazioni di quella partizione, quindi si possono raggiungere facilmente gli obiettivi di scalabilità.

> [AZURE.NOTE] I test delle prestazioni dovrebbero rivelare eventuali progettazioni inefficaci delle query nell'applicazione.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Le metriche indicano un aumento di PercentTimeoutError

Le metriche indicano un aumento di **PercentTimeoutError** per uno dei servizi di archiviazione. Allo stesso tempo, il client riceve una grande quantità di messaggi di stato HTTP "500 Operation Timeout" dalle operazioni di archiviazione.

> [AZURE.NOTE] È possibile che vengano temporaneamente visualizzati errori di timeout perché il servizio di archiviazione bilancia il carico delle richieste spostando una partizione su un nuovo server.
 
La metrica **PercentTimeoutError** è una combinazione delle metriche seguenti: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** e **SASServerTimeoutError**.

I timeout del server sono causati da un errore del server. I timeout del client si verificano perché un'operazione eseguita sul server ha superato il timeout specificato dal client. Ad esempio, un client che usa la libreria del client di archiviazione può impostare un timeout per un'operazione usando la proprietà **ServerTimeout** della classe **QueueRequestOptions**.

I timeout del server indicano un problema con il servizio di archiviazione che deve essere esaminato. È possibile usare le metriche per verificare se si raggiungono i limiti di scalabilità del servizio e identificare eventuali picchi di traffico che possono causare il problema. Se il problema è intermittente, può essere dovuto all'attività di bilanciamento del carico nel servizio. Se il problema è persistente e non è causato dal fatto che l'applicazione raggiunge i limiti di scalabilità del servizio, sarà necessario rivolgersi all'assistenza. Per i timeout del client, è necessario decidere se il timeout è impostato su un valore appropriato nel client e modificare il valore del timeout impostato nel client o valutare in che modo è possibile migliorare le prestazioni delle operazioni nel servizio di archiviazione, ad esempio ottimizzando le query sulle tabelle o riducendo le dimensioni dei messaggi.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Le metriche indicano un aumento di PercentNetworkError

Le metriche indicano un aumento di **PercentNetworkError** per uno dei servizi di archiviazione. La metrica **PercentNetworkError** è una combinazione delle metriche seguenti: **NetworkError**, **AnonymousNetworkError** e **SASNetworkError**. L'errore si verifica se il servizio di archiviazione rileva un errore della rete quando il client esegue una richiesta di archiviazione.

La causa più comune dell'errore è la disconnessione del client prima della scadenza del timeout nel servizio di archiviazione. È necessario esaminare il codice nel client per capire perché e quando il client si disconnette dal servizio di archiviazione. Per esaminare i problemi di connessione dal client, è possibile usare anche Wireshark, Microsoft Message Analyzer o Tcping. Questi strumenti sono descritti nelle [Appendici].

### <a name="the-client-is-receiving-403-messages"></a>Il client sta ricevendo messaggi HTTP 403 (Accesso negato)

Se l'applicazione client genera errori HTTP 403 (Accesso negato), probabilmente il client sta usando una firma di accesso condiviso (SAS, Shared Access Signature) scaduta per inviare una richiesta di archiviazione (benché esistano altre cause possibili, come sfasamento di orario, chiavi non valide e intestazioni vuote). Se la causa è una chiave SAS scaduta, non si vedrà alcuna voce nei dati di log della registrazione dell'archiviazione lato server. La tabella che segue mostra un esempio del file di log lato client generato dalla libreria del client di archiviazione in cui è illustrato il problema in corso:

<table>
 <tr>
    <td><b>Origine</b></td>
    <td><b>Livello di dettaglio</b></td>
    <td><b>Livello di dettaglio</b></td>
    <td><b>ID richiesta client</b></td>
    <td><b>Testo operazione</b></td>
 </tr>
 <tr>
    <td>Microsoft.WindowsAzure.Storage</td>
    <td>Informazioni</td>
    <td>3</td>
    <td>85d077ab-...</td>
    <td>Avvio operazione con posizione primaria in base alla modalità di posizionamento PrimaryOnly.</td>
 </tr>
 <tr>
    <td>Microsoft.WindowsAzure.Storage</td>
    <td>Informazioni</td>
    <td>3</td>
    <td>85d077ab -...</td>
    <td>Avvio richiesta sincrona a https://domemaildist.blob.core.windows.netazure<br>imblobcontainer/blobCreatedViaSAS.txt?
	    <br>sv=2014-02-14&amp;sr=c&amp;si=mypolicy
	    <br>&amp;sig=OFnd4Rd7z01fIvh%
	    <br>2BmcR6zbudIH2F5Ikm%
	    <br>2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14.</td>
 </tr>
 <tr>
    <td>Microsoft.WindowsAzure.Storage</td>
    <td>Informazioni</td>
    <td>3</td>
    <td>85d077ab -...</td>
    <td>In attesa di risposta.</td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Avviso 
  </td>
  <td>
  2 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  Eccezione generata in attesa di risposta: il server remoto ha restituito un errore (403) Accesso negato.. 
  </td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Informazioni 
  </td>
  <td>
  3 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  Risposta ricevuta. Codice stato = 403, ID richiesta = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . 
  </td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Avviso 
  </td>
  <td>
  2 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  Eccezione generata durante l'operazione: il server remoto ha restituito un errore (403) Accesso negato.. 
  </td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Informazioni 
  </td>
  <td>
  3 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  Verifica se l'operazione deve essere ritentata. Conteggio tentativi = 0, codice stato HTTP = 403, Eccezione = il server remoto ha restituito un errore (403) Accesso negato.. 
  </td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Informazioni 
  </td>
  <td>
  3 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  La posizione successiva è stata impostata come primaria, in base alla modalità di posizionamento. 
  </td>
 </tr>
 <tr>
  <td>
  Microsoft.WindowsAzure.Storage 
  </td>
  <td>
  Errore 
  </td>
  <td>
  1 
  </td>
  <td>
  85d077ab -... 
  </td>
  <td>
  Il criterio di ripetizione non ha consentito un nuovo tentativo. Errore con server remoto che ha restituito un errore (403) Accesso negato. 
  </td>
 </tr>
</table>

In questo scenario, è necessario verificare perché il token SAS scade prima che il client invii il token al server:

- Di solito non è consigliabile impostare un orario di inizio quando si crea una SAS che un client deve usare immediatamente. Se esistono piccoli sfasamenti di orario tra l'host che genera la SAS usando l'ora attuale e il servizio di archiviazione, è possibile che il servizio di archiviazione riceva una SAS non ancora valida.
- Non impostare tempi troppo brevi per la scadenza di una SAS. Come già detto, eventuali piccole differenze di orario tra l'host che genera la SAS e il servizio di archiviazione possono causa un'apparente scadenza della SAS prima del tempo.
- Il parametro della versione nella chiave della firma di accesso condiviso (ad esempio **sv=2012-02-12**) deve corrispondere alla versione della libreria del client di archiviazione in uso. Usare sempre la versione più recente della libreria del client di archiviazione. Per altre informazioni sul controllo della versione del token della firma di accesso condiviso e delle dipendenze nella versione della libreria dei client, vedere <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx" target="_blank"></a>. 
- Se si rigenerano le chiavi di accesso all'archiviazione (fare clic su **Gestisci chiavi di accesso** in qualsiasi pagina dell'account di archiviazione sul portale di Azure), i token della firma di accesso condiviso già esistenti possono essere invalidati. Questo può essere un problema se si generano token SAS con tempo di scadenza lungo per le applicazioni client da memorizzare nella cache.

Se si usa la libreria del client di archiviazione per generare i token della firma di accesso condiviso, sarà semplice creare un token valido. Tuttavia, se si usa l'API REST di archiviazione e si creano i token della firma di accesso condiviso manualmente, si consiglia di leggere con attenzione l'argomento <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Delega dell'accesso con una firma di accesso condiviso</a> su MSDN.

### <a name="the-client-is-receiving-404-messages"></a>Il client sta ricevendo messaggi HTTP 404 (Non trovato)
Se l'applicazione client riceve un messaggio HTTP 404 (Non trovato) dal server, significa che l'oggetto che il client sta tentando di usare (ad esempio un oggetto entità, tabella, BLOB, contenitore o coda) non esiste nel servizio di archiviazione. I motivi possono essere diversi, ad esempio:

- [Il client o un altro processo ha eliminato in precedenza l'oggetto]
- [Si è verificato un problema di autenticazione della firma di accesso condiviso]
- [Il codice JavaScript lato client non è autorizzato ad accedere all'oggetto]
- [Errore della rete]

#### <a name="client-previously-deleted-the-object"></a>Il client o un altro processo ha eliminato in precedenza l'oggetto
Negli scenari in cui il client tenta di leggere, aggiornare o eliminare i dati in un servizio di archiviazione, normalmente è facile identificare nei file di log lato server un'operazione precedente che ha eliminato l'oggetto in questione dal servizio di archiviazione. Molto spesso i dati del log indicano che un altro utente o processo ha eliminato l'oggetto. Nel file di log della registrazione dell'archiviazione sul lato server, le colonne del tipo di operazione e della chiave dell'oggetto richiesto indicano quando un client ha eliminato un oggetto.

Nello scenario in cui un client tenta di inserire un oggetto può non essere subito evidente il motivo per cui si riceve una risposta HTTP 404 (Non trovato) se il client sta creando un nuovo oggetto. Tuttavia, se il client sta creando un oggetto BLOB deve essere in grado di trovare il relativo contenitore, se sta creando un messaggio deve essere in grado di trovare una coda e se sta aggiungendo una riga deve essere in grado di trovare la tabella. 

Usare il log lato client generato dalla libreria del client di archiviazione per avere maggiori dettagli sul momento in cui il client invia richieste specifiche al servizio di archiviazione.

Il seguente file di log lato client generato dalla libreria del client di archiviazione illustra il problema che si verifica quando il client non è in grado di trovare il contenitore per il BLOB che sta creando. Il file di log include dettagli relativi alle seguenti operazioni di archiviazione:

<table>
  <tr>
    <td>
      <b>ID richiesta</b>
    </td>
    <td>
      <b>Operazione</b>
    </td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td>
    Metodo <b>DeleteIfExists</b> per eliminare il contenitore BLOB. Notare che questa operazione include una richiesta 
    <b>HEAD</b> per verificare l'esistenza del contenitore.</td>
  </tr>
  <tr>
    <td>e2d06d78...</td>
    <td>
    Metodo <b>CreateIfNotExists</b> per creare il contenitore BLOB. Notare che questa operazione include una richiesta 
    <b>HEAD</b> per verificare l'esistenza del contenitore. La richiesta 
    <b>HEAD</b> restituisce un messaggio 404 ma continua.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td>
    Metodo <b>UploadFromStream</b> per creare l'oggetto BLOB. La richiesta 
    <b>PUT</b> ha esito negativo con messaggio 404</td>
  </tr>
</table>

Voci del log:

<table>
  <tr>
    <td>
      <b>ID richiesta</b>
    </td>
    <td>
      <b>Testo operazione</b>
    </td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Avvio richiesta sincrona a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11
    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> In attesa di risposta.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Risposta ricevuta. Codice stato = 200, ID richiesta = eeead849-...Content-MD5 = , ETag =
    &quot;0x8D14D2DC63D059B&quot;.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Intestazioni della risposta elaborate correttamente, si procede con il resto dell'operazione.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Download del corpo della risposta.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Operazione completata correttamente.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Avvio richiesta sincrona a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12
    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> In attesa di risposta.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Risposta ricevuta. Codice stato = 202, ID richiesta = 6ab2a4cf-..., Content-MD5 = , ETag =.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Intestazioni della risposta elaborate correttamente, si procede con il resto dell'operazione.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Download del corpo della risposta.</td>
  </tr>
  <tr>
    <td>07b26a5d-...</td>
    <td> Operazione completata correttamente.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Avvio richiesta asincrona a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12
    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> In attesa di risposta.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Avvio richiesta sincrona a https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> StringToSign =
    PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun
    2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Preparazione della scrittura dei dati della richiesta.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Eccezione generata in attesa di risposta: il server remoto ha restituito un errore (404) Non trovato..</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Risposta ricevuta. Codice stato = 404, ID richiesta = 353ae3bc-..., Content-MD5 = , ETag =.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Intestazioni della risposta elaborate correttamente, si procede con il resto dell'operazione.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Download del corpo della risposta.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Operazione completata correttamente.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Avvio richiesta asincrona a https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12
    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> In attesa di risposta.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Scrittura dei dati della richiesta.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> In attesa di risposta.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Eccezione generata in attesa di risposta: il server remoto ha restituito un errore (409) Conflitto..</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Risposta ricevuta. Codice stato = 409, ID richiesta = c27da20e-..., Content-MD5 = , ETag =.</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Download del corpo della risposta con errore.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Eccezione generata in attesa di risposta: il server remoto ha restituito un errore (404) Non trovato..</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Risposta ricevuta. Codice stato = 404, ID richiesta = 0eaeab3e-..., Content-MD5 = , ETag =.</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Eccezione generata durante l'operazione: il server remoto ha restituito un errore (404) Non trovato..</td>
  </tr>
  <tr>
    <td>de8b1c3c-...</td>
    <td> Il criterio di ripetizione non ha consentito un nuovo tentativo. Errore con server remoto che ha restituito un errore (404) Non trovato..</td>
  </tr>
  <tr>
    <td>e2d06d78-...</td>
    <td> Il criterio di ripetizione non ha consentito un nuovo tentativo. Errore con server remoto che ha restituito un errore (409) Conflitto..</td>
  </tr>
</table>

In questo esempio, il log indica che il client sta eseguendo un'interfoliazione delle richieste del metodo **CreateIfNotExists** (ID richiesta e2d06d78...) con le richieste del metodo **UploadFromStream** (de8b1c3c-...); ciò accade perché l'applicazione client sta richiamando questi metodi in modo asincrono. È necessario modificare il codice asincrono nel client per assicurarsi che crei il contenitore prima di tentare di caricare dati in un BLOB in tale contenitore. La soluzione migliore sarebbe creare prima tutti i contenitori.

#### <a name="SAS-authorization-issue"></a>Si è verificato un problema di autenticazione della firma di accesso condiviso

Se l'applicazione client tenta di usare una chiave SAS che non include le autorizzazioni necessarie per l'operazione, il servizio di archiviazione restituisce un messaggio HTTP 404 (Non trovato) al client. Allo stesso tempo, verrà visualizzato in valore diverso da zero per **SASAuthorizationError** nelle metriche.

La tabella che segue mostra un esempio di messaggio del log lato server generato dal file di log della registrazione dell'archiviazione:

<table>
  <tr>
    <td>Orario di inizio richiesta</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Tipo di operazione</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Stato della richiesta</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Stato codice HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Tipo di autenticazione</td>
    <td>Sas</td>
  </tr>
  <tr>
    <td>Tipo di servizio</td>
    <td>BLOB</td>
  </tr>
  <tr>
    <td>URL richiesta</td>
    <td>
    https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp;sr=c&amp;amp;si=mypolicy&amp;amp;sig=XXXXX&amp;amp;api-version=2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>Intestazione ID richiesta</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID richiesta client</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

È necessario verificare perché l'applicazione client sta tentando di eseguire un'operazione per la quale non dispone di autorizzazioni.

#### <a name="JavaScript-code-does-not-have-permission"></a>Il codice JavaScript lato client non è autorizzato ad accedere all'oggetto

Se si usa un client JavaScript e il servizio di archiviazione restituisce messaggi HTTP 404, verificare la presenza dei seguenti errori JavaScript nel browser:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] È possibile usare gli strumenti di sviluppo F-12 in Internet Explorer per tracciare i messaggi scambiati dal browser e dal servizio di archiviazione quando si individuano e risolvono i problemi di JavaScript sul lato client. 

Tali errori si verificano perché il browser Web implementa come restrizione di sicurezza la <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">regola della stessa origine</a> che impedisce che una pagina Web esegua una chiamata a un'API in un dominio diverso da quello da cui proviene la pagina. 

Per evitare il problema di JavaScript, è possibile configurare il servizio CORS (Cross Origin Resource Sharing) per il servizio di archiviazione a cui accede il client. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/windowsazure/dn535601.aspx" target="_blank">Supporto della condivisione delle risorse tra le origini (CORS) per il servizio Archiviazione di Azure</a> su MSDN.

L'esempio di codice che segue indica come configurare il servizio BLOB per consentire l'esecuzione di JavaScript nel dominio Contoso in modo da poter accedere a un BLOB nel servizio di archiviazione BLOB:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Errore della rete 

In alcune circostanze, la perdita di pacchetti di rete può causare la restituzione da parte del servizio di archiviazione di messaggi HTTP 404 per il client. Ad esempio, quando l'applicazione client elimina un'entità dal servizio tabelle, il client genera un'eccezione di archiviazione segnalando un messaggio di stato "HTTP 404 (Non trovato)" ricevuto dal servizio tabelle. Quando si esamina la tabella nel servizio di archiviazione tabelle, si può notare che il servizio ha eliminato l'entità come richiesto.

I dettagli dell'eccezione nel client includono l'ID richiesta (7e84f12d...) assegnato al servizio tabella per la richiesta: è possibile usare queste informazioni per individuare i dettagli della richiesta nei log di archiviazione sul lato server, eseguendo una ricerca nella colonna **request-id-header** del file di log. È inoltre possibile usare le metriche per identificare il momento in cui si verificano errori come questo e quindi eseguire una ricerca nei file di log in base all'orario in cui le metriche hanno registrato l'errore. Questa voce del file di log indica che l'eliminazione non è riuscita con messaggio di stato HTTP 404 (altro errore del client). La stessa voce del log include anche l'ID richiesta generato dal client nella colonna **client-request-id** (813ea74f...).

Il log lato server contiene anche un'altra voce con lo stesso valore **client-request-id** (813ea74f...) perché venga eseguita correttamente l'eliminazione della stessa entità dallo stesso client. L'operazione di eliminazione corretta viene eseguita poco prima della richiesta di eliminazione non riuscita.

La causa più probabile di tale situazione è il fatto che il client ha inviato correttamente al servizio tabelle una richiesta di eliminazione per l'entità, ma non ha ricevuto alcuna conferma da parte del server (forse per un problema temporaneo della rete). Il client ha quindi ritentato l'operazione usando lo stesso valore **client-request-id** e il nuovo tentativo non è riuscito perché l'entità è già stata eliminata.

Se questo problema si verifica di frequente, è necessario capire perché il client non riesce a ricevere le risposte di conferma dal servizio tabelle. Se il problema è intermittente, occorre bloccare l'errore "HTTP (404) Non trovato" e registrarlo nel log del client, ma consentire al client di continuare.

### <a name="the-client-is-receiving-409-messages"></a>Il client sta ricevendo messaggi HTTP 409 (Conflitto)

La tabella che segue contiene un estratto del log lato server per due operazioni del client: **DeleteIfExists** seguita immediatamente da **CreateIfNotExists** usando lo stesso nome di contenitore BLOB. Si noti che ogni operazione del client determina l'invio di due richieste al server, prima una richiesta **GetContainerProperties** per verificare se il contenitore esiste, quindi una richiesta **DeleteContainer** o **CreateContainer**.

<table>
  <tr>
    <td>
      <b>Timestamp</b>
    </td>
    <td>
      <b>Operazione</b>
    </td>
    <td>
      <b>Risultato</b>
    </td>
    <td>
      <b>Nome del contenitore</b>
    </td>
    <td>
      <b>ID richiesta client</b>
    </td>
  </tr>
  <tr>
    <td>05:10:13.7167225</td>
    <td>GetContainerProperties</td>
    <td>200</td>
    <td>mmcont</td>
    <td>c9f52c89-...</td>
  </tr>
  <tr>
    <td>05:10:13.8167325</td>
    <td>DeleteContainer</td>
    <td>202</td>
    <td>mmcont</td>
    <td>c9f52c89-...</td>
  </tr>
  <tr>
    <td>05:10:13.8987407</td>
    <td>GetContainerProperties</td>
    <td>404</td>
    <td>mmcont</td>
    <td>bc881924-...</td>
  </tr>
  <tr>
    <td>05:10:14.2147723</td>
    <td>CreateContainer</td>
    <td>409</td>
    <td>mmcont</td>
    <td>bc881924-...</td>
  </tr>
</table>

Il codice nell'applicazione client elimina e quindi immediatamente ricrea un contenitore BLOB usando lo stesso nome: il metodo **CreateIfNotExists** (ID richiesta client bc881924-...) ha esito negativo con errore HTTP 409 (Conflitto). Quando un client elimina contenitori BLOB, tabelle o code, trascorre un breve periodo di tempo prima che il nome sia di nuovo disponibile.

L'applicazione client deve usare nomi univoci per i contenitori ogni volta che crea nuovi contenitori se il pattern delete/recreate è comune.

### <a name="metrics-show-low-percent-success"></a>Le metriche indicano un valore PercentSuccess basso o le voci del log di analisi contengono operazioni con stato della transazione ClientOtherErrors

La metrica **PercentSuccess** acquisisce la percentuale di operazioni eseguite correttamente in base al relativo codice di stato HTTP. Le operazioni con codici di stato 2XX hanno avuto esito positivo, mentre le operazioni con codici 3XX, 4XX e 5XX hanno avuto esito negativo e riducono il valore della metrica **PercentSucess**. Nei file di log dell'archiviazione sul lato server, queste operazioni vengono registrate con stato della transazione **ClientOtherErrors**. 

È importante notare che queste operazioni sono state completate correttamente e quindi non influiscono sulle altre metriche, ad esempio la disponibilità. Alcuni esempi di operazioni eseguite correttamente ma che possono restituire codici di stato HTTP negativi sono:
- **ResourceNotFound** (Non trovato 404), ad esempio da una richiesta GET a un oggetto BLOB che non esiste.
- **ResouceAlreadyExists** (Conflitto 409), ad esempio da un'operazione **CreateIfNotExist** quando la risorsa esiste già.
- **ConditionNotMet** (Non modificato 304), ad esempio da un'operazione condizionale come quando un client invia un valore **ETag** e un'intestazione HTTP **If-None-Match** per richiedere un'immagine solo se è stata aggiornata dall'ultima operazione.

Per l'elenco dei codici di errore API REST che i servizi di archiviazione restituiscono più di frequente, vedere la pagina <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codici di errore comuni dell'API REST</a>. 

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Le metriche della capacità indicano un aumento imprevisto dell'uso della capacità di archiviazione


Se si riscontrano cambiamenti improvvisi e imprevisti dell'uso della capacità nel proprio account di archiviazione, per scoprire quali sono le cause per prima cosa analizzare le metriche di disponibilità. Ad esempio, un aumento del numero di richieste di eliminazione non riuscite può determinare un aumento dell'archiviazione BLOB in uso perché le operazioni di pulizia specifiche dell'applicazione, che avrebbero dovuto liberare spazio, non funzionano come previsto (ad esempio perché i token SAS usare per liberare spazio sono scaduti). 

### <a name="you-are-experiencing-unexpected-reboots"></a>Si stanno verificando riavvii imprevisti delle macchine virtuali con un numero elevato di VHD allegati

Se una macchina virtuale contiene numerosi dischi rigidi virtuali (VHD, Virtual Hard Disk) allegati nello stesso account di archiviazione, è possibile che vengano superati gli obiettivi di scalabilità dell'account di archiviazione determinando un errore della macchina virtuale. Controllare le metriche al minuto per l'account di archiviazione (**TotalRequests**/**TotalIngress**/**TotalEgress**) per verificare se esistono picchi che superino gli obiettivi di scalabilità previsti per un account di archiviazione. Vedere la sezione "[Le metriche indicano un aumento di PercentThrottlingError]" per sapere come si stabilisce se si è verificata una limitazione nell'account di archiviazione. 

In generale, ogni singola operazione di input o output eseguita su un VHD da una macchina virtuale viene trasformata in un'operazione **Get Page** o **Put Page** nel BLOB di pagine sottostante. Di conseguenza, è possibile usare le IOPS previste per l'ambiente per stabilire quanti VHD si possono avere in un singolo account di archiviazione in base al comportamento specifico dell'applicazione. Non è consigliabile usare più di 40 dischi virtuali in un unico account di archiviazione. Vedere <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Obiettivi di scalabilità e prestazioni di Azure</a> per i dettagli relativi agli attuali obiettivi di scalabilità per gli account di archiviazione, in particolare la velocità totale delle richieste e la larghezza di banda totale per il tipo di account di archiviazione in uso. 
Se si superano gli obiettivi di scalabilità per il proprio account di archiviazione, sarà necessario inserire i VHD in più account diversi per ridurre l'attività in ogni singolo account.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Il problema è dovuto all'uso dell'emulatore di archiviazione per attività di sviluppo o test

L'emulatore di archiviazione normalmente si usa durante lo sviluppo e i test per evitare i requisiti di un account di archiviazione di Azure. I problemi che si riscontrano più di frequente usando l'emulatore di archiviazione sono:

- [La funzionalità "X" non funziona nell'emulatore di archiviazione]
- [Un messaggio indica che il valore di una delle intestazioni HTTP non è nel formato corretto quando si usa l'emulatore di archiviazione]
- [L'esecuzione dell'emulatore di archiviazione richiede privilegi amministrativi]

#### <a name="feature-X-is-not-working"></a>La funzionalità "X" non funziona nell'emulatore di archiviazione

L'emulatore di archiviazione non supporta tutte le funzioni del servizio Archiviazione di Azure, ad esempio il servizio file. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/gg433135.aspx" target="_blank">Differenze tra l'emulatore di archiviazione e i servizi di archiviazione di Azure</a> su MSDN.

Per le funzioni non supportate dall'emulatore di archiviazione, usare il servizio Archiviazione di Azure nel cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Un messaggio indica che il valore di una delle intestazioni HTTP non è nel formato corretto quando si usa l'emulatore di archiviazione

Si sta testando l'applicazione che usa la libreria del client di archiviazione con l'emulatore di archiviazione locale e le chiamate del metodo come **CreateIfNotExists** hanno esito negativo con un errore dovuto al formato errato di una delle intestazioni HTTP. Ciò indica che la versione dell'emulatore di archiviazione non supporta la versione della libreria del client di archiviazione in uso. La libreria del client di archiviazione aggiunge l'intestazione **x-ms-version** a tutte le richieste che effettua. Se l'emulatore di archiviazione non riconosce il valore nell'intestazione **x-ms-version**, rifiuta la richiesta.

Usare i log della libreria client di archiviazione per visualizzare il valore di **x-ms-version header** inviato. È inoltre possibile visualizzare il valore di **x-ms-version header** se si usa Fiddler per la traccia delle richieste provenienti dall'applicazione client.

Questa situazione di solito si verifica quando si installa e si usa la versione più recente della libreria del client di archiviazione senza aggiornare l'emulatore di archiviazione. È necessario installare la versione più recente dell'emulatore di archiviazione o usare l'archiviazione cloud anziché l'emulatore per lo sviluppo e i test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>L'esecuzione dell'emulatore di archiviazione richiede privilegi amministrativi

Vengono richieste le credenziali di amministratore quando si esegue l'emulatore di archiviazione. Questo si verifica solo quando si inizializza l'emulatore di archiviazione per la prima volta. Una volta inizializzato l'emulatore di archiviazione, non saranno necessari privilegi amministrativi per eseguirlo di nuovo. 

Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/gg433132.aspx" target="_blank">Inizializzare l'emulatore di archiviazione tramite lo strumento da riga di comando</a> su MSDN. È possibile inizializzare l'emulatore di archiviazione anche in Visual Studio, che richiede comunque privilegi amministrativi.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Si stanno verificando problemi di installazione di Microsoft Azure SDK per .NET

Quando si installa l'SDK, si verifica un errore se si tenta di installare l'emulatore di archiviazione sul computer locale. Il log di installazione contiene uno dei seguenti messaggi:

- CAQuietExec:  Error: Unable to access SQL instance
- CAQuietExec:  Error: Unable to create database

La causa è un problema nell'installazione LocalDB già esistente. Per impostazione predefinita, l'emulatore di archiviazione usa LocalDB per mantenere persistenti i dati quando simula il servizio Archiviazione di Azure. Per reimpostare l'istanza LocalDB, eseguire i comandi indicati di seguito in una finestra del prompt dei comandi prima di provare a installare l'SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

Il comando **delete** rimuove tutti i vecchi file di database dalle installazioni precedenti dell'emulatore di archiviazione.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Si è verificato un problema diverso con un servizio di archiviazione

Se le sezioni precedenti non includono il problema riscontrato con un servizio di archiviazione, attenersi alla seguente procedura per diagnosticare e risolvere il problema.

- Controllare le metriche per verificare se sono presenti variazioni del normale comportamento di base. Dalle metriche è possibile determinare se il problema è transitorio o permanente e quali operazioni di archiviazione sono interessate dal problema.
- Usare le informazioni delle metriche per cercare nei dati dei log lato server informazioni più dettagliate su eventuali errori riscontrati. Queste informazioni possono essere utili per diagnosticare e risolvere il problema.
- Se le informazioni dei log lato server non sono sufficienti per risolvere efficacemente il problema, usare i log lato client della libreria del client di archiviazione per esaminare il comportamento dell'applicazione client e strumenti come Fiddler, Wireshark e Microsoft Message Analyzer per esaminare la rete.

Per altre informazioni sull'uso di Fiddler, vedere "[Appendice 1: Uso di Fiddler per l'acquisizione del traffico HTTP e HTTPS]".

Per altre informazioni sull'uso di Wireshark, vedere "[Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]".

Per altre informazioni sull'uso di Microsoft Message Analyzer, vedere "[Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]".

## <a name="appendices"></a>Appendici

Le appendici descrivono alcuni strumenti che possono risultare utili per la diagnosi e la risoluzione dei problemi del servizio Archiviazione di Azure (e di altri servizi). Tali strumenti non fanno parte del servizio Archiviazione di Azure e alcuni sono prodotti di terze parti. Quindi, gli strumenti descritti nelle appendici non sono coperti da eventuali contratti di assistenza stipulati per Microsoft Azure o il servizio Archiviazione di Azure ed è necessario che l'utente, come parte del proprio processo di valutazione, esamini le opzioni di licenza e di assistenza disponibili presso i fornitori degli strumenti.

### <a name="appendix-1"></a>Appendice 1: Uso di Fiddler per l'acquisizione del traffico HTTP e HTTPS

Fiddler è uno strumento utile per l'analisi del traffico HTTP e HTTPS tra l'applicazione client e il servizio Archiviazione di Azure in uso. Fiddler può essere scaricato all'indirizzo <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [AZURE.NOTE] Fiddler è in grado di decodificare il traffico HTTPS. È consigliabile leggere attentamente la documentazione di Fiddler per capire in che modo esegue questa operazione e quali sono le implicazioni in termini di sicurezza.

Questa appendice descrive brevemente la procedura di configurazione di Fiddler per l'acquisizione del traffico tra il computer locale in cui è installato Fiddler e il servizio Archiviazione di Azure.

Dopo l'avvio, Fiddler inizia ad acquisire il traffico HTTP e HTTPS sul computer locale. Di seguito sono riportati alcuni comandi utili per il controllo di Fiddler:

- Interruzione e avvio dell'acquisizione del traffico. Nel menu principale accedere a **File** e fare clic su **Capture Traffic** per attivare e disattivare l'acquisizione.
- Salvare i dati del traffico acquisiti. Nel menu principale, accedere a **File**, fare clic su **Save** e quindi su **All Sessions**: in questo modo il traffico viene salvato in un file di archivio sessione. Tale file potrà essere ricaricato successivamente per l'analisi o inviato al Supporto Microsoft, se richiesto.

Per limitare il volume del traffico acquisito da Fiddler, usare i filtri configurabili nella scheda **Filters**. La schermata che segue mostra un esempio di filtro che acquisisce solo il traffico inviato all'endpoint di archiviazione **contosoemaildist.table.core.windows.net**:

![][5]

### <a name="appendix-2"></a>Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete

Wireshark è uno strumento di analisi dei protocolli di rete che consente di visualizzare informazioni dettagliate sui pacchetti per un'ampia gamma di protocolli di rete. Wireshark può essere scaricato all'indirizzo <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

La procedura che segue indica come acquisire informazioni dettagliate sui pacchetti per il traffico dal computer locale su cui è installato Wireshark al servizio tabelle nell'account di archiviazione di Azure.

1.	Avviare Wireshark sul computer locale.
2.	Nella sezione **Start** selezionare l'interfaccia (o le interfacce) di rete locale connessa a Internet.
3.	Fare clic su **Capture Options**.
4.	Aggiungere un filtro nella casella di testo **Capture Filter**. Ad esempio, **host contosoemaildist.table.core.windows.net** configura Wireshark in modo che acquisisca solo i pacchetti inviati a o dall'endpoint del servizio tabelle nell'account di archiviazione **contosoemaildist**. Per un elenco completo dei filtri di acquisizione, vedere <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>. 

    ![][6]

5.	Fare clic su **Start**. Wireshark acquisirà tutti i pacchetti inviati a o dall'endpoint del servizio tabelle non appena l'applicazione client viene usata sul computer locale.
6.	Al termine, nel menu principale fare clic su **Capture** e quindi su **Stop**.
7.	Per salvare i dati acquisiti in un file di acquisizione di Wireshark, nel menu principale fare clic su **File** e quindi su **Save**.

Wireshark evidenzia tutti gli errori presenti nella finestra **packetlist**. Usare la finestra **Expert Info** (fare clic su **Analyze**, quindi su **Expert Info**) per visualizzare un riepilogo di errori e avvisi.

![][7]

È anche possibile scegliere di visualizzare i dati TCP appena il livello dell'applicazione li vede, facendo clic con il pulsante destro del mouse sui dati TCP e selezionando **Follow TCP Stream**. Ciò è particolarmente utile se si acquisisce il dump senza un filtro di acquisizione. Altre informazioni sono disponibili <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">in questa pagina</a>.

![][8]

> [AZURE.NOTE] Per altre informazioni sull'uso di Wireshark, vedere la <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">guida dell'utente di Wireshark</a>.

### <a name="appendix-3"></a>Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete

Microsoft Message Analyzer può essere usato per l'acquisizione del traffico HTTP e HTTPS in modo analogo a Fiddler e per l'acquisizione del traffico di rete in modo analogo a Wireshark.

#### Configurazione di una sessione di traccia Web tramite Microsoft Message Analyzer

Per configurare una sessione di traccia Web per il traffico HTTP e HTTPS usando Microsoft Message Analyzer, eseguire l'applicazione Microsoft Message Analyzer e quindi nel menu **File** fare clic su **Capture/Trace**. Nell'elenco degli scenari di traccia disponibili selezionare **Web Proxy**. Quindi, nel riquadro **Trace Scenario Configuration**, nella casella di testo **HostnameFilter**, aggiungere i nomi degli endpoint di archiviazione (cercare i nomi nel portale di Azure). Ad esempio, se il nome dell'account di archiviazione di Azure è **contosodata**, aggiungere quanto segue alla casella di testo **HostnameFilter**:

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
    
> [AZURE.NOTE] I nomi host sono separati da uno spazio. 

Quando si è pronti a iniziare a raccogliere i dati di traccia, fare clic sul pulsante **Start With**.

Per altre informazioni sulla traccia **Web Proxy** di Microsoft Message Analyzer, vedere <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF-WebProxy Provider</a> su TechNet.

La traccia **Web Proxy** integrata in Microsoft Message Analyzer è basata su Fiddler ed è in grado di acquisire il traffico HTTPS lato client e di visualizzare i messaggi HTTPS non crittografati. **Web Proxy** funziona mediante la configurazione di un proxy locale per tutto il traffico HTTP e HTTPS che fornisce l'accesso ai messaggi non crittografati.

#### Diagnosi dei problemi di rete con Microsoft Message Analyzer

Oltre a usare la traccia **Web Proxy** di Microsoft Message Analyzer per acquisire i dettagli del traffico HTTP/HTTP tra l'applicazione client e il servizio di archiviazione, è possibile usare la traccia **Local Link Layer** integrata per acquisire le informazioni sui pacchetti di rete. Ciò consente di acquisire dati simili a quelli acquisiti con Wireshark e di diagnosticare eventuali problemi della rete, ad esempio i pacchetti interrotti.

Nella schermata che segue è riportato un esempio della traccia **Local Link Layer** con alcuni messaggi **informativi** nella colonna **DiagnosisTypes**. Facendo clic su un'icona nella colonna **DiagnosisTypes** verranno visualizzati i dettagli del messaggio. In questo esempio, il server ha ritrasmesso il messaggio #305 perché non riceveva una conferma dal client:

![][9]

Quando si crea la sessione di traccia in Microsoft Message Analyzer, è possibile specificare dei filtri per ridurre la quantità di disturbi nella traccia. Nella pagina **Capture / Trace** in cui si definisce la traccia fare clic sul collegamento **Configure** accanto a **Microsoft-Windows-NDIS-PacketCapture**. La schermata che segue mostra una configurazione in cui il traffico TCP viene filtrato per gli indirizzi IP di tre servizi di archiviazione:

![][10]

Per altre informazioni sulla traccia Local Link Layer di Microsoft Message Analyzer, vedere l'argomento <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture Provider</a> su TechNet.

### <a name="appendix-4"></a>Appendice 4: Uso di Excel per la visualizzazione di metriche e dati di log

Molti strumenti consentono di scaricare i dati di Metriche di archiviazione dalle tabelle di archiviazione di Azure in un formato delimitato che semplifica il caricamento dei dati in Excel per la visualizzazione e l'analisi. I dati della registrazione dell'archiviazione provenienti dai BLOB di Azure sono già in un formato delimitato che può essere caricato in Excel. Tuttavia, sarà necessario aggiungere le intestazioni di colonna appropriate in base alle informazioni disponibili in <a href="http://msdn.microsoft.com/library/hh343259.aspx" target="_blank">Formato del log di Analisi archiviazione</a> e <a href="http://msdn.microsoft.com/library/hh343264.aspx" target="_blank">Schema di tabella della metrica di Analisi archiviazione</a>.

Per importare i dati della registrazione dell'archiviazione in Excel dopo il download dall'archiviazione BLOB:

- Nel menu **Dati** fare clic su **Da testo**.
- Passare al file di log da visualizzare e scegliere **Importa**.
- Nel passaggio 1 dell'**Importazione guidata testo** selezionare **Delimitato**.

Nel passaggio 1 dell'**Importazione guidata testo** selezionare **Punto e virgola** come unico delimitatore e scegliere le virgolette doppie come **Qualificatore di testo**. Fare clic su **Fine** e scegliere il punto in cui inserire i dati nella cartella di lavoro.

### <a name="appendix-5"></a>Appendice 5: Monitoraggio con Application Insights per Visual Studio Online

È possibile usare la funzionalità Application Insights per Visual Studio Online come parte del monitoraggio delle prestazioni e della disponibilità. Questo strumento consente di: 

- Assicurarsi che il servizio Web sia disponibile e reattivo. Sia che l'applicazione sia un sito Web o un'app per dispositivo che usa un servizio Web, è in grado di testare l'URL a intervalli di pochi minuti da postazioni situate in tutto il mondo e indicare se è presente un problema. 
- Diagnosticare rapidamente eventuali problemi di prestazioni o eccezioni del servizio Web. Sapere se la CPU o altre risorse vengono estese, ricavare analisi dello stack dalle eccezioni ed eseguire facilmente la ricerca delle tracce nei log. Se le prestazioni dell'app scendono al di sotto di limiti accettabili, verrà inviato un messaggio di posta elettronica. L'utente può monitorare i servizi Web sia .NET che Java.

Al momento della redazione di questo documento Application Insights è disponibile in anteprima. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/dn481095.aspx" target="_blank">Application Insights per Visual Studio Online</a>.


<!--Anchors-->
[Introduzione]: #introduction
[Organizzazione di questa guida]: #how-this-guide-is-organized

[Monitoraggio del servizio di archiviazione]: #monitoring-your-storage-service
[Monitoraggio dello stato del servizio]: #monitoring-service-health
[Monitoraggio della capacità]: #monitoring-capacity
[Monitoraggio della disponibilità]: #monitoring-availability
[Monitoraggio delle prestazioni]: #monitoring-performance

[Diagnosi dei problemi di archiviazione]: #diagnosing-storage-issues
[Problemi di stato del servizio]: #service-health-issues
[Problemi di prestazioni]: #performance-issues
[Diagnostica degli errori]: #diagnosing-errors
[Problemi dell'emulatore di archiviazione]: #storage-emulator-issues
[Strumenti di registrazione dell'archiviazione]: #storage-logging-tools
[Uso degli strumenti di registrazione in rete]: #using-network-logging-tools

[Traccia end-to-end]: #end-to-end-tracing
[Correlazione dei dati di log]: #correlating-log-data
[ID richiesta client]: #client-request-id
[ID richiesta server]: #server-request-id
[Timestamp]: #timestamps

[Guida alla risoluzione dei problemi]: #troubleshooting-guidance
[Le metriche indicano un valore AverageE2ELatency alto e un valore AverageServerLatency basso]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Le metriche indicano un valore AverageE2ELatency basso e un valore AverageServerLatency basso, ma il client riscontra una latenza elevata]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Le metriche indicano un valore AverageServerLatency alto]: #metrics-show-high-AverageServerLatency
[Si stanno verificando ritardi imprevisti nel recapito dei messaggi di una coda]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Le metriche indicano un aumento di PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento temporaneo di PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente di PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Le metriche indicano un aumento di PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Le metriche indicano un aumento di PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Il client sta ricevendo messaggi HTTP 403 (Accesso negato)]: #the-client-is-receiving-403-messages
[Il client sta ricevendo messaggi HTTP 404 (Non trovato)]: #the-client-is-receiving-404-messages
[Il client o un altro processo ha eliminato in precedenza l'oggetto]: #client-previously-deleted-the-object
[Si è verificato un problema di autenticazione della firma di accesso condiviso]: #SAS-authorization-issue
[Il codice JavaScript lato client non è autorizzato ad accedere all'oggetto]: #JavaScript-code-does-not-have-permission
[Errore della rete]: #network-failure
[Il client sta ricevendo messaggi HTTP 409 (Conflitto)]: #the-client-is-receiving-409-messages

[Le metriche indicano un valore PercentSuccess basso o le voci del log di analisi contengono operazioni con stato della transazione ClientOtherErrors]: #metrics-show-low-percent-success
[Le metriche della capacità indicano un aumento imprevisto dell'uso della capacità di archiviazione]: #capacity-metrics-show-an-unexpected-increase
[Si stanno verificando riavvii imprevisti delle macchine virtuali con un numero elevato di VHD allegati]: #you-are-experiencing-unexpected-reboots
[Il problema è dovuto all'uso dell'emulatore di archiviazione per attività di sviluppo o test]: #your-issue-arises-from-using-the-storage-emulator
[La funzionalità "X" non funziona nell'emulatore di archiviazione]: #feature-X-is-not-working
[Un messaggio indica che il valore di una delle intestazioni HTTP non è nel formato corretto quando si usa l'emulatore di archiviazione]: #error-HTTP-header-not-correct-format
[L'esecuzione dell'emulatore di archiviazione richiede privilegi amministrativi]: #storage-emulator-requires-administrative-privileges
[Si stanno verificando problemi di installazione di Microsoft Azure SDK per .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Si è verificato un problema diverso con un servizio di archiviazione]: #you-have-a-different-issue-with-a-storage-service

[Appendici]: #appendices
[Appendice 1: Uso di Fiddler per l'acquisizione del traffico HTTP e HTTPS]: #appendix-1
[Appendice 2: Uso di Wireshark per l'acquisizione del traffico di rete]: #appendix-2
[Appendice 3: Uso di Microsoft Message Analyzer per l'acquisizione del traffico di rete]: #appendix-3
[Appendice 4: Uso di Excel per la visualizzazione di metriche e dati di log]: #appendix-4
[Appendice 5: Monitoraggio con Application Insights per Visual Studio Online]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
<!--HONumber=42-->
