<properties
	pageTitle="Soluzioni di Operational Insights"
	description="Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati."
	services="operational-insights"
	documentationCenter="n/a"
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="NA"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="alfran"/>

# Soluzioni disponibili in Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Le soluzioni sono un insieme di **logica**, **visualizzazione** e **regole per l'acquisizione dei dati** in grado di risolvere i principali problemi che i clienti si trovano oggi ad affrontare. Le soluzioni sono basate sulla ricerca di Operational Insights e offrono le metriche specifiche per una particolare area problematica. Permettono di ottenere analisi più approfondite e di risolvere più rapidamente i problemi operativi, raccogliere e correlare diversi tipi di dati del computer e migliorare la proattività grazie ad attività quali la pianificazione capacità, la creazione di report sullo stato delle patch e il controllo di sicurezza.

Questa guida illustra in modo dettagliato le diverse soluzioni disponibili e il relativo uso.

>[AZURE.NOTE]Per altre informazioni sull'*aggiunta* di soluzioni, vedere l'articolo relativo all'[aggiunta di soluzioni](operational-insights-add-solution.md).

## SQL Assessment

La soluzione SQL Assessment valuta il rischio e l'integrità degli ambienti SQL Server a intervalli regolari. Per impostazione predefinita, analizzerà ogni settimana i sistemi SQL e le informazioni verranno presentate come rollup mensile. Fornisce un elenco di suggerimenti ordinato in base alla priorità e adattato alle distribuzioni disponibili. I suggerimenti sono classificati in base a tre aree principali, in modo da permettere all'utente e al team di eseguire le operazioni seguenti:

- Comprendere **rapidamente** **il rischio e l'integrità** degli ambienti
- Effettuare con facilità un'**azione** per ridurre il rischio e migliorare l'integrità
- Organizzare il lavoro in base alle **priorità** e aumentare la **produttività**

SQL Assessment richiede .NET 4 per l'esecuzione in ogni agente. Supporta le edizioni Standard, Developer ed Enterprise di SQL Server, tutte versioni attualmente supportate. Vedere l'articolo relativo all'[ottimizzazione dell'ambiente mediante soluzioni per la valutazione](operational-insights-assessment.md).


## Configuration Assessment

Configuration Assessment fornisce informazioni dettagliate sullo stato corrente dell'infrastruttura server.

A differenza delle soluzioni, Configuration Assessment è disponibile quando si inizia a usare Operational Insights con System Center Operations Manager. Non è disponibile se si usano solo gli agenti connessi direttamente.

I dati di configurazione vengono raccolti dai server monitorati e quindi inviati al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. I dati elaborati per i server vengono visualizzati per le aree seguenti:

- **Avvisi:** mostra gli avvisi proattivi relativi alla configurazione generati per i server monitorati. Gli avvisi vengono generati da regole definite dal Servizio Supporto Tecnico Clienti Microsoft sulla base delle procedure consigliate derivanti dall'esperienza sul campo.
- **Knowledge Recommendations:** mostra gli articoli della Microsoft Knowledge Base consigliati per i carichi di lavoro rilevati nell'infrastruttura. Gli articoli vengono suggeriti automaticamente in base alla configurazione tramite Machine Learning.
- **Servers and Workloads Analyzed:** mostra i server e i carichi di lavoro che vengono monitorati da Operational Insights.
- **Current Snapshot:** mostra le informazioni più recenti relative ai server che hanno segnalato dati al servizio Operational Insights.
- **Change History:** mostra un elenco di modifiche della configurazione apportate ai server monitorati.

> [AZURE.IMPORTANT]Configuration Assessment può essere abilitato solo per i computer gestiti da **Operations Manager**. Per altre informazioni, vedere la pagina [Connettersi a Operational Insights da System Center Operations Manager](operational-insights-connect-scom.md).


## Malware Assessment
Se viene rilevata una protezione insufficiente, i server con minacce attive e protezione insufficiente vengono visualizzati nella pagina **Malware**. Usando le informazioni disponibili nella pagina **Malware**, è possibile sviluppare un piano per applicare la protezione ai server che ne hanno necessità.

