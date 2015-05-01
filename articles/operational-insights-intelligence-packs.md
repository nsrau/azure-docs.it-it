<properties 
	pageTitle="Intelligence Pack di Operational Insights" 
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
	ms.date="02/20/2015" 
	ms.author="alfran"/>

# Intelligence Pack

Gli Intelligence Pack sono una raccolta di **logica**, **visualizzazione** e **regole per l'acquisizione dei dati** che permettono di affrontare le sfide attuali principali dei clienti. Gli Intelligence Pack sono basati sulla ricerca di Operational Insights per offrire le metriche specifiche per una particolare area problematica. Permettono di ottenere analisi più approfondite e di risolvere più rapidamente i problemi operativi, raccogliere e correlare diversi tipi di dati del computer e migliorare la proattività grazie ad attività quali la pianificazione capacità, la creazione di report sullo stato delle patch e il controllo di sicurezza. 

Questa guida illustra in modo dettagliato i diversi Intelligence Pack disponibili e il relativo uso.

>[AZURE.NOTE] Per altre informazioni sull' *aggiunta* di Intelligence Pack, vedere la pagina relativa all'[aggiunta di Intelligence Pack](operational-insights-add-intelligence-pack.md)

## SQL Assessment

L'Intelligence Pack SQL Assessment valuta il rischio e l'integrità degli ambienti SQL Server a intervalli regolari. Per impostazione predefinita, analizzerà ogni settimana i sistemi SQL e le informazioni verranno presentate come rollup mensile. Fornisce un elenco di suggerimenti ordinato in base alla priorità e adattato alle distribuzioni disponibili. I suggerimenti sono classificati in base a tre aree principali, in modo da permettere all'utente e al team di eseguire le operazioni seguenti:

- Comprendere **rapidamente** il **rischio e l'integrità** degli ambienti
- Effettuare con facilità un'**azione** per ridurre il rischio e migliorare l'integrità
- **Classificare in ordine di priorità** il lavoro e aumentare la **produttività**

