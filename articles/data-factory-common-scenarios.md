<properties 
	pageTitle="Scenari comuni di utilizzo di Data factory di Azure" 
	description="Informazioni su alcuni scenari comuni di utilizzo del servizio Data factory di Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Scenari comuni di utilizzo di Data factory di Azure
Questa sezione descrive alcuni scenari di esempio attualmente supportati da Data factory di Azure, ma in futuro verranno aggiunti altri scenari hub.

> [AZURE.NOTE]Prima di questo articolo, leggere [Introduzione a Data Factory di Azure][datafactory-introduction].

##Scenario 1: Origini dati per l'hub di dati
![Origine dell'hub di dati][image-data-factory-introduction-secenario1-source-datahub]

Le grandi imprese hanno svariati tipi di dati in diverse origini. Il primo passaggio per la creazione di un sistema di produzione delle informazioni consiste nella connessione di tutte le origini di dati ed elaborazione necessarie, ad esempio i servizi SaaS, le condivisioni file, l'FTP e i servizi Web, e nel conseguente spostamento dei dati in base alle necessità per l'elaborazione successiva.

Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni. È un procedimento costoso, i sistemi sono difficili da integrare e gestire e spesso non forniscono il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito è in grado di offrire.
  
Con Data factory di Azure, i servizi di archiviazione e di elaborazione dati vengono raccolti in un contenitore hub che semplifica e ottimizza le attività di calcolo e di archiviazione, consente la gestione unificata dell'utilizzo delle risorse e fornisce i servizi per lo spostamento dei dati, secondo necessità.

##Scenario 2: Rendere operativa la produzione di informazioni
Gli scenari di messa in funzione rappresentano il passaggio logico naturale dopo gli scenari di acquisizione dei dati. Quando i dati sono presenti in un hub, creare e rendere operative le pipeline di dati per generare in modo affidabile i dati trasformati in una pianificazione gestibile e controllata in modo da alimentare gli ambienti di produzione con dati attendibili. La trasformazione dei dati in Data factory di Azure avviene mediante elaborazioni Hive, Pig e C# personalizzate in esecuzione su Hadoop (HDInsight di Azure). Queste trasformazioni consentono di eliminare dati, nascondere campi di dati critici ed eseguire altre operazioni sui dati in molti modi diversi. In genere, la messa in funzione viene conseguita tramite servizi personalizzati e un'infrastruttura complessa e di difficile manutenzione e comporta diversi problemi di implementazione, gestione, scalabilità, risoluzione dei problemi e controllo delle versioni.
  
Usando Data factory come servizio completamente gestito, gli utenti possono rendere operative queste pipeline definendole con pianificazioni una tantum o ricorrenti complesse, mentre l'orchestrazione viene gestita direttamente dal servizio Data factory. Con gli hub, le operazioni di gestione dei cluster per tutti i dati e di elaborazione all'interno di un hub vengono gestite per conto dell'utente per consentire agli utenti di concentrarsi sull'analisi trasformativa anziché sulla gestione delle infrastrutture. Data factory di Azure elimina il problema dell'utilizzo di servizi personalizzati transitori e consente alle aziende di produrre informazioni attendibili in modo affidabile e riproducibile.


##Scenario 3: Integrare la produzione di informazioni con l'individuazione di dati
Gli approcci e le tecnologie BI tradizionali offrono una "fonte autorevole di veridicità", ma presentano quasi sempre un grave effetto collaterale: un backlog costante di richieste dovuto a un processo di richiesta di modifiche controllato con attenzione. Per adattarsi alle sfide aziendali in rapida evoluzione è necessaria una maggiore flessibilità nelle grandi imprese che consenta di connettere i sistemi di produzione delle informazioni ai sistemi di utilizzo delle informazioni. Data factory di Azure contribuisce ad affrontare due sfide: connettere questi sistemi con pipeline di dati ottimizzate per la produzione delle informazioni e semplificare l'utilizzo delle informazioni rendendo i dati attendibili aggiornati disponibili in formati di facile utilizzo.
  
Data factory di Azure supporta le seguenti capacità per permettere un utilizzo semplificato dei dati generati:

- Spostamento facile degli asset di dati generati (una-tantum o pianificati) in data mart relazionali per l'utilizzo con strumenti BI esistenti (Excel, Tableau e così via).
- Utilizzo degli asset di dati generati in base a un'istanza di Data factory usando direttamente Power Query in Excel.

Vedere i seguenti argomenti per l'utilizzo dei dati con Power Query:

- [Power Query: Connettersi ad Archiviazione tabella Microsoft Azure][Power-Query-Azure-Table]
- [Power Query: Connettersi ad Archiviazione BLOB Microsoft Azure][Power-Query-Azure-Blob]
- [Power Query: Connettersi al database SQL di Microsoft Azure][Power-Query-Azure-SQL]
- [Power Query: Connettersi a un server Microsoft SQL locale][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png

<!---HONumber=GIT-SubDir-->