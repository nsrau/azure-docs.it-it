<properties title="Introduction to Azure Data Factory" pageTitle="Introduzione a Data factory di Azure" description="Learn how you can use the Azure Data Factory service to compose data processing, data storage and data movement services to create pipelines that produce trusted information." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Introduzione al servizio Data factory di Azure
Il servizio **Data factory di Azure** è un servizio completamente gestito per l'integrazione dei servizi di archiviazione dei dati, elaborazione e spostamento in pipeline di produzione dei dati ottimizzate, scalabili ed affidabili.  Gli sviluppatori possono usare Data factory per trasformare i dati semistrutturati, non strutturati e strutturati da origini locali e cloud in informazioni attendibili. Gli sviluppatori creano flussi di lavoro basati sui dati (pipeline) che uniscono, aggregano e trasformano i dati acquisiti dai servizi locali, basati su cloud e Internet e configurano elaborazioni di dati complesse mediante un semplice script JSON. Il servizio Data factory di Azure fornisce il monitoraggio e la gestione immediati delle pipeline grazie a una ricca esperienza visiva offerta dal portale di anteprima di Azure. Le informazioni generate dalle pipeline possono essere usate facilmente con gli strumenti di analisi e BI e con altre applicazioni per ottenere importanti informazioni aziendali accurate e prendere decisioni in modo affidabile.

Questo articolo fornisce una panoramica del servizio Data factory di Azure, del valore offerto dal servizio e degli scenari supportati. 

##Panoramica di Data factory di Azure
In passato i progetti di integrazione dei dati si basavano sulla creazione di processi ETL (Extract-Transform-Load) che consentivano di estrarre i dati da diverse origini dati in un'organizzazione, di trasformarli in modo che fossero conformi allo schema di destinazione di un data warehouse aziendale (EDW, Enterprise Data Warehouse) e di caricare i dati in un EDW come mostrato nell'immagine seguente. L'EDW a cui veniva eseguito l'accesso rappresentava quindi l'unica fonte di veridicità per le soluzioni di analisi BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

Lo scenario attuale di gestione dei dati aziendali continua a crescere esponenzialmente in volume, varietà e complessità, come mostrato nell'immagine seguente.  Si tratta di uno scenario diversificato, con dati locali e generati su cloud di diversi formati e velocità.  L'elaborazione dati deve essere eseguita in più aree geografiche e include una combinazione di software open source, soluzioni commerciali e servizi di elaborazione personalizzati, che possono risultare costosi e difficili da integrare e gestire.  La flessibilità necessaria per adattarsi al mutevole scenario dei Big Data rappresenta un'opportunità per unire il tradizionale EDW con le capacità richieste in un moderno sistema di produzione delle informazioni.  Il servizio Data factory di Azure è la piattaforma di composizione che consente di usare i tradizionali EDW con lo scenario dei dati in continuo cambiamento per permettere alle grandi imprese di usare tutte le informazioni disponibili nei processi decisionali basati sui dati.

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


Il servizio **Data factory di Azure** permette alle grandi imprese di sfruttare questa diversità grazie a una piattaforma in grado di **comporre i servizi di elaborazione dati, archiviazione e spostamento nelle pipeline di produzione delle informazioni e di gestire gli asset di dati attendibili**.

Il servizio Data factory di Azure consente di:

- **Usare facilmente diversi sistemi di archiviazione e di elaborazione dati.** 
Data factory consente di usare i dati dalla posizione in cui sono archiviati e comporre i servizi per l'archiviazione, l'elaborazione e lo spostamento dei dati.  Ad esempio, è possibile comporre pipeline di produzione delle informazioni per elaborare dati locali come quelli di SQL Server insieme a dati cloud come quelli di database, BLOB e tabelle SQL di Azure.  
- **Trasformare i dati in informazioni attendibili.** 
Per combinare e strutturare correttamente i dati complessi possono essere richiesti diversi tentativi e la modifica dei modelli di dati può rivelarsi lunga e costosa.  Con Data factory è possibile concentrarsi sull'analisi trasformativa mentre il servizio si occupa del plumbing.  Data factory supporta l'elaborazione Hive, Pig e C#, oltre alle funzionalità di elaborazione principali, ad esempio la gestione automatica dei cluster Hadoop (HDInsight), i nuovi tentativi in caso di errori temporanei, i criteri di timeout configurabili e gli avvisi.  
- **Monitorare le pipeline di dati in un'unica posizione.** 
Con un portfolio di dati diverso, è importante avere una vista affidabile e completa dei servizi di archiviazione, elaborazione e spostamento dei dati.  Data factory permette di valutare rapidamente lo stato della pipeline end-to-end, individuare i problemi e intraprendere eventuali azioni correttive. Monitorare visivamente la derivazione dei dati e le relazioni tra di essi in qualsiasi origine. Avere una visione cronologica completa dell'esecuzione dei processi, dello stato del sistema e delle dipendenze da un singolo dashboard di monitoraggio
- **Ottenere informazioni accurate complete dai dati trasformati**
Adattarsi alle sfide in continua evoluzione che l'organizzazione deve affrontare e avere sempre il controllo sulla produzione dei dati.  Migliorare la capacità di acquisizione di informazioni aziendali più accurate grazie alla generazione di informazioni da usare in modo attendibile e tempestivo. Usare le pipeline di dati per trasferire i dati trasformati dal cloud ad origini locali come SQL Server oppure mantenerli nelle origini di archiviazione cloud per usarli con strumenti di analisi e BI e con altre applicazioni.

Oggi, per sfruttare i vantaggi di Data factory, gli sviluppatori interagiscono direttamente con le singole pipeline di dati, con i servizi di archiviazione e con i servizi di calcolo.  Man mano che il servizio Data factory si evolve nel tempo, vengono introdotti nuovi servizi di archiviazione ed elaborazione e nuovi meccanismi di raggruppamento in "hub" dei servizi di calcolo, di archiviazione e delle pipeline di dati.  Gli hub vengono descritti nell'introduzione poiché questo nuovo concetto viene menzionato in tutto il servizio come precursore di futuri rilasci.

Un hub di Data factory di Azure è un contenitore per servizi di archiviazione e di calcolo (indicati collettivamente servizi collegati) e per le pipeline di dati che usano ed eseguono tali risorse. Il contenitore hub consente di dividere Data factory in gruppi logici o di dominio specifici.  Ad esempio, una grande impresa può avere "West US Azure Hub" che gestisce tutti i servizi collegati e le pipeline concentrati nel data center degli Stati Uniti occidentali oppure "Sales EDW Hub" che gestisce tutti i servizi collegati e le pipeline associati al popolamento e all'elaborazione dei dati per l'EDW di vendita.  Una caratteristica importante degli hub riguarda il fatto che una pipeline viene eseguita in un singolo hub. Ciò significa che, quando si definisce una pipeline, tutti i servizi collegati a cui fanno riferimento le tabelle o le attività nella pipeline devono avere lo stesso nome hub della pipeline.

Gli hub consentono di incapsulare l'archiviazione e il calcolo in modo che le pipeline possano fare riferimento solo a un hub e non agli specifici servizi e tabelle usati. L'hub può usare dei criteri per determinare dove eseguire una pipeline. Questa funzionalità ha effetti significativi, ad esempio, fornisce una funzionalità semplificata che consente di aumentare il numero di istanze aggiungendo più servizi collegati a un hub e di eseguire il bilanciamento del carico delle pipeline in questi nuovi servizi collegati. Inoltre, riutilizza le definizioni delle pipeline in hub diversi.

## Scenari
Esistono diversi scenari per le pipeline di dati che si estendono in sistemi locali, sistemi cloud, sistemi SaaS e nella produzione di dati in streaming e basati su batch.  Questa sezione descrive alcuni scenari di esempio che Data factory di Azure supporta attualmente, ma in futuro verranno aggiunti altri scenari hub.

