---
title: Panoramica di Azure Data Lake Storage Gen1 | Microsoft Docs
description: Comprendere che cos'è Azure Data Lake Storage Gen1 (precedentemente conosciuto come Azure Data Lake Store) e il valore che fornisce per altri archivi di dati
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 438eab091fac103b66f0789beca0098b87ee44cd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885656"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Panoramica di Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

> [!TIP]
> Usare il [percorso di apprendimento di Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) per iniziare a esplorare il servizio Data Lake Storage Gen1.
> 
> 

Si può accedere a Data Lake Storage Gen1 da Hadoop (disponibile con i cluster HDInsight) mediante le API REST compatibili con WebHDFS. È progettato specificamente per consentire l'analisi su dati archiviati ed è ottimizzato per eseguire prestazioni per scenari di analisi dei dati. Per impostazione predefinita, include tutte le funzionalità a livello aziendale, protezione, gestibilità, scalabilità, affidabilità e disponibilità, essenziali per i casi di utilizzo aziendale reali.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Di seguito sono riportate alcune delle principali funzionalità di Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Creato per Hadoop
Data Lake Storage Gen1 è un file system Apache Hadoop compatibile con HDFS (Hadoop Distributed File System) e funziona con l'ecosistema Hadoop.  Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni

I dati archiviati in Data Lake Storage Gen1 possono essere analizzati facilmente mediante framework di analisi di Hadoop, come MapReduce o Hive. Si può eseguire il provisioning sui cluster di Microsoft Azure HDInsight e questi possono essere configurati per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte
Data Lake Storage Gen1 offre un'archiviazione illimitata ed è adatto per l'archiviazione di una serie di dati per l'analisi. Ciò non impone limiti per le dimensioni dell’account, le dimensioni dei file o la quantità di dati che possono essere archiviati in un Data Lake. I singoli file possono variare da kilobyte di petabyte rendendo la scelta ideale per memorizzare qualsiasi tipo di dati. I dati vengono archiviati in modo permanente creando più copie e non esiste alcun limite per la durata del tempo durante il quale i dati possono essere archiviati nel Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestazioni ottimizzate per l'analisi di Big Data
Data Lake Storage Gen1 è progettato per l'esecuzione di sistemi di analisi di scalabilità di grandi dimensioni che richiedono una velocità effettiva molto elevata per eseguire query e analisi su grandi quantità di dati. Il Data Lake propaga parti di un file su un numero di singoli server di archiviazione. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

### <a name="enterprise-ready-highly-available-and-secure"></a>Livello aziendale: Disponibilità elevata e sicura
Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente creando copie ridondanti per salvaguardarsi da eventuali errori imprevisti. Le aziende possono usare Data Lake Storage Gen1 nelle loro soluzioni come una parte importante della piattaforma di dati esistente.

Data Lake Storage Gen1 offre anche la protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Tutti i dati
Data Lake Storage Gen1 può archiviare i dati nel loro formato originale, così come sono, senza alcuna trasformazione. Data Lake Storage Gen1 non richiede la definizione di uno schema prima che i dati vengano caricati, lasciando al singolo framework di analisi l'interpretazione dei dati e la definizione di uno schema al momento dell'analisi. La possibilità di archiviare i file di dimensioni e formati arbitrari consente a Data Lake Storage Gen1 di gestire dati strutturati, semi-strutturati e non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. È possibile agire sui dati archiviati mediante SDK, portale di Azure e Azure PowerShell. Dopo aver inserito i dati nell'archivio usando queste interfacce e i contenitori appropriati, è possibile memorizzare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione particolare dei dati in base al tipo di dati archiviati.

## <a name="DataLakeStoreSecurity"></a>Proteggere i dati in Data Lake Storage Gen1
Data Lake Storage Gen1 usa Azure Active Directory per l'autenticazione e gli elenchi di controllo di accesso (ACL) per gestire l'accesso ai dati.

| Funzionalità | DESCRIZIONE |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure Active Directory (AAD) per la gestione delle identità e degli accessi per tutti i dati archiviati in Data Lake Storage Gen1. Con l'integrazione, Data Lake Storage Gen1 può usare tutte le funzionalità di AAD che includono l'autenticazione a più fattori, l'accesso condizionale, il controllo degli accessi in base al ruolo, il monitoraggio dell'uso delle applicazioni, il monitoraggio e gli avvisi di sicurezza e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione con l'interfaccia REST. Vedere [Autenticazione di Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md)|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. Gli elenchi di controllo di accesso possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento degli elenchi di controllo di accesso nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 offre anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](data-lake-store-encryption.md). Per istruzioni su come specificare la configurazione relativa alla crittografia, vedere [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md). |

Per altre informazioni sulla protezione dei dati in Data Lake Storage Gen1, fare clic sui collegamenti seguenti.

* Per istruzioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).
* Se si preferiscono i video, [guardare questo video](https://mix.office.com/watch/1q2mgzh9nn5lx) su come proteggere i dati archiviati in Data Lake Storage Gen1.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Applicazioni compatibili con Data Lake Storage Gen1
Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra bene anche con altri servizi di Azure. Per questa ragione Data Lake Storage Gen1 rappresenta un'ottima opzione per le esigenze di archiviazione dei dati. Seguire i collegamenti seguenti per altre informazioni su come usare Data Lake Storage Gen1 con componenti open source e altri servizi di Azure.

* Vedere [Applicazioni e servizi compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che è possibile usare con Data Lake Storage Gen1.
* Vedere [Integrazione con altri servizi di Azure](data-lake-store-integrate-with-other-services.md) per informazioni su come usare Data Lake Storage Gen1 con altri servizi di Azure per abilitare una gamma di servizi più ampia.
* Vedere [Scenari per l'uso di Data Lake Storage Gen1](data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Qual è il file system di Data Lake Storage Gen1 (adl://)?
È possibile accedere a Data Lake Storage Gen1 tramite il nuovo file system, AzureDataLakeFilesystem (adl://), negli ambienti Hadoop (disponibile con cluster HDInsight). Le applicazioni e servizi che utilizzano adl:// sono in grado di sfruttare altre ottimizzazioni delle prestazioni che non sono attualmente disponibili in WebHDFS. Data Lake Storage Gen1 offre in questo modo la flessibilità di scegliere se ottenere migliori prestazioni con l'uso consigliato di adl:// o mantenere il codice esistente continuando a usare direttamente l'API WebHDFS. Azure HDInsight usa AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

È possibile accedere ai dati in Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Per altre informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Visualizzare le proprietà dei dati archiviati](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Data Lake archiviazione Gen1 usando il portale di Azure](data-lake-store-get-started-portal.md)
* [Introduzione a Azure Data Lake archiviazione Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Usare Azure HDInsight con Data Lake Store Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)