> [AZURE.IMPORTANT]Malware Assessment supporta attualmente solo i client in tempo reale di Windows Defender e System Center Endpoint Protection (SCEP). Se questi client non vengono rilevati, verranno usati i dati forniti da Malicious Software Removal Tool e il server verrà contrassegnato come privo di protezione in tempo reale. Per altre informazioni, leggere [qui](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Microsoft sta anche verificando [un problema relativo ai computer con Windows 7 e Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n).





## Alert Management

La soluzione Alert Management permette di visualizzare gli avvisi di **Operations Manager** in tutti i server. Questo strumento può semplificare la valutazione degli avvisi e può permettere di identificare le cause principali nell'ambiente in modo molto rapido e semplice.

È possibile ottenere approfondimenti relativi agli scenari chiave, inclusi i seguenti:

- Numero di avvisi generati in un intervallo di tempo specifico.
- Origini principali con il numero maggiore di avvisi attivi generati in un intervallo di tempo specifico.
- Avvisi critici e standard attivi principali generati in un intervallo di tempo specifico.
- Possibilità di eseguire ricerche in tutti gli avvisi e di visualizzare ogni avviso in modo dettagliato.

> [AZURE.IMPORTANT]È possibile abilitare Alert Management solo quando Operational Insights viene usato insieme a **System Center Operations Manager**. Non verranno inviati dati dagli agenti. Gli avvisi di Operations Manager verranno semplicemente sincronizzati con il cloud per la valutazione in Operational Insights e per l'uso della ricerca. Per altre informazioni, vedere la pagina [Connettersi a Operational Insights da System Center Operations Manager](operational-insights-connect-scom.md).


## Capacity Planning

È possibile usare la soluzione Capacity Management in Microsoft Azure Operational Insights per determinare la capacità dell'infrastruttura server. Questa soluzione legge i contatori delle prestazioni nel server monitorato e invia i dati di utilizzo al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati di utilizzo, che vengono quindi registrati dal servizio cloud. Nel tempo, vengono identificati modelli di utilizzo e proiezioni di capacità, in base al consumo corrente.

Ad esempio, una proiezione potrebbe identificare quando saranno necessari core aggiuntivi o altra memoria per un determinato server. In questo esempio, la proiezione potrebbe indicare che entro 30 giorni sarà necessario aggiungere memoria al server. Ciò consente di pianificare un aggiornamento di memoria durante la successiva finestra di manutenzione del server, che potrebbe verificarsi una volta ogni due settimane.

>[AZURE.IMPORTANT]È possibile abilitare Capacity Management solo quando Operational Insights viene usato insieme a **System Center Operations Manager**. È inoltre necessario abilitare il connettore di Operations Manager con Virtual Machine Manager (VMM). Per informazioni aggiuntive sulla connessione tra i sistemi, vedere [Modalità di connessione di VMM con Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Per altre informazioni sull'uso della soluzione Capacity Management, vedere:

- [Come usare la pagina di calcolo](operational-insights-capacity/#compute-page)
- [Come usare la pagina Direct Attached Storage](operational-insights-capacity/#direct-attached-storage-page)


## Change Tracking

È possibile usare la soluzione Configuration Change Tracking per identificare facilmente le modifiche apportate al software e ai servizi Windows nell'ambiente in uso. Questa funzionalità può semplificare l'individuazione dei problemi operativi. Usando le informazioni nella pagina di rilevamento modifiche, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server. È possibile vedere le modifiche all'infrastruttura e quindi analizzare i dettagli per le categorie seguenti:

- Modifiche per tipo di configurazione per i servizi software e di Windows.
- Modifiche software ad applicazioni e aggiornamenti per singoli server.
- Numero totale di modifiche software per ogni applicazione.
- Modifiche dei servizi di Windows per singoli server.


## System Update Assessment

È possibile usare la soluzione System Updates in Microsoft Azure Operational Insights per applicare gli aggiornamenti mancanti ai server dell'infrastruttura. Gli aggiornamenti mancanti rilevati vengono visualizzati nella pagina **Updates**. È possibile usare la pagina **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari.

Questa pagina include informazioni dettagliate sulle categorie seguenti:

- Server a cui mancano aggiornamenti delle sicurezza
- Server non aggiornati di recente
- Aggiornamenti da applicare a server specifici
- Tipo di aggiornamenti mancanti

È possibile fare clic su qualsiasi riquadro o elemento per visualizzarne i dettagli nella pagina **Search** e ottenere altre informazioni sull'aggiornamento mancante. I risultati della ricerca comprendono:

- Server
- Titolo dell'aggiornamento
- ID della Knowledge Base
- Prodotto a cui è destinato l'aggiornamento
- Gravità dell'aggiornamento
- Data di pubblicazione

I risultati della ricerca per il server comprendono:

- Nome server
- Nome della versione del sistema operativo
- Metodo di abilitazione degli aggiornamenti automatici
- Giorni trascorsi dall'ultimo aggiornamento
- Versione dell'agente di Windows Update

<!---HONumber=July15_HO2-->