###Scenario 1: origini dati per l'hub di dati
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

Le grandi imprese hanno svariati tipi di dati in diverse posizioni.  Il primo passaggio per la creazione di un sistema di produzione delle informazioni consiste nella connessione di tutte le origini di dati ed elaborazioni necessarie, ad esempio i servizi SaaS, le condivisioni file, l'FTP e i servizi Web, e nel conseguente spostamento dei dati in base alle necessità per l'elaborazione successiva.

Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni.  È un procedimento costoso, i sistemi sono difficili da integrare e gestire e spesso non forniscono il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito è in grado di offrire.
  
Con Data factory di Azure, i servizi di archiviazione e di elaborazione dati vengono raccolti in un contenitore hub che semplifica e ottimizza le attività di calcolo e di archiviazione, consente la gestione unificata dell'utilizzo delle risorse e fornisce i servizi per lo spostamento dei dati, secondo necessità.

###Scenario 2: rendere operativa la produzione di informazioni

Gli scenari di messa in funzione rappresentano il passaggio logico naturale dopo gli scenari di acquisizione dei dati. Quando i dati sono presenti in un hub, creare e rendere operative le pipeline di dati per generare in modo affidabile i dati trasformati in una pianificazione gestibile e controllata in modo da alimentare gli ambienti di produzione con dati attendibili.  La trasformazione dei dati in Data factory di Azure avviene mediante elaborazioni Hive, Pig e C# personalizzate in esecuzione su Hadoop (HDInsight di Azure).  Queste trasformazioni possono essere usate per pulire i dati, mascherare i campi dati critici ed eseguire altre operazioni sui dati mediante diversi metodi complessi.  In genere, la messa in funzione viene eseguita con un'infrastruttura e con servizi personalizzati complessi e di difficile gestione e pone diverse sfide in termini di implementazione, gestione, scalabilità, risoluzione dei problemi e controllo delle versioni.
  
Usando Data factory come servizio completamente gestito, gli utenti possono rendere operative queste pipeline definendole con pianificazioni una-tantum o ricorrenti complesse, mentre l'orchestrazione viene gestita direttamente dal servizio Data factory.  Con gli hub, la gestione del cluster per tutti i dati e le elaborazioni in un hub viene gestita per conto dell'utente, quindi gli utenti possono concentrarsi sull'analisi trasformativa piuttosto che sulla gestione dell'infrastruttura.  Data factory di Azure elimina i problemi legati all'uso di servizi personalizzati transitori e consente alle grandi imprese di generare informazioni attendibili in modo affidabile e riproducibile.


###Scenario 3:  integrare la produzione di informazioni con l'individuazione di dati
Gli approcci e le tecnologie BI tradizionali offrono una "fonte autorevole di veridicità", ma presentano quasi sempre un grave effetto collaterale: un backlog costante di richieste dovuto a un processo di richieste di modifica attentamente controllato.  Per adattarsi alle sfide aziendali in rapida evoluzione è necessaria una maggiore flessibilità nelle grandi imprese che consenta di connettere i sistemi di produzione delle informazioni ai sistemi di utilizzo delle informazioni.  Data factory di Azure contribuisce ad affrontare due sfide: connettere questi sistemi con pipeline di dati ottimizzate per la produzione delle informazioni e semplificare l'utilizzo delle informazioni rendendo i dati attendibili aggiornati disponibili in formati di facile utilizzo.
  
Data factory di Azure supporta le seguenti capacità per permettere un utilizzo semplificato dei dati generati:

- Spostamento facile degli asset di dati generati (una-tantum o pianificati) in data mart relazionali per l'utilizzo con strumenti BI esistenti (Excel, Tableau e così via).
- Utilizzo degli asset di dati generati in base a un'istanza di Data factory usando direttamente Power Query in Excel.

Vedere i seguenti argomenti per l'utilizzo dei dati con Power Query: 

- [Power Query: Connettersi ad Archiviazione tabella Microsoft Azure] [Power-Query-Azure-Table]
- [Power Query: Connettersi all'archiviazione BLOB di Microsoft Azure] [Power-Query-Azure-Blob]
- [Power Query: Connettersi a un database SQL di Microsoft Azure] [Power-Query-Azure-SQL]
- [Power Query: Connettersi a un database di SQL Server][Power-Query-OnPrem-SQL] 

## Modello applicativo
Il seguente diagramma illustra il modello di applicazione supportato da Data factory di Azure.

![Application Model][image-data-factory-application-model]

In Data factory di Azure sono presenti tre fasi di produzione delle informazioni:


- **Connect & Collect**. In questa fase, i dati di più origini dati vengono importati negli hub di dati.
- **Transform & Enrich**. In questa fase, i dati raccolti vengono elaborati.
- **Publish**. In questa fase, i dati sono pubblicati in modo da poter essere usati dagli strumenti BI, dagli strumenti di analisi e da altre applicazioni.

Data factory consente agli sviluppatori di creare le **pipeline**, ossia gruppi di **attività** di spostamento e/o elaborazione dati che accettano uno o più **set di dati** di input e generano uno o più set di dati di output.  Le pipeline possono essere eseguite una sola volta o in base a un intervallo flessibile di pianificazioni (oraria, giornaliera, settimanale e così via).  Un **set di dati** corrisponde a una visualizzazione dati denominata. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle **tabelle** o ai **modelli**.

Le **pipeline** che comprendono **attività** di spostamento dati (ad esempio, l'attività di copia) vengono spesso usate per importare/esportare i dati da tutte le **origini dati** (database, file, servizi SaaS e così via) usate dall'organizzazione in un **hub di dati**.
    
Quando i dati sono in un **hub**, le **pipeline** ospitate dai servizi di calcolo dell'hub vengono usate per trasformare i dati in un formato adatto all'uso (da parte degli strumenti BI, delle applicazioni, dei clienti e così via).  
  
Infine, le **pipeline** possono essere concatenate (come mostrato nel diagramma) in modo che i **set di dati** di output di una pipeline corrispondano agli input di un'altra.  Ciò consente di includere i flussi di dati complessi nelle **pipeline** eseguite in un hub di dati o su più hub.  Usando le **pipeline** in questo modo, le organizzazioni dispongono di blocchi predefiniti per comporre i migliori servizi locali, cloud e SaaS mediante una singola istanza di Data factory facile da gestire. 


##Terminologia
Questa sezione descrive la terminologia relativa a Data factory di Azure.

### Data factory

**Data factory di Azure** è un servizio completamente gestito per la composizione di servizi per l'archiviazione, l'elaborazione e lo spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili ed affidabili. 

Il servizio Data factory usa, genera, gestisce e pubblica i **set di dati**. Una sottoscrizione di Azure può contenere una o più istanze di Data factory di Azure. Data factory di Azure può essere collegato a uno o più servizi di archiviazione e di calcolo (chiamati servizi collegati).
 
Data factory di Azure può avere una o più pipeline che elaborano i dati in archiviazioni collegate usando servizi di calcolo collegati come HDInsight di Azure. Data factory di Azure non contiene dati al suo interno. I dati vengono archiviati al di fuori dell'istanza di Data factory, in un sistema di archiviazione esistente dell'utente.


### Servizio collegato
Un servizio collegato è un servizio collegato a un'istanza di Data factory di Azure. Un servizio collegato può essere:


- Un archivio dati, ad esempio archiviazione di Azure, database SQL di Azure e database SQL Server locale
- Un servizio di calcolo come HDInsight di Azure.

Un archivio dati è un contenitore di dati o di set di dati e HDInsight di Azure è l'unico servizio di calcolo attualmente supportato. Viene creato inizialmente un servizio collegato che punta a un archivio dati, quindi vengono definiti i set di dati che rappresentano i dati provenienti dall'archivio dati specificato. 

### Set di dati
Una visualizzazione dati denominata. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle tabelle relazionali o persino ai modelli. Una **tabella** è un set di dati che contiene uno schema ed è di forma rettangolare.

###Pipeline
Una **pipeline** in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Ad esempio, in un'attività di copia i dati vengono copiati da un'archiviazione di origine a un'archiviazione di destinazione, mentre le attività Hive/Pig usano un cluster HDInsight di Azure per elaborare i dati usando le query Hive o gli script Pig.

I passaggi tipici per la creazione di un'istanza di Data factory di Azure sono:

1. Creare un'istanza di Data factory
2. Creare un servizio collegato per ciascun archivio dati o servizio di calcolo
3. Creare set di dati di input e di output
4. Creare una pipeline 

###Attività
Un passaggio di elaborazione dati in una pipeline che acquisisce uno o più set di dati di input e genera uno o più set di dati di output.  Le attività vengono eseguite in un ambiente di esecuzione (ad esempio, il cluster HDInsight di Azure) e leggono/scrivono i dati in un archivio dati associato all'istanza di Data factory di Azure.
 
###Hub di dati
Un hub di dati di Azure è un contenitore per i servizi di archiviazione e di calcolo dei dati. Ad esempio, un cluster Hadoop con HDFS come archiviazione e Hive/Pig come calcolo (elaborazione) è un hub di dati. Analogamente, un data warehouse aziendale (EDW) può essere modellato come hub di dati (database come archiviazione, stored procedure e/o strumento ETL come servizi di calcolo).  Le pipeline usano gli archivi dati e sono eseguite su risorse di calcolo in un hub di dati. Al momento è supportato solo l'hub HDInsight.

L'hub di dati consente di dividere un'istanza di Data factory in gruppi logici o di dominio specifici, ad esempio "West US Azure Hub" che gestisce tutti i servizi collegati (archivi dati e calcolo) e le pipeline concentrati nel data center degli Stati Uniti occidentali oppure "Sales EDW Hub" che gestisce tutti i servizi collegati e le pipeline associati al popolamento e all'elaborazione dei dati per l'EDW di vendita.

Una caratteristica importante dell'hub riguarda il fatto che una pipeline viene eseguita in un singolo hub. Ciò significa che, quando si definisce una pipeline, tutti i servizi collegati a cui fanno riferimento le tabelle o le attività nella pipeline devono avere lo stesso nome hub della pipeline. Se la proprietà HubName non è specificata per un servizio collegato, questo viene inserito nell'hub "predefinito".

###Sezione
Una tabella in Data factory di Azure è composta da sezioni sull'asse temporale. La larghezza di una sezione viene determinata dalla pianificazione, oraria o giornaliera. Quando la pianificazione è oraria, viene generata una sezione all'ora con l'ora di inizio e di fine di una pipeline e così via.  

Le sezioni consentono ai professionisti IT di lavorare con un subset di dati generali per una specifica finestra temporale, ad esempio, la sezione generata per la durata (ora): dalle 1:00 PM alle 2:00 PM). Possono anche visualizzare tutte le sezioni di dati downstream per uno specifico intervallo di tempo ed eseguire di nuovo una sezione in caso di errore.

L'esecuzione è un'unità di elaborazione per una sezione. Possono esserci più esecuzioni di una sezione se vengono eseguiti più tentativi o se si ripete l'esecuzione della sezione in caso di errori. Una sezione viene identificata in base all'ora di inizio. 

###Gateway di gestione dati
Il Gateway di gestione dati di Microsoft è un software che connette le origini dati locali ai servizi cloud per l'utilizzo. È necessario che nell'ambiente aziendale sia installato almeno un gateway registrato con il portale di Data factory di Azure prima di aggiungere origini dati locali come servizi collegati.


##Passaggi successivi
[Introduzione a Data factory][datafactory-getstarted]. Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx


[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png



