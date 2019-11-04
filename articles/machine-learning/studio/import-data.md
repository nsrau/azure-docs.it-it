---
title: Importare dati da diverse origini dati
titleSuffix: Azure Machine Learning Studio (classic)
description: Come importare i dati in Azure Machine Learning Studio (classico) da varie origini dati. Informazioni sui tipi di dati e i formati di dati supportati.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 6e84f5c8cab6323234b81126ad3e8b1299e10171
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492892"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importare i dati di training in Azure Machine Learning Studio (classico) da varie origini dati

Per usare i propri dati in Machine Learning Studio (classico) per sviluppare ed eseguire il training di una soluzione di analisi predittiva, è possibile usare i dati di: 

* **File locale**: caricare dati locali in anticipo dal disco rigido per creare un modulo di set di dati nell'area di lavoro
* **Origini dati online** : usare il modulo [Import Data][import-data] per accedere ai dati da una delle diverse origini online durante l'esecuzione dell'esperimento
* **Esperimento di Machine Learning Studio (classico)** : usare i dati salvati come set di dati nella versione classica di Machine Learning Studio
* [**Database SQL Server locale** ](use-data-from-an-on-premises-sql-server.md): usare i dati da un database SQL Server locale senza dover copiare manualmente i dati

> [!NOTE]
> Nella versione classica di Machine Learning Studio sono disponibili numerosi set di dati di esempio che è possibile usare per i dati di training. Per informazioni su questi, vedere [usare i set di dati di esempio in Azure Machine Learning Studio (classico)](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparazione dei dati

La versione classica di Machine Learning Studio è progettata per funzionare con dati rettangolari o tabulari, ad esempio dati di testo delimitati o strutturati da un database, anche se in alcuni casi è possibile utilizzare dati non rettangolari.

È consigliabile se i dati sono relativamente puliti prima di importarli nella versione classica di studio. È opportuno risolvere eventuali problemi, ad esempio stringhe non racchiuse tra virgolette.

Sono tuttavia disponibili moduli nella versione classica di studio che consentono di modificare i dati all'interno dell'esperimento dopo aver importato i dati. A seconda degli algoritmi di Machine Learning usati, potrebbe essere necessario stabilire come gestire problemi strutturali di dati, ad esempio valori mancanti e dati di tipo sparse. A tale scopo, esistono moduli che possono rivelarsi utili. Vedere la sezione **Trasformazione dei dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.

In qualsiasi momento dell'esperimento è possibile visualizzare o scaricare i dati prodotti da un modulo facendo clic sulla porta di output. A seconda del modulo, potrebbero essere disponibili opzioni di download diverse oppure potrebbe essere possibile visualizzare i dati all'interno del Web browser nella versione classica di studio.

## <a name="supported-data-formats-and-data-types"></a>Tipi di dati e formati di dati supportati

È possibile importare diversi tipi di dati nell'esperimento, a seconda del meccanismo usato per importare dati e della relativa provenienza:

* Testo normale (con estensione txt)
* Valori delimitati da virgole (CSV) con un'intestazione (con estensione csv) o senza intestazione (con estensione nh.csv)
* Valori separati da tabulazioni (TSV) con un'intestazione (con estensione tsv) o senza intestazione (con estensione nh.tsv)
* File di Excel
* Tabella di Azure
* Tabella Hive
* Tabella di database SQL
* Valori OData
* Dati SVMLight (con estensione svmlight) (vedere la [definizione di SVMLight](http://svmlight.joachims.org/) per informazioni sul formato)
* Dati ARFF (Attribute Relation File Format) (con estensione arff) (vedere la [definizione di ARFF](https://weka.wikispaces.com/ARFF) per informazioni sul formato)
* File ZIP (con estensione zip)
* File dell’oggetto o dell'area di lavoro R (con estensione RData)

Se si importano dati in un formato come ARFF che include metadati, la versione classica di studio usa questi metadati per definire l'intestazione e il tipo di dati di ogni colonna.

Se si importano dati come il formato TSV o CSV che non include questi metadati, la versione classica di studio deduce il tipo di dati per ogni colonna eseguendo il campionamento dei dati. Se anche i dati non includono intestazioni di colonna, la versione classica di studio fornisce i nomi predefiniti.

È possibile specificare in modo esplicito o modificare le intestazioni e i tipi di dati per le colonne usando il modulo di [modifica dei metadati][edit-metadata] .

I tipi di dati seguenti sono riconosciuti dalla versione classica di studio:

* Stringa
* Integer
* DOUBLE
* boolean
* Data e ora
* Intervallo di tempo

Studio usa un tipo di dati interno denominato ***tabella dati*** per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato tabella dati usando il modulo [Convert to DataSet][convert-to-dataset] .

I moduli che accettano formati diversi da tabella dati convertiranno i dati in tabella dati in modo automatico, prima di passare al modulo successivo.

Se necessario, è possibile convertire il formato tabella dati in CSV, TSV, ARFF o SVMLight usando altri moduli di conversione.
Esaminare la sezione **Conversioni di formati di dati** della tavolozza dei moduli per i moduli che eseguono queste funzioni.

## <a name="data-capacities"></a>Capacità di dati

I moduli della versione classica di Machine Learning Studio supportano set di dati con un massimo di 10 GB di dati numerici densi per i casi d'uso comuni. Se un modulo richiede più input, il valore di 10 GB rappresenta il totale delle dimensioni di tutti gli input. È possibile campionare set di dati di dimensioni maggiori con query di Hive o del database SQL di Azure oppure usare la pre-elaborazione di Learning by Counts prima di importare i dati.  

I tipi di dati seguenti possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

* Sparse
* Categorical
* Stringhe
* Dati binari

I moduli seguenti sono limitati a set di dati inferiori a 10 GB:

* Moduli di raccomandazione
* Modulo SMOTE (Synthetic Minority Oversampling Technique)
* Moduli di script: R, Python, SQL
* Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati di input, come Join (Unione) o Feature Hashing (Hash delle funzionalità)
* Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato

Per i set di dati di dimensioni maggiori di alcuni GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare Azure HDInsight, invece di eseguire il caricamento direttamente da un file locale.

È possibile trovare informazioni sui dati di immagine nelle informazioni di riferimento sul modulo di [importazione di immagini](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes).

## <a name="import-from-a-local-file"></a>Importare da un file locale

È possibile caricare un file di dati dal disco rigido da usare come dati di training nella versione classica di studio. Quando si importa il file di dati, si crea un modulo set di dati pronto per l'uso negli esperimenti nell'area di lavoro.

Per importare dati da un disco rigido locale effettuare le operazioni seguenti:

1. Fare clic su **+ nuovo** nella parte inferiore della finestra di studio (classica).
2. Selezionare **DATASET** (SET DI DATI) e **FROM LOCAL FILE** (DA FILE LOCALE).
3. Nella finestra di dialogo **Caricare un nuovo set di dati** selezionare il file da caricare.
4. Immettere un nome, identificare il tipo di dati e immettere facoltativamente una descrizione. Una descrizione è consigliabile perché consente di registrare tutte le caratteristiche relative ai dati da tenere presenti quando si useranno tali dati in futuro.
5. La casella di controllo **Questa è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistente con nuovi dati. A tale scopo, fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

![Caricare un nuovo set di dati](./media/import-data/upload-dataset-from-local-file.png)

Il tempo di caricamento dipende dalle dimensioni dei dati e dalla velocità della connessione al servizio. Se si è certi che il file richiederà molto tempo, è possibile eseguire altre operazioni all'interno della versione classica di studio mentre si è in attesa. In caso di chiusura del browser prima del completamento del caricamento dei dati, tuttavia, il caricamento dei dati avrà esito negativo.

Una volta caricati, i dati vengono archiviati in un modulo di set di dati e sono disponibili per eventuali esperimenti nell'area di lavoro.

Durante la modifica di un esperimento, è possibile individuare i set di dati caricati nell'elenco **My Datasets** (Set di dati personali) presente nell'elenco **Saved Datasets** (Set di dati salvati) nella tavolozza dei moduli. È possibile trascinare il set di dati nell'area di disegno dell'esperimento per usarlo per l'ulteriore analisi e l'apprendimento automatico.

## <a name="import-from-online-data-sources"></a>Importare da origini dati online

Usando il modulo [Import Data][import-data] , l'esperimento può importare dati da diverse origini dati online mentre l'esperimento è in esecuzione.

> [!NOTE]
> Questo articolo fornisce informazioni generali sul modulo [Import Data][import-data] . Per informazioni più dettagliate sui tipi di dati a cui è possibile accedere, i formati, i parametri e le risposte alle domande più comuni, vedere l'argomento di riferimento sui moduli per il modulo [Import Data][import-data] .

Usando il modulo [Import Data][import-data] , è possibile accedere ai dati da una delle diverse origini dati online durante l'esecuzione dell'esperimento:

* URL Web tramite HTTP
* Hadoop tramite HiveQL
* Archivio BLOB di Azure
* Tabella di Azure
* Database SQL di Azure o SQL Server in una macchina virtuale di Azure
* Database SQL Server locale
* Provider di feed di dati, attualmente OData
* Azure Cosmos DB

Dal momento che si accede a questi dati di training durante l'esecuzione dell'esperimento, i dati sono disponibili solo durante l'esperimento. I dati archiviati in un modulo del set di dati sono invece disponibili per ogni esperimento nell'area di lavoro.

Per accedere alle origini dati online nell'esperimento di studio (classico), aggiungere il modulo [Import Data (Importa dati][import-data] ) all'esperimento. In **Properties** (Proprietà) selezionare **Launch Import Data Wizard** (Avvia importazione dati guidata) per le istruzioni dettagliate per selezionare e configurare l'origine dati. In alternativa, è possibile selezionare manualmente **Data source** (Origine dati) in **Properties** (Proprietà) e specificare i parametri necessari per accedere ai dati.

Le origini dati online supportate vengono illustrate nella tabella seguente. Questa tabella riepiloga anche i formati di file supportati e i parametri usati per accedere ai dati.

> [!IMPORTANT]
> Attualmente i moduli [Import Data][import-data] ed [Export Data][export-data] possono leggere e scrivere dati solo dall'archiviazione di Azure creata con il modello di distribuzione classica. In altre parole, il nuovo tipo di account di archiviazione BLOB di Azure che offre un livello di accesso di archiviazione a caldo o un livello di accesso di archiviazione a freddo non è ancora supportato.
>
> In genere gli account di archiviazione di Azure creati prima che fosse disponibile questa opzione non dovrebbero essere influenzati.
> Per creare un nuovo account, selezionare **Classica** come modello di distribuzione o usare Resource Manager e selezionare **Utilizzo generico** anziché **Archivio BLOB** come **Tipologia account**.
>
> Per altre informazioni, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Origini dati online supportate
La versione classica del modulo Azure Machine Learning Studio **Import Data** supporta le origini dati seguenti:

| origine dati | Description | parameters |
| --- | --- | --- |
| URL Web tramite HTTP |Legge i dati nei formati CSV (Comma-Separated Values), TSV (Tab-Separated Values), ARFF (Attribute-Relation File Format) e SVM-light (Support Vector Machines), da qualsiasi URL Web che usa HTTP. |<b>URL</b>: specifica il nome completo del file, inclusi l'URL del sito e il nome file, con qualsiasi estensione. <br/><br/><b>Formato dati</b>: specifica uno dei formati di dati supportati, ovvero CSV, TSV, ARFF o SVM-light. Se i dati includono una riga di intestazione, la riga verrà usata per assegnare i nomi di colonna. |
| Hadoop/HDFS |Legge i dati dall'archivio distribuito in Hadoop. Specificare i dati desiderati usando HiveQL, un linguaggio di query analogo a SQL. HiveQL può essere usato anche per aggregare i dati ed eseguire il filtro dei dati prima di aggiungere i dati alla versione classica di studio. |<b>Hive database query</b> (Query di database Hive): specifica la query Hive usata per generare i dati.<br/><br/><b>HCatalog server URI</b> (URI del server HCatalog): specifica il nome del cluster usando il formato *&lt;nome del cluster&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop user account name</b> (Nome dell'account utente Hadoop): specifica il nome dell'account utente Hadoop usato per il provisioning del cluster.<br/><br/><b>Hadoop user account password</b> (Password dell'account utente Hadoop): specifica le credenziali usate durante il provisioning del cluster. Per altre informazioni, vedere [Creare cluster Hadoop basati su Windows in HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Location of output data</b> (Posizione dei dati di output): specifica se i dati vengono archiviati in Hadoop Distributed File System (HDFS) o in Azure. <br/><ul>Se si archiviano i dati di output in HDFS, specificare l'URI del server HDFS. Assicurarsi di usare il nome del cluster HDInsight senza il prefisso HTTPS://. <br/><br/>Se si archiviano i dati di output in Azure, sarà necessario specificare il nome dell'account di archiviazione di Azure, la chiave di accesso alle risorse di archiviazione e il nome del contenitore di archiviazione.</ul> |
| Database SQL |Legge i dati archiviati in un database SQL di Azure o in un database SQL Server in esecuzione in una macchina virtuale di Azure. |<b>Nome server di database</b>: specifica il nome del server in cui il database è in esecuzione.<br/><ul>Se si usa il database SQL di Azure, immettere il nome del server generato. In genere ha il formato seguente: *&lt;identificatore_generato&gt;.database.windows.net.* <br/><br/>Nel caso di un server SQL ospitato in una macchina virtuale di Azure *, immettere TCP:&lt;nome DNS della macchina virtuale&gt;, 1433*</ul><br/><b>Nome database</b>: specifica il nome del database nel server. <br/><br/><b>Server user account name</b> (Nome dell'account utente del server): specifica un nome utente per un account con autorizzazioni di accesso per il database. <br/><br/><b>Server user account password</b> (Password dell'account utente del server): specifica la password per l'account utente.<br/><br/><b>Query database</b>: immettere un'istruzione SQL che descriva i dati da leggere. |
| Database SQL locale |Legge i dati archiviati in un database SQL locale. |<b>Gateway dati</b>: specifica il nome del gateway di gestione dati installato in un computer in cui può accedere al database SQL Server. Per informazioni sulla configurazione del gateway, vedere [eseguire analisi avanzate con la versione classica di Azure Machine Learning Studio usando i dati di un'istanza di SQL Server locale](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome server di database</b>: specifica il nome del server in cui il database è in esecuzione.<br/><br/><b>Nome database</b>: specifica il nome del database nel server. <br/><br/><b>Server user account name</b> (Nome dell'account utente del server): specifica un nome utente per un account con autorizzazioni di accesso per il database. <br/><br/><b>Nome utente password</b>: fare clic su <b>Enter values</b> (Immettere i valori) per immettere le credenziali del database. È possibile usare Autenticazione integrata di Windows o Autenticazione di SQL Server, in base al tipo di configurazione del database SQL Server locale.<br/><br/><b>Query database</b>: immettere un'istruzione SQL che descriva i dati da leggere. |
| tabella di Azure |Legge i dati dal servizio tabelle nell'Archiviazione di Azure Storage.<br/><br/>Se si leggono raramente quantità elevate di dati, usare il servizio tabelle di Azure. Offre una soluzione di archiviazione flessibile, non relazionale (NoSQL), a scalabilità elevata, poco costosa e a disponibilità elevata. |Le opzioni disponibili nel modulo **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Questo aspetto è determinato da <b>Authentication Type</b>, che può avere un valore "PublicOrSAS" o "Account", ognuno dei quali ha un set di parametri specifico. <br/><br/><b>URI pubblico o di firma di accesso condiviso</b>. I parametri sono i seguenti:<br/><br/><ul><b>Table URI</b> (URI della tabella): specifica l'URL pubblico o di firma di accesso condiviso per la tabella.<br/><br/><b>Rows to scan for property names</b> (Righe in cui cercare i nomi di proprietà): i valori sono <i>TopN</i> (Prime N), per analizzare il numero di righe specificato, o <i>ScanAll</i> (Tutte) per ottenere tutte le righe nella tabella. <br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* (Prime N) e immettere un numero per N. Per tabelle di grandi dimensioni, questo permette di ottenere tempi di lettura più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* (Tutte) per analizzare tutte le righe. Ciò garantisce l'integrità della conversione di metadati e proprietà risultante.<br/><br/></ul><b>Account di archiviazione privato</b>. I parametri sono i seguenti: <br/><br/><ul><b>Nome account</b>: specifica il nome dell'account che contiene la tabella da leggere.<br/><br/><b>Chiave dell'account</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Nome tabella</b>: specifica il nome della tabella che contiene i dati da leggere.<br/><br/><b>Rows to scan for property names</b> (Righe in cui cercare i nomi di proprietà): i valori sono <i>TopN</i> (Prime N), per analizzare il numero di righe specificato, o <i>ScanAll</i> (Tutte) per ottenere tutte le righe nella tabella.<br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* (Prime N) e immettere un numero per N. Per tabelle di grandi dimensioni, questo permette di ottenere tempi di lettura più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* (Tutte) per analizzare tutte le righe. Ciò garantisce l'integrità della conversione di metadati e proprietà risultante.<br/><br/> |
| Archiviazione BLOB di Azure |Legge i dati archiviati nel servizio BLOB dell'Archiviazione di Azure, incluse immagini, testo non strutturato o dati binari.<br/><br/>È possibile usare il servizio BLOB per esporre pubblicamente i dati o per archiviare privatamente i dati dell'applicazione. È possibile accedere ai dati da qualsiasi posizione mediante connessioni HTTP o HTTPS. |Le opzioni disponibili nel modulo **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Ciò è dovuto al <b>tipo di autenticazione</b>, che può avere valore "PublicOrSAS" o "Account".<br/><br/><b>URI pubblico o di firma di accesso condiviso</b>. I parametri sono i seguenti:<br/><br/><ul><b>URI</b>: specifica l'URL pubblico o di firma di accesso condiviso per l'archivio BLOB.<br/><br/><b>Formato file</b>: specifica il formato dei dati nel servizio BLOB. I formati supportati sono CSV, TSV e ARFF.<br/><br/></ul><b>Account di archiviazione privato</b>. I parametri sono i seguenti: <br/><br/><ul><b>Nome account</b>: specifica il nome dell'account che contiene il BLOB da leggere.<br/><br/><b>Chiave dell'account</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Path to container, directory, or blob</b> (Percorso del contenitore, della directory o del BLOB): specifica il nome del BLOB che contiene i dati da leggere.<br/><br/><b>Formato del file BLOB</b>: specifica il formato dei dati nel servizio BLOB. I formati di dati supportati sono CSV, TSV, ARFF, CSV con una codifica specificata, ed Excel. <br/><br/><ul>Se il formato è CSV o TSV, assicurarsi di indicare se il file contiene una riga di intestazione.<br/><br/>È possibile usare l'opzione Excel per leggere dati dalle cartelle di lavoro di Excel. Nell'opzione<i>Excel data format</i> indicare se i dati si trovano in un intervallo di foglio di lavoro di Excel o in una tabella di Excel. Nell'opzione <i>Excel sheet or embedded table</i> (Foglio di Excel o tabella incorporata) specificare il nome del foglio o della tabella da cui leggere i dati.</ul><br/> |
| Provider di feed di dati |Legge dati da un provider di feed supportato. È attualmente supportato solo il formato OData (Open Data Protocol). |<b>Data content type</b> (Tipo di contenuto dei dati): specifica il formato OData.<br/><br/><b>URL di origine</b>: specifica l'URL completo per il feed di dati. <br/>Ad esempio, il seguente URL legge dal database di esempio Northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importare da un altro esperimento

Talvolta si vuole ottenere un risultato intermedio da un esperimento e usare tale risultato come parte di un altro esperimento. A tale scopo, si salva il modulo come un set di dati:

1. Fare clic sull'output del modulo da salvare come set di dati.
2. Fare clic su **Salva come set di dati**.
3. Quando richiesto, immettere un nome e una descrizione che consentiranno di identificare facilmente il set di dati.
4. Fare clic sul segno di spunta **OK** .

Al termine del salvataggio, il set di dati sarà disponibile per l'uso in qualsiasi esperimento dell'area di lavoro. È possibile individuarlo nell’elenco **Set di dati salvati** nella tavolozza dei moduli.

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di Azure Machine Learning Studio servizi Web che utilizzano i moduli importazione dati e esportazione dati](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
