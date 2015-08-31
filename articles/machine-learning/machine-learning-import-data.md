<properties
	pageTitle="Importare dati in Azure Machine Learning Studio | Microsoft Azure"
	description="Come importare dati di training in Azure Machine Learning Studio da varie origini dati. Informazioni sui tipi di dati e i formati di dati supportati."
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
	ms.date="08/14/2015"
	ms.author="garye" />


# Importare dati di training in Azure Machine Learning Studio da varie origini dati

Quando si sviluppa una soluzione di analisi predittiva in Azure Machine Learning Studio, si esegue il training del modello tramite dati rappresentativi dello spazio problema. In questa esercitazione viene illustrato come importare dati da diverse origini dati per il training del modello in Machine Learning Studio. Vengono inoltre illustrati i formati di dati supportati.

Un determinato numero di set di dati di esempio sono disponibili in Machine Learning Studio ed è possibile utilizzarli per questo scopo (vedere [Usare i set di dati di esempio in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)). Tuttavia, è anche possibile importare i dati in Machine Learning Studio per usarli negli esperimenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Per usare i propri dati in Machine Learning Studio, è possibile caricare un file di dati in anticipo dal disco rigido locale per creare un modulo di set di dati nell'area di lavoro. Oppure, è possibile accedere ai dati da una delle diverse origini dati online mentre l'esperimento viene eseguito tramite il modulo [Lettore][reader]\:

- Archiviazione BLOB di Azure, tabella o database SQL
- Hadoop tramite HiveQL
- Un URL web tramite HTTP
- Un provider di feed di dati

Machine Learning Studio è progettato per lavorare con dati tabulari o rettangolari, ad esempio dati di testo che rappresentano dati delimitati o strutturati di un database, anche se in alcuni casi potrebbero essere usati dati non rettangolari.

È consigliabile che i dati siano relativamente puliti. Ciò significa che, prima di caricare i dati nell’esperimento, è opportuno risolvere eventuali problemi, ad esempio stringhe non racchiuse tra virgolette.

Tuttavia, in Machine Learning Studio sono disponibili moduli che consentono di eseguire alcune modifiche dei dati all'interno dell'esperimento. A seconda degli algoritmi di Machine Learning usati, potrebbe essere necessario stabilire come gestire problemi strutturali di dati, ad esempio valori mancanti e dati di tipo sparse. A tale scopo, esistono moduli che possono rivelarsi utili. Esaminare la sezione **Trasformazione dei dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.

In qualsiasi momento dell'esperimento, è possibile visualizzare o scaricare i dati prodotti da un modulo facendo clic con il pulsante destro del mouse sulla porta di output. A seconda del modulo, potrebbero essere disponibili opzioni di download diverse, oppure l’utente potrebbe essere in grado di visualizzare i dati dal browser Web in Machine Learning Studio.


## Formati di dati

È possibile importare diversi tipi di dati nell'esperimento, a seconda del meccanismo usato per importare dati e della relativa provenienza:

- Testo normale (con estensione txt)
- Valori delimitati da virgole (CSV) con un'intestazione (con estensione csv) o senza intestazione (con estensione nh.csv)
- Valori separati da tabulazioni (TSV) con un'intestazione (con estensione tsv) o senza intestazione (con estensione nh.tsv)
- Tabella Hive
- Tabella di database SQL
- Valori OData
- Dati SVMLight (con estensione svmlight) (vedere la [definizione di SVMLight](http://svmlight.joachims.org/) per informazioni sul formato)
- Dati ARFF (Attribute Relation File Format) (con estensione arff) (vedere la [definizione di ARFF](http://weka.wikispaces.com/ARFF) per informazioni sul formato)
- File ZIP (con estensione zip)
- File dell’oggetto o dell'area di lavoro R (con estensione RData)

Se si importano dati in un formato quale ARFF che include metadati, Machine Learning Studio usa i metadati per definire l'intestazione e il tipo di dati di ogni colonna. Se si importano dati in un formato quale TSV o CSV che non include metadati, Machine Learning Studio deduce il tipo di dati per ogni colonna tramite il campionamento dei dati. Se nemmeno i dati presentano intestazioni di colonna, Machine Learning Studio fornisce nomi predefiniti. È possibile specificare in modo esplicito o modificare le intestazioni e i tipi di dati delle colonne tramite l’[Editor metadati][metadata-editor].

I seguenti tipi di dati vengono riconosciuti da Machine Learning Studio:

- String
- Integer
- Double
- Boolean
- DateTime
- TimeSpan

Machine Learning Studio usa un tipo di dati interno denominato *Tabella dati* per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato Tabella dati tramite il modulo [Convertire in set di dati][convert-to-dataset]. I moduli che accettano formati diversi da Tabella dati convertiranno i dati in Tabella dati in modo automatico, prima di passare al modulo successivo. Se necessario, è possibile convertire il formato Tabella dati in CSV, TSV, ARFF o SVMLight usando altri moduli di conversione. Esaminare la sezione **Conversioni di formati di dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.


## Importare dati da un file locale

È possibile importare dati da un disco rigido locale effettuando le operazioni seguenti:

1. Fare clic su **+ NEW** nella parte inferiore della finestra di Machine Learning Studio.
2. Selezionare **SET DI DATI** e **DA FILE LOCALE**.
3. Nella finestra di dialogo **Caricare un nuovo set di dati** selezionare il file da caricare
4. Immettere un nome, identificare il tipo di dati e immettere facoltativamente una descrizione. Una descrizione è consigliata in quanto consente di registrare tutte le caratteristiche relative ai dati che è opportuno tenere presente quando si usano tali dati in futuro.
5. La casella di controllo **Questa è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistente con nuovi dati. Fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

Durante il caricamento, viene visualizzato un messaggio che indica che il file è in fase di caricamento. Il tempo di caricamento dipende dalla dimensione dei dati e dalla velocità della connessione al servizio. Se è già noto che il file richiederà molto tempo, è possibile eseguire altre operazioni in Machine Learning Studio durante l'attesa. Tuttavia, la chiusura del browser comporterà un errore di caricamento dei dati.

Una volta caricati, i dati vengono archiviati in un modulo di set di dati e sono disponibili per eventuali esperimenti nell'area di lavoro. È possibile individuare il set di dati, insieme a tutti i set di dati di esempio precaricati nell’elenco **Set di dati salvati** nella tavolozza dei moduli quando si modifica un esperimento.


## Accesso ai dati online con il modulo Lettore

Tramite il modulo [Lettore][reader] nell’esperimento, è possibile accedere ai dati da varie origini online mentre l'esperimento è in esecuzione. Perché ai dati di training si accede mentre l’esperimento è in esecuzione, tali dati sono disponibili solo in un esperimento (diversamente dai moduli di set di dati che sono disponibili in qualsiasi esperimento dell'area di lavoro).

Dopo aver aggiunto il modulo [Lettore][reader] all'esperimento, viene selezionata l’**Origine dati** e vengono fornite informazioni di accesso tramite i parametri del modulo. Ad esempio, se si seleziona **URL Web tramite HTTP**, vengono forniti l’URL di origine e il formato dei dati. Se si accede ai dati di training da Archiviazione di Azure o HDInsight (tramite una query Hive), fornire le informazioni sull'account appropriate e la posizione dei dati.

> [AZURE.NOTE]In questo articolo vengono fornite informazioni generali sul modulo [Lettore][reader]. Per ulteriori informazioni sui tipi di dati a cui è possibile accedere, i formati, i parametri e le risposte alle domande comuni, vedere l'argomento di riferimento del modulo per il modulo [Lettore][reader].


### Ottenere dati da Azure

È possibile eseguire l’importazione da tre origini dati di Azure:

- **Archiviazione BLOB di Azure**: se si usa il formato ARFF per l'archiviazione, viene eseguito il mapping delle colonne tramite i metadati di intestazione. Se si usano i formati TSV o CSV, i mapping vengono dedotti dal campionamento dei dati della colonna.
- **Archiviazione tabelle di Azure**: il modulo [Lettore][reader] analizza i dati per identificare i tipi di dati della colonna. Se i dati sono abbastanza omogenei e prevedibili, è possibile limitare il numero di righe analizzate.
- **Database SQL di Azure**: il modulo [Lettore][reader] sfrutta l’API del client SQL Azure Transact per importare i dati tramite una query di database specificata dall'utente.

Per l'archiviazione BLOB e tabelle è necessario specificare un URI di firma di accesso condiviso (Shared Access Signature URI, SAS URI) o informazioni sull'account di archiviazione di Azure per fornire l'accesso ai dati. Per un database SQL di Azure è necessario fornire informazioni sul database e l’account, nonché una query di database che identifica i dati che si vuole importare.

### Ottenere dati dal Web

È possibile usare il modulo [Lettore][reader] per leggere i dati di training da un sito Web o FTP. È necessario fornire:

- Un indirizzo URL HTTP completo di un file
- Il formato dati del file (CSV, TSV, ARFF o SvmLight)
- Per i file CSV o TSV, indicare se la prima riga nel file è un'intestazione

### Ottenere dati da Hadoop

È possibile usare il modulo [Lettore][reader] per leggere i dati di training dall'archivio distribuito usando il linguaggio di query di HiveQL. È necessario specificare la query di database Hive e fornire informazioni di accesso utente sul server HCatalog. È inoltre necessario specificare se i dati vengono archiviati in un file system distribuito Hadoop (HDFS) o in Azure e, se vengono archiviati in Azure, è necessario specificare le informazioni sull'account Azure

### Ottenere dati da un provider di feed di dati

Specificando un URL OData, è possibile leggere direttamente da un provider di feed di dati. È necessario fornire l'URL di origine e il tipo di contenuto di dati.


## Salvare i dati dell'esperimento


Talvolta l’utente potrebbe voler ricevere un risultato intermedio di un esperimento e usarlo come parte di un altro esperimento. A tale scopo, effettuare l'operazione seguente:

1. Fare clic con il pulsante destro del mouse sull'output del modulo che si vuole salvare come set di dati.

2. Fare clic su **Salva come set di dati**.

3. Quando richiesto, immettere un nome e una descrizione che consentiranno di identificare facilmente il set di dati.

4. Fare clic sul segno di spunta **OK**.

Al termine del salvataggio, il set di dati sarà disponibile per l'uso in qualsiasi esperimento dell'area di lavoro. È possibile individuarlo nell’elenco **Set di dati salvati** nella tavolozza dei moduli.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=August15_HO8-->