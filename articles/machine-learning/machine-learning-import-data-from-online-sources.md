---
title: Importare dati in Machine Learning Studio da origini dati online | Microsoft Docs
description: Come importare dati di training in Azure Machine Learning Studio da varie origini online.
keywords: dati di importazione, formato dati, tipi di dati, origini dati, dati di training
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 16d4586d82ed256a90d8eb6be4aab927aed1200a
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importare i dati in Azure Machine Learning Studio da diverse origini dati online con il modulo Import Data
Questo articolo illustra il supporto per l'importazione di dati online da varie origini e le informazioni necessarie per spostare i dati da queste origini a un esperimento di Azure Machine Learning.

> [!NOTE]
> Questo articolo fornisce informazioni generali sul modulo [Import Data][import-data]. Per altre informazioni sui tipi di dati a cui è possibile accedere, i formati, i parametri e le risposte alle domande comuni, vedere l'argomento di riferimento del modulo per il modulo [Import Data][import-data].
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introduzione
Il modulo [Import Data][import-data] permette di accedere ai dati provenienti da una delle origini dati online disponibili durante l'esecuzione degli esperimenti in [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* URL Web tramite HTTP
* Hadoop tramite HiveQL
* Archivio BLOB di Azure
* Tabella di Azure
* Database SQL di Azure o SQL Server in una macchina virtuale di Azure
* Database SQL Server locale
* Provider di feed di dati, attualmente OData
* Azure CosmosDB (in precedenza denominato DocumentDB)

Per accedere alle origini dati online nell'esperimento di Studio, aggiungere il modulo [Import Data][import-data], selezionare l'**origine dati** e quindi specificare i parametri necessari per accedere ai dati. Le origini dati online supportate vengono illustrate nella tabella seguente. Questa tabella riepiloga anche i formati di file supportati e i parametri usati per accedere ai dati.

Dal momento che si accede a questi dati di training durante l'esecuzione dell'esperimento, i dati sono disponibili solo durante l'esperimento. I dati archiviati in un modulo del set di dati sono invece disponibili per ogni esperimento nell'area di lavoro.

