---
title: Che cos'è Azure Data Lake Storage Gen1? | Microsoft Docs
description: Panoramica di Azure Data Lake Storage Gen1 (precedentemente noto come Azure Data Lake Store) e del suo valore aggiunto rispetto ad altri archivi dati
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 2ca6ceb326a1fd6f7aaf5a9871d043377e60de83
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85508555"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Che cos'è Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Si può accedere a Data Lake Storage Gen1 da Hadoop (disponibile con i cluster HDInsight) mediante le API REST compatibili con WebHDFS. È progettato per consentire l'analisi dei dati archiviati e ottimizzato per offrire prestazioni elevate in scenari di analisi dei dati. Azure Data Lake Storage Gen1 include tutte le funzionalità di livello aziendale: sicurezza, gestibilità, scalabilità, affidabilità e disponibilità.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Funzionalità principali

Di seguito sono riportate alcune delle principali funzionalità di Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Creato per Hadoop

Data Lake Storage Gen1 è un file system Apache Hadoop compatibile con HDFS (Hadoop Distributed File System) e con l'ecosistema Hadoop. Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni.

I dati archiviati in Data Lake Storage Gen1 possono essere analizzati facilmente mediante framework di analisi di Hadoop come MapReduce o Hive. È possibile effettuare il provisioning dei cluster Azure HDInsight e configurarli per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake Storage Gen1 offre un'archiviazione illimitata e può archiviare una varietà di dati per l'analisi. Non impone alcun limite per le dimensioni degli account, le dimensioni dei file o la quantità di dati che possono essere archiviati in un data lake. Le dimensioni dei singoli file possono spaziare da pochi kilobyte a diversi petabyte. I dati vengono archiviati in modo permanente mediante la creazione di più copie. Non esiste alcun limite sulla durata del periodo di archiviazione dei dati nel data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestazioni ottimizzate per l'analisi di Big Data

Data Lake Storage Gen1 è progettato per l'esecuzione di sistemi di analisi su larga scala che richiedono una velocità effettiva molto elevata per eseguire query e analisi su grandi quantità di dati. Il Data Lake propaga parti di un file su un numero di singoli server di archiviazione. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

### <a name="enterprise-ready-highly-available-and-secure"></a>Livello aziendale: Sicurezza e disponibilità elevate

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente creando copie ridondanti per salvaguardarsi da eventuali errori imprevisti.

Data Lake Storage Gen1 offre anche la protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Tutti i dati

Data Lake Storage Gen1 può archiviare qualsiasi tipo dii dati nel formato nativo, senza alcuna trasformazione preliminare. Data Lake Storage Gen1 non richiede la definizione di uno schema prima che i dati vengano caricati, lasciando al singolo framework di analisi l'interpretazione dei dati e la definizione di uno schema al momento dell'analisi. La capacità di archiviare file di qualsiasi dimensione e formato consente a Data Lake Storage Gen1 di gestire dati strutturati, semi-strutturati e non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. È possibile eseguire operazioni sui dati archiviati mediante SDK, il portale di Azure e Azure PowerShell. Se si inseriscono i dati nell'archivio usando queste interfacce e i contenitori appropriati, è possibile archiviare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione particolare dei dati in base al tipo di dati archiviati.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Protezione dei dati

Data Lake Storage Gen1 usa Azure Active Directory (Azure AD) per l'autenticazione e gli elenchi di controllo di accesso (ACL) per gestire l'accesso ai dati.

| Funzionalità | Descrizione |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure AD per la gestione delle identità e degli accessi per tutti i dati archiviati. Grazie a questa integrazione, Data Lake Storage Gen1 può sfruttare tutte le funzionalità di Azure AD, come l'autenticazione a più fattori, l'accesso condizionale, il controllo degli accessi in base al ruolo, il monitoraggio dell'uso delle applicazioni, il monitoraggio e gli avvisi di sicurezza e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione nell'interfaccia REST. Vedere [Autenticazione di Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. È possibile abilitare gli elenchi di controllo di accesso nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento di questi elenchi nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 offre anche la crittografia dei dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](data-lake-store-encryption.md). Per istruzioni su come specificare la configurazione relativa alla crittografia, vedere [Iniziare a usare Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-get-started-portal.md). |

Per istruzioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilità tra le versioni

Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra bene anche con altri servizi di Azure. Per altre informazioni su come usare Data Lake Storage Gen1 con componenti open source e altri servizi di Azure, usare i collegamenti seguenti:

- Consultare [Applicazioni e servizi compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che è possibile usare con Data Lake Storage Gen1.
- Vedere [Integrazione con altri servizi di Azure](data-lake-store-integrate-with-other-services.md) per informazioni su come usare Data Lake Storage Gen1 con altri servizi di Azure per coprire una gamma di scenari più ampia.
- Vedere [Scenari per l'uso di Data Lake Storage Gen1](data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="data-lake-storage-gen1-file-system"></a>File system di Data Lake Storage Gen1

È possibile accedere a Data Lake Storage Gen1 tramite il file system AzureDataLakeFilesystem (adl://) negli ambienti Hadoop (disponibile con il cluster HDInsight). Le applicazioni e i servizi che usano adl:// possono sfruttare altre ottimizzazioni delle prestazioni che non sono attualmente disponibili in WebHDFS. Di conseguenza, Data Lake Storage Gen1 offre la flessibilità di scegliere se ottenere prestazioni ottimali con l'uso consigliato di adl:// o mantenere il codice esistente continuando a usare direttamente l'API WebHDFS. Azure HDInsight usa AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

È possibile accedere ai dati in Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Per altre informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Visualizzare le proprietà dei dati archiviati](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-get-started-portal.md)
- [Iniziare a usare Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)