SQL Assessment richiede .NET 4 per l'esecuzione in ogni agente. Supporta le edizioni Standard, Developer ed Enterprise di SQL Server, tutte versioni attualmente supportate.
[Altre informazioni su MSDN](https://msdn.microsoft.com/library/azure/dn873958.aspx)


## Configuration Assessment

Configuration Assessment fornisce informazioni dettagliate sullo stato corrente dell'infrastruttura server. 

A differenza degli Intelligence Pack, Configuration Assessment è disponibile quando si inizia a usare Operational Insights con System Center Operations Manager. Non è disponibile se si usano solo gli agenti connessi direttamente.

I dati di configurazione vengono raccolti dai server monitorati e quindi inviati al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. I dati elaborati per i server vengono visualizzati per le aree seguenti:

- **Alerts:** mostra gli avvisi proattivi correlati alla configurazione generati per i server monitorati. Gli avvisi vengono prodotti dalle regole create dall'organizzazione di Assistenza clienti Microsoft(CSS) sulla base di procedure consigliate del settore.
- **Knowledge Recommendations:** mostra gli articoli della Microsoft Knowledge Base consigliati per i carichi di lavoro rilevati nell'infrastruttura. Gli articoli vengono suggeriti automaticamente in base alla configurazione tramite Machine Learning. 
- **Servers and Workloads Analyzed:** mostra i server e i carichi di lavoro che vengono monitorati da Operational Insights
- **Current Snapshot:** mostra le informazioni più recenti sui server che hanno segnalato dati al servizio Operational Insights.
- **Change History:** mostra un elenco di modifiche della configurazione apportate ai server monitorati.

> [AZURE.IMPORTANT] Configuration Assessment può essere abilitato solo per i computer gestiti da **Operations Manager**.  Per altre informazioni, vedere la pagina relativa alla [connessione di Operations Manager](operational-insights-connect-scom.md)


## Malware Assessment
Se viene trovata una protezione insufficiente, i server con minacce attive e protezione insufficiente vengono visualizzati nella pagina **Malware**. Usando le informazioni nella pagina **Malware**, è possibile sviluppare un piano per applicare la protezione ai server che lo richiedono.

> [AZURE.IMPORTANT] Malware Assessment supporta attualmente solo i client in tempo reale di Windows Defender e System Center Endpoint Protection (SCEP). Se questi client non vengono rilevati, verranno usati i dati forniti da Malicious Software Removal Tool e il server verrà contrassegnato come privo di protezione in tempo reale. Per altre informazioni, leggere[qui](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Microsoft sta anche verificando [un problema relativo ai computer Windows 7 e Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## Alert Management

L'Intelligence Pack Alert Management permette di visualizzare gli avvisi di **Operations Manager** in tutti i server. Questo strumento può semplificare la valutazione degli avvisi e può permettere di identificare le cause principali nell'ambiente in modo molto rapido e semplice.

È possibile ottenere approfondimenti relativi agli scenari chiave, inclusi i seguenti:

- Numero di avvisi generati in un intervallo di tempo specifico.
- Origini principali con il numero maggiore di avvisi attivi generati in un intervallo di tempo specifico.
- Avvisi critici e standard attivi principali generati in un intervallo di tempo specifico.
- Possibilità di eseguire ricerche in tutti gli avvisi e di visualizzare ogni avviso in modo dettagliato.

> [AZURE.IMPORTANT] Alert Management può essere abilitato solo quando Operational Insights viene usato insieme a **System Center Operations Manager**. Non verranno inviati dati dagli agenti. Gli avvisi di Operations Manager verranno semplicemente sincronizzati con il cloud per la valutazione in Operational Insights e per l'uso della ricerca. Per altre informazioni, vedere la pagina relativa alla [connessione di Operations Manager](operational-insights-connect-scom.md)


## Capacity Planning

È possibile usare l'Intelligence Pack Capacity Management in Microsoft Azure Operational Insights per stabilire e comprendere la capacità dell'infrastruttura server. L'Intelligence Pack legge i contatori delle prestazioni nel server monitorato e invia i dati di utilizzo al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati di utilizzo, che vengono quindi registrati dal servizio cloud. Nel tempo, vengono identificati modelli di utilizzo e proiezioni di capacità, in base al consumo corrente.

Ad esempio, una proiezione potrebbe identificare quando saranno necessari core aggiuntivi o altra memoria per un determinato server. In questo esempio, la proiezione potrebbe indicare che entro 30 giorni sarà necessario aggiungere memoria al server. Ciò consente di pianificare un aggiornamento di memoria durante la successiva finestra di manutenzione del server, che potrebbe verificarsi una volta ogni due settimane.

>[AZURE.IMPORTANT] È possibile abilitare Capacity Management solo quando Operational Insights viene usato insieme a **System Center Operations Manager** ed è anche necessario abilitare il connettore di Operations Manager con Virtual Machine Manager (VMM). Per altre informazioni sulla connessione tra i sistemi, vedere [Modalità di connessione di VMM con Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Per altre informazioni sull'uso dell'Intelligence Pack Capacity Management, vedere:

- [Come usare la pagina di calcolo](https://msdn.microsoft.com/library/azure/dn873965.aspx)
- [Come usare la pagina Direct Attached Storage](https://msdn.microsoft.com/library/azure/dn873954.aspx) 


## Change Tracking

È possibile usare l'Intelligence Pack Configuration Change Tracking per identificare facilmente le modifiche apportate al software e ai servizi Windows nel proprio ambiente. Questa funzionalità può semplificare l'individuazione dei problemi operativi. Usando le informazioni nella pagina di rilevamento modifiche, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server. È possibile vedere le modifiche all'infrastruttura e quindi analizzare i dettagli per le categorie seguenti:

- Modifiche per tipo di configurazione per i servizi software e di Windows.
- Modifiche software ad applicazioni e aggiornamenti per singoli server.
- Numero totale di modifiche software per ogni applicazione.
- Modifiche dei servizi di Windows per singoli server.


## System Update Assessment

È possibile usare l'Intelligence Pack System Updates in Microsoft Azure Operational Insights per applicare gli aggiornamenti mancanti ai server nell'infrastruttura. Se vengono rilevati aggiornamenti mancanti, vengono visualizzati nella pagina **Updates**. È possibile usare la pagina **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari.

Questa pagina include informazioni dettagliate sulle categorie seguenti:

- Server a cui mancano aggiornamenti delle sicurezza
- Server non aggiornati di recente
- Aggiornamenti da applicare a server specifici
- Tipo di aggiornamenti mancanti

È possibile fare clic su qualsiasi riquadro o elemento per visualizzarne i dettagli nella pagina **Search** e ottenere altre informazioni sull'aggiornamento mancante.  I risultati della ricerca comprendono:

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








<!--HONumber=52-->