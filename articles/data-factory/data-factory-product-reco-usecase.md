<properties 
	pageTitle="Caso d'uso su Data factory - Consigli sui prodotti" 
	description="Descrizione di un caso d'uso implementato usando Data factory di Azure e altri servizi." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Caso d'uso - Consigli sui prodotti 

Data factory di Azure è uno dei numerosi servizi disponibili per implementare i Solution Accelerator inclusi nella suite Cortana Analytics. Vedere la pagina dedicata a [Cortana Analytics](http://www.microsoft.com/cortanaanalytics) per informazioni dettagliate sulla suite. Questo documento descrive un caso d'uso comune risolto e implementato da alcuni utenti di Azure usando Data factory di Azure e altri servizi di Cortana Analytics.

## Scenario

I rivenditori online, in genere, cercano di indurre i clienti ad effettuare acquisti mostrando loro prodotti a cui probabilmente sono interessati e che quindi sono propensi ad acquistare. Questi rivenditori, pertanto, devono riuscire a personalizzare l'esperienza online degli utenti mediante consigli sui prodotti personalizzati, che tengano conto dei dati sui comportamenti di acquisto presenti e passati, delle informazioni sui prodotti, dei nuovi marchi introdotti e dei risultati della segmentazione dei dati relativi a prodotti e clienti. Solo così possono proporre ai clienti prodotti di loro effettivo interesse. I rivenditori possono fornire consigli sui prodotti anche in base all'analisi del comportamento di utilizzo complessivo da parte di tutti gli utenti.

L'obiettivo di questi rivenditori è ottimizzare il rapporto clic/vendite e aumentare così le entrate derivanti dalle vendite. Per raggiungere questo obiettivo, forniscono consigli sui prodotti contestuali e basati sul comportamento, messi a punto a partire dagli interessi e dalle azioni dei clienti. In questo caso, si considereranno i rivenditori online come esempio di azienda che desidera ottimizzare la propria offerta per i clienti, ma i principi espressi sono validi per qualsiasi azienda che desideri attrarre l'attenzione dei clienti sui propri beni e servizi e migliorare l'esperienza di acquisto dei clienti con consigli sui prodotti personalizzati.

## Problematiche

Sono molte le problematiche che i rivenditori online devono affrontare quando tentano di implementare un caso d'uso di questo tipo. In primo luogo, devono riuscire a integrare dati di diverse forme e dimensioni provenienti da più origini dati, sia in locale sia nel cloud, in modo da poter acquisire dati sui prodotti, dati sui comportamenti dei clienti e dati sugli utenti mentre questi navigano all'interno del sito del rivenditore.

In secondo luogo, devono elaborare e prevedere in modo ragionevole e accurato consigli personalizzati sui prodotti. Oltre ai dati relativi ai prodotti, ai marchi, ai comportamenti dei clienti e al browser, i rivenditori online devono tener conto anche di eventuali feedback inviati da altri clienti su acquisti precedenti per determinare il prodotto migliore da consigliare a un utente.

In terzo luogo, i consigli devono essere immediatamente resi disponibili agli utenti per garantire loro la migliore esperienze di navigazione e acquisto e poter fornire loro consigli sui prodotti più recenti e rilevanti. I rivenditori, infine, devono poter misurare l'efficacia del loro approccio monitorando il tasso di conversione dei clic in vendite (upselling e cross-selling) e apportare eventuali modifiche nella definizione dei consigli futuri.

## Panoramica della soluzione

Questo caso d'uso di esempio è stato risolto e implementato da utenti reali di Azure usando Data factory di Azure e altri servizi di Cortana Analytics, tra cui [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/), per acquisire, preparare, trasformare, analizzare e pubblicare i dati finali.

Il rivenditore online usa un archivio BLOB di Azure, un server SQL locale, un database SQL Azure e un data mart relazionale come soluzioni di archiviazione per le varie fasi del flusso di lavoro. L'archivio BLOB contiene informazioni sui clienti, dati sul comportamento dei clienti e informazioni sui prodotti, che comprendono informazioni sui marchi dei prodotti e un catalogo prodotti archiviato localmente in SQL Data Warehouse.

Come illustrato nella figura seguente, tutti i dati vengono combinati e inseriti in un sistema di raccomandazione dei prodotti allo scopo di fornire consigli personalizzati in base agli interessi e alle azioni dei clienti, mentre questi consultano i prodotti nel catalogo disponibile sul sito Web del rivenditore. Ai clienti vengono mostrati anche prodotti simili al prodotto che stanno visualizzando, definiti sulla base di modelli generali di utilizzo del sito non correlati a un utente specifico.

![diagramma caso d'uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Ogni giorno, gigabyte di file di log Web non elaborati vengono generati dal sito Web del rivenditore online come file semistrutturati. I file di log Web non elaborati e i dati sugli utenti e del catalogo prodotti vengono integrati a intervalli regolari in un account di archiviazione BLOB di Azure mediante servizi di spostamento dei dati distribuiti globalmente da Data factory. I file di log non elaborati relativi a un dato giorno vengono partizionati (per anno e mese) nell'archivio BLOB per l'archiviazione a lungo termine. Viene usato [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) (Hadoop as a Service) per partizionare nell'archivio BLOB i file di log non elaborati (in modo da migliorarne la gestibilità, la disponibilità e le prestazioni) ed elaborare in scala i log acquisiti mediante script Hive e Pig. I dati dei log Web così partizionati vengono quindi elaborati al fine di estrarre gli input necessari per un sistema di raccomandazione di apprendimento automatico e generare consigli sui prodotti personalizzati.

Il sistema di raccomandazione usato in questo esempio per l'apprendimento automatico è una piattaforma di raccomandazione open source ricavata da [Apache Mahout](http://mahout.apache.org/). È possibile usare anche [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) o un modello personalizzato. Il modello Mahout consente di stimare la somiglianza tra gli articoli presenti nel sito Web del rivenditore sulla base di modelli di utilizzo generali e di generare consigli personalizzati per ogni utente.

Il set di risultati ottenuto dai consigli personalizzati sui prodotti viene infine spostato in un data mart relazionale che verrà usato dal sito Web del rivenditore. È possibile accedere direttamente al set di risultati anche dall'archivio BLOB di un'altra applicazione oppure è possibile spostarlo in archivi aggiuntivi per metterlo a disposizione di altri consumer e casi d'uso.

## Vantaggi

Ottimizzando la strategia di raccomandazione dei prodotti e allineandola agli obiettivi aziendali, la soluzione ha soddisfatto gli obiettivi commerciali e di marketing del rivenditore online. Ha inoltre consentito di rendere operativo e gestire il flusso di lavoro di raccomandazione dei prodotti in maniera efficiente, affidabile ed economica, in modo da semplificare l'aggiornamento del modello e ottimizzarne l'efficacia in base alle statistiche di conversione di clic in vendite. Usando Data factory di Azure, inoltre, il rivenditore ha potuto abbandonare la gestione manuale delle risorse cloud, eccessivamente lunga e costosa, per passare a una gestione delle risorse cloud di tipo on-demand, che consente di risparmiare tempo e denaro e ridurre i tempi di distribuzione della soluzione. Grazie all'intuitiva interfaccia di gestione e monitoraggio di Data factory, accessibile dal portale di Azure classico, è possibile anche visualizzare e risolvere facilmente eventuali problemi relativi alla condizione dei servizi operativi e alle viste di derivazione dei dati. Questa soluzione, infine, può essere pianificata e gestita in modo che vengano prodotti e distribuiti agli utenti dati finiti e le dipendenze di elaborazione vengano gestite automaticamente senza l'intervento umano.

Offrendo un'esperienza di acquisto personalizzata, il rivenditore ha messo a punto un'esperienza utente più coinvolgente e competitiva e, quindi, ha aumentato le vendite e la soddisfazione complessiva dei clienti.



  

<!---HONumber=AcomDC_0128_2016-->