> [!IMPORTANT]
> Attualmente i moduli [Import Data][import-data] ed [Export Data][export-data] possono leggere e scrivere dati solo da un'istanza di Archiviazione di Azure creata con il modello di distribuzione classica. In altre parole, il nuovo tipo di account di archiviazione BLOB di Azure che offre un livello di accesso di archiviazione a caldo o un livello di accesso di archiviazione a freddo non è ancora supportato. 
> 
> In genere gli account di archiviazione di Azure creati prima che fosse disponibile questa opzione non dovrebbero essere influenzati. 
> Per creare un nuovo account, selezionare **Classica** come modello di distribuzione o usare Resource Manager e selezionare **Utilizzo generico** anziché **Archivio BLOB** come **Tipologia account**. 
> 
> Per altre informazioni, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente e sporadico](../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Origini dati online supportate
Il modulo **Import data** di Azure Machine Learning supporta le origini dati seguenti:

| origine dati | Descrizione | Parametri |
| --- | --- | --- |
| URL Web tramite HTTP |Legge i dati nei formati CSV (Comma-Separated Values), TSV (Tab-Separated Values), ARFF (Attribute-Relation File Format) e SVM-light (Support Vector Machines), da qualsiasi URL Web che usa HTTP. |<b>URL</b>: specifica il nome completo del file, inclusi l'URL del sito e il nome file, con qualsiasi estensione. <br/><br/><b>Formato dati</b>: specifica uno dei formati di dati supportati, ovvero CSV, TSV, ARFF o SVM-light. Se i dati includono una riga di intestazione, la riga verrà usata per assegnare i nomi di colonna. |
| Hadoop/HDFS |Legge i dati dall'archivio distribuito in Hadoop. Specificare i dati desiderati usando HiveQL, un linguaggio di query analogo a SQL. HiveQL può essere usato anche per aggregare i dati e applicare filtri ai dati prima di aggiungere i dati a Machine Learning Studio. |<b>Hive database query</b> (Query di database Hive): specifica la query Hive usata per generare i dati.<br/><br/><b>HCatalog server URI</b> (URI del server HCatalog): specifica il nome del cluster usando il formato *&lt;nome del cluster&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop user account name</b> (Nome dell'account utente Hadoop): specifica il nome dell'account utente Hadoop usato per il provisioning del cluster.<br/><br/><b>Hadoop user account password</b> (Password dell'account utente Hadoop): specifica le credenziali usate durante il provisioning del cluster. Per altre informazioni, vedere [Creare cluster Hadoop basati su Windows in HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Location of output data</b> (Posizione dei dati di output): specifica se i dati vengono archiviati in Hadoop Distributed File System (HDFS) o in Azure. <br/><ul>Se si archiviano i dati di output in HDFS, specificare l'URI del server HDFS. Assicurarsi di usare il nome del cluster HDInsight senza il prefisso HTTPS://. <br/><br/>Se si archiviano i dati di output in Azure, sarà necessario specificare il nome dell'account di archiviazione di Azure, la chiave di accesso alle risorse di archiviazione e il nome del contenitore di archiviazione.</ul> |
| Database SQL |Legge i dati archiviati in un database SQL di Azure o in un database SQL Server in esecuzione in una macchina virtuale di Azure. |<b>Nome server di database</b>: specifica il nome del server in cui il database è in esecuzione.<br/><ul>Se si usa il database SQL di Azure, immettere il nome del server generato. In genere ha il formato seguente: *&lt;identificatore_generato&gt;.database.windows.net.* <br/><br/>Nel caso di un'istanza di SQL Server ospitata in una macchina virtuale di Azure immettere *tcp:&lt;Nome DNS macchina virtuale&gt;, 1433*</ul><br/><b>Nome database</b>: specifica il nome del database nel server. <br/><br/><b>Server user account name</b> (Nome dell'account utente del server): specifica un nome utente per un account con autorizzazioni di accesso per il database. <br/><br/><b>Server user account password</b> (Password dell'account utente del server): specifica la password per l'account utente.<br/><br/><b>Accept any server certificate</b> (Accetta qualsiasi certificato server): usare questa opzione, meno sicura, se si vuole evitare di esaminare il certificato del sito prima di leggere i dati.<br/><br/><b>Query database</b>: immettere un'istruzione SQL che descriva i dati da leggere. |
| Database SQL locale |Legge i dati archiviati in un database SQL locale. |<b>Gateway dati</b>: specifica il nome del gateway di gestione dati installato in un computer in cui può accedere al database SQL Server. Per informazioni sulla configurazione del gateway, vedere [Eseguire analisi avanzate con Azure Machine Learning usando i dati di un database SQL Server locale](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome server di database</b>: specifica il nome del server in cui il database è in esecuzione.<br/><br/><b>Nome database</b>: specifica il nome del database nel server. <br/><br/><b>Server user account name</b> (Nome dell'account utente del server): specifica un nome utente per un account con autorizzazioni di accesso per il database. <br/><br/><b>Nome utente password</b>: fare clic su <b>Enter values</b> (Immettere i valori) per immettere le credenziali del database. È possibile usare Autenticazione integrata di Windows o Autenticazione di SQL Server, in base al tipo di configurazione del database SQL Server locale.<br/><br/><b>Query database</b>: immettere un'istruzione SQL che descriva i dati da leggere. |
| tabella di Azure |Legge i dati dal servizio tabelle nell'Archiviazione di Azure Storage.<br/><br/>Se si leggono raramente quantità elevate di dati, usare il servizio tabelle di Azure. Offre una soluzione di archiviazione flessibile, non relazionale (NoSQL), a scalabilità elevata, poco costosa e a disponibilità elevata. |Le opzioni disponibili nel modulo **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Questo aspetto è determinato da <b>Authentication Type</b>, che può avere un valore "PublicOrSAS" o "Account", ognuno dei quali ha un set di parametri specifico. <br/><br/><b>URI pubblico o di firma di accesso condiviso</b>. I parametri sono i seguenti:<br/><br/><ul><b>Table URI</b> (URI della tabella): specifica l'URL pubblico o di firma di accesso condiviso per la tabella.<br/><br/><b>Rows to scan for property names</b> (Righe in cui cercare i nomi di proprietà): i valori sono <i>TopN</i> (Prime N), per analizzare il numero di righe specificato, o <i>ScanAll</i> (Tutte) per ottenere tutte le righe nella tabella. <br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* (Prime N) e immettere un numero per N. Per tabelle di grandi dimensioni, questo permette di ottenere tempi di lettura più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* (Tutte) per analizzare tutte le righe. Ciò garantisce l'integrità della conversione di metadati e proprietà risultante.<br/><br/></ul><b>Account di archiviazione privato</b>. I parametri sono i seguenti: <br/><br/><ul><b>Nome account</b>: specifica il nome dell'account che contiene la tabella da leggere.<br/><br/><b>Chiave dell'account</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Nome tabella</b>: specifica il nome della tabella che contiene i dati da leggere.<br/><br/><b>Rows to scan for property names</b> (Righe in cui cercare i nomi di proprietà): i valori sono <i>TopN</i> (Prime N), per analizzare il numero di righe specificato, o <i>ScanAll</i> (Tutte) per ottenere tutte le righe nella tabella.<br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* (Prime N) e immettere un numero per N. Per tabelle di grandi dimensioni, questo permette di ottenere tempi di lettura più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* (Tutte) per analizzare tutte le righe. Ciò garantisce l'integrità della conversione di metadati e proprietà risultante.<br/><br/> |
| Archiviazione BLOB di Azure |Legge i dati archiviati nel servizio BLOB dell'Archiviazione di Azure, incluse immagini, testo non strutturato o dati binari.<br/><br/>È possibile usare il servizio BLOB per esporre pubblicamente i dati o per archiviare privatamente i dati dell'applicazione. È possibile accedere ai dati da qualsiasi posizione mediante connessioni HTTP o HTTPS. |Le opzioni disponibili nel modulo **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Ciò è dovuto al <b>tipo di autenticazione</b>, che può avere valore "PublicOrSAS" o "Account".<br/><br/><b>URI pubblico o di firma di accesso condiviso</b>. I parametri sono i seguenti:<br/><br/><ul><b>URI</b>: specifica l'URL pubblico o di firma di accesso condiviso per l'archivio BLOB.<br/><br/><b>Formato file</b>: specifica il formato dei dati nel servizio BLOB. I formati supportati sono CSV, TSV e ARFF.<br/><br/></ul><b>Account di archiviazione privato</b>. I parametri sono i seguenti: <br/><br/><ul><b>Nome account</b>: specifica il nome dell'account che contiene il BLOB da leggere.<br/><br/><b>Chiave dell'account</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Path to container, directory, or blob</b> (Percorso del contenitore, della directory o del BLOB): specifica il nome del BLOB che contiene i dati da leggere.<br/><br/><b>Formato del file BLOB</b>: specifica il formato dei dati nel servizio BLOB. I formati di dati supportati sono CSV, TSV, ARFF, CSV con una codifica specificata, ed Excel. <br/><br/><ul>Se il formato è CSV o TSV, assicurarsi di indicare se il file contiene una riga di intestazione.<br/><br/>È possibile usare l'opzione Excel per leggere dati dalle cartelle di lavoro di Excel. Nell'opzione<i>Excel data format</i> indicare se i dati si trovano in un intervallo di foglio di lavoro di Excel o in una tabella di Excel. Nell'opzione <i>Excel sheet or embedded table</i> (Foglio di Excel o tabella incorporata) specificare il nome del foglio o della tabella da cui leggere i dati.</ul><br/> |
| Provider di feed di dati |Legge dati da un provider di feed supportato. È attualmente supportato solo il formato OData (Open Data Protocol). |<b>Data content type</b> (Tipo di contenuto dei dati): specifica il formato OData.<br/><br/><b>URL di origine</b>: specifica l'URL completo per il feed di dati. <br/>Ad esempio, l'URL seguente legge dal database di esempio Northwind: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di servizi di Web Azure ML che usano i moduli Import Data ed Export Data](machine-learning-web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/

