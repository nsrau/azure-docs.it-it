<properties
	pageTitle="Importare dati di training in Machine Learning Studio | Azure"
	description="Come importare dati di training in Azure Machine Learning Studio da varie origini dati"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="garye" />


#Importare dati di training in Azure Machine Learning Studio

Quando si sviluppa una soluzione di analisi predittiva in Azure Machine Learning Studio, si esegue il training del modello usando dati rappresentativi del problema da risolvere. In ML Studio sono disponibili numerosi set di dati di esempio che è possibile usare a questo scopo \(vedere [Usare i set di dati di esempio in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)\), ma per la realizzazione dell'esperimento è possibile anche importare in ML Studio dati personali.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

Per usare dati personali in ML Studio, è possibile caricare in anticipo un file di dati dal disco rigido locale, in modo da creare un modulo di set di dati nella propria area di lavoro. Mentre l'esperimento è in esecuzione, è possibile anche usare il modulo [Reader][reader] per accedere ai dati da una delle numerose origini online:

- Archiviazione BLOB di Azure, tabella o database SQL
- Hadoop usando HiveQL
- URL Web usando HTTP
- Provider di feed di dati

ML Studio è progettato per usare dati tabulari o rettangolari, ad esempio dati di testo delimitato o dati strutturati da un database, sebbene in alcuni casi sia possibile usare anche dati non rettangolari.

È opportuno inoltre che i dati siano relativamente puliti. Prima di caricare i dati nell'esperimento, infatti, è necessario eliminare eventuali problemi come la presenza di stringhe non racchiuse tra virgolette. In ML Studio, tuttavia, sono disponibili moduli specifici che consentono di manipolare in vario modo i dati nel corso dell'esperimento. A seconda degli algoritmi di apprendimento automatico usati, infatti, è possibile decidere in che modo dovranno essere gestiti eventuali problemi strutturali dei dati, ad esempio valori mancanti o dati di tipo sparse, e avvalersi di moduli diversi in base alla decisione. Per conoscere i moduli che svolgono queste funzioni, consultare la sezione **Data Transformation** nella tavolozza dei moduli.

In qualsiasi fase dell'esperimento, è possibile visualizzare o scaricare i dati prodotti da un modulo facendo clic con il pulsante destro del mouse sulla porta di output. A seconda del modulo, inoltre, sono disponibili opzioni di download diverse ed è possibile visualizzare i dati anche nel browser Web di ML Studio.


## Formati di dati

In base alla modalità di importazione dei dati e all'origine da cui vengono estratti, è possibile importare nel proprio esperimento vari tipi di dati:

- Testo normale \(con estensione txt\)
- Valori delimitati da virgole \(CSV\) con intestazione \(con estensione csv\) o senza intestazione \(con estensione nh.csv\)
- Valori delimitati da tabulazioni \(TSV\) con intestazione \(con estensione tsv\) o senza intestazione \(con estensione nh.tsv\)
- Tabella Hive
- Tabella di database SQL
- Valori OData
- Dati SVMLight \(con estensione svmlight\) \(vedere la [definizione di SVMLight](http://svmlight.joachims.org/) per informazioni sul formato\)
- Dati ARFF \(Attribute Relation File Format\) \(con estensione arff\) \(vedere la [definizione di ARFF](http://weka.wikispaces.com/ARFF) per informazioni sul formato\)
- File Zip \(con estensione zip\)
- File di oggetto o area di lavoro R \(con estensione RData\)

Se si importano dati in un formato che include metadati, ad esempio ARFF, ML Studio usa i metadati per definire l'intestazione e il tipo di dati per ogni colonna. Se invece si importano dati in un formato che non include metadati, ad esempio TSV o CSV, ML Studio deduce il tipo di dati per ogni colonna mediante il campionamento dei dati. Se per i dati non sono disponibili intestazioni di colonna, ML Studio propone nomi predefiniti. È possibile specificare in modo esplicito o modificare le intestazioni e i tipi di dati delle colonne usando il modulo [Metadata Editor][metadata-editor].
 
In ML Studio sono supportati i tipi di dati seguenti:

- String
- Integer
- Double
- Boolean
- DateTime
- TimeSpan

ML Studio usa un tipo di dati interno denominato *tabella dati* per spostare i dati tra i moduli. È possibile convertire in modo esplicito i propri dati in formato tabella dati usando il modulo [Convert to Dataset][convert-to-dataset]. Tutti i moduli che accettano formati diversi da tabella dati convertono automaticamente i dati in questo formato prima di spostarli nel modulo successivo. Se necessario, è possibile riconvertire i dati dal formato tabella dati al formato originale \(CSV, TSV, ARFF o SVMLight\) usando altri moduli di conversione. Per conoscere i moduli che svolgono queste funzioni, consultare la sezione**Data Format Conversions** nella tavolozza dei moduli.


## Importazione di un file di dati locale

È possibile caricare dati dal disco rigido locale seguendo questa procedura:

1. Fare clic su **+NEW** nella parte inferiore della finestra di ML Studio.
2. Selezionare **DATASET** e **FROM LOCAL FILE**.
3. Nella finestra di dialogo **Upload a new dataset** selezionare il file da caricare.
4. Specificare un nome, identificare il tipo di dati e immettere una descrizione \(facoltativo\). È consigliabile aggiungere una descrizione poiché consente di registrare eventuali caratteristiche relative ai dati di cui sarà opportuno tenere presente quando si useranno di nuovo i dati in futuro.
5. La casella di controllo **This is the new version of an existing dataset** consente di aggiornare un set di dati esistente con nuovi dati. Fare clic su questa casella di controllo e immettere il nome di un set di dati esistente.

Durante il caricamento, viene visualizzato un messaggio in cui si specifica che è in corso il caricamento del file. Il tempo di caricamento dipende dalle dimensioni dei dati e dalla velocità della connessione al servizio. Se si prevede che il caricamento del file richiederà molto tempo, mentre si attende è possibile eseguire altre operazioni in ML Studio. Se si chiude il browser, tuttavia, il caricamento dei dati avrà esito negativo.

Dopo essere stati caricati, i dati vengono memorizzati in un modulo di set di dati e risultano disponibili per qualsiasi esperimento presente nella propria area di lavoro. Mentre si modifica un esperimento, è possibile trovare il set di dati, insieme a tutti i set di dati di esempio precaricati, nell'elenco **Saved Datasets** della tavolozza dei moduli.


## Accesso ai dati online con il modulo Reader

Il modulo [Reader][reader] consente di accedere ai dati di diverse origini online mentre è in esecuzione un esperimento. Poiché si accede ai dati mentre l'esperimento è in corso, questi sono disponibili per un unico esperimento \(contrariamente ai moduli di set di dati, disponibili per tutti gli esperimenti in corso nell'area di lavoro\).

Dopo aver aggiunto il modulo [Reader][reader] all'esperimento, si seleziona l'**origine dati** e si specificano le informazioni di accesso usando i parametri del modulo. Ad esempio, se si seleziona **Web URL via HTTP**, si specificano l'URL di origine e il formato di dati. Se invece si accede ai dati dall'archiviazione di Azure o da HDInsight \(tramite una query Hive\), è necessario specificare le informazioni di account appropriate e la posizione dei dati.

> [AZURE.NOTE]Questo articolo fornisce informazioni generali sul modulo [Reader][reader]. Per ulteriori informazioni sui tipi di dati a cui è possibile accedere, i formati, i parametri e le risposte alle domande comuni, vedere l'argomento di riferimento relativo al modulo [Reader][reader].


### Uso di dati presenti in Azure

È possibile importare dati da tre origini di Azure:

- **Archiviazione BLOB di Azure** - Se si usa il formato ARFF per l'archiviazione, viene eseguito il mapping delle colonne tramite i metadati dell'intestazione. Se invece si usa il formato TSV o CSV, i mapping vengono dedotti tramite il campionamento dei dati della colonna. 
- **Archiviazione tabelle di Azure** - Il modulo [Reader][reader] esegue la scansione dei dati per identificare i tipi di dati delle colonne. Se i dati sono relativamente omogenei e prevedibili, è possibile limitare il numero di righe analizzate.
- **Database SQL di Azure** - Il modulo [Reader][reader] usa l'API client Transact-SQL di Azure per importare i dati tramite una query di database specificata dall'utente.

Per le archiviazioni BLOB e tabelle è possibile consentire l'accesso ai dati fornendo un URI di firma di accesso condiviso \(URI SAS\) o informazioni sull'account di archiviazione di Azure. Per i database SQL di Azure, invece, è necessario fornire informazioni sul database e sull'account, oltre a una query di database che identifica i dati da importare.

### Uso di dati presenti nel Web

È possibile usare il modulo [Reader][reader] per leggere dati da un sito Web o FTP. È necessario specificare:

- L'indirizzo URL HTTP completo di un file
- Il formato di dati del file \(CSV, TSV, ARFF o SvmLight\)
- Per i file CSV o TSV, indicare se la prima riga del file è un'intestazione

### Uso di dati presenti in Hadoop

È possibile usare il modulo [Reader][reader] per leggere dati dall'archivio distribuito tramite il linguaggio di query HiveQL. È necessario specificare la query di database Hive e fornire informazioni di accesso utente sul server HCatalog. È inoltre necessario specificare se i dati devono essere archiviati in un file system distribuito Hadoop \(HDFS\) o in Azure e, se in Azure, fornire informazioni sull'account Azure.

### Uso di dati presenti in un provider di feed di dati

Specificando un URL OData, è possibile leggere i dati direttamente da un provider di feed di dati. In questo caso, è necessario specificare l'URL di origine e il tipo di contenuto dei dati.


## Salvataggio dei dati dall'esperimento


In alcuni casi può essere utile acquisire un risultato intermedio di un esperimento e usarlo nell'ambito di un altro esperimento. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul modulo che si desidera salvare come set di dati.

2. Fare clic su **Save as Dataset**.

3. Quando viene richiesto, immettere un nome e una descrizione che consentano di identificare facilmente il set di dati.

4. Fare clic su **OK** \(segno di spunta\).

Al termine del salvataggio, il set di dati sarà disponibile per essere usato in uno degli esperimenti in corso nella propria area di lavoro. È possibile trovarlo nell'elenco **Saved Datasets** della tavolozza dei moduli.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->