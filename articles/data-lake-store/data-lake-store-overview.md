---
title: Che cos'è Azure Data Lake archiviazione Gen1? | Microsoft Docs
description: Panoramica di Data Lake archiviazione Gen1 (precedentemente noto come Azure Data Lake Store) e il valore che fornisce per altri archivi dati
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118798"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Che cos'è Azure Data Lake archiviazione Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Si può accedere a Data Lake Storage Gen1 da Hadoop (disponibile con i cluster HDInsight) mediante le API REST compatibili con WebHDFS. È progettato per permettere di analitica sui dati archiviati ed è ottimizzato per le prestazioni per scenari di analitica dei dati. Gen1 di archivio Data Lake include tutte le funzionalità di livello aziendale: sicurezza, gestibilità, scalabilità, affidabilità e disponibilità.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Funzionalità principali

Di seguito sono riportate alcune delle principali funzionalità di Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Creato per Hadoop

Gen1 di archivio Data Lake è un file system Apache Hadoop che è compatibile con Hadoop Distributed File System (HDFS) e funziona con l'ecosistema Hadoop. Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni.

È possibile analizzare facilmente i dati archiviati in Data Lake archiviazione Gen1 usando Framework di analisi di Hadoop come MapReduce o Hive. È possibile effettuare il provisioning di cluster HDInsight di Azure e configurare in modo da accedere direttamente ai dati archiviati in Data Lake archiviazione Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake archiviazione Gen1 fornisce un'archiviazione illimitata e può archiviare una serie di dati di analitica. Non pone limiti alle dimensioni degli account, le dimensioni dei file o la quantità di dati che possono essere archiviati in un archivio data lake. I singoli file possono variare da kilobyte di petabyte. I dati vengono archiviati in modo permanente creando più copie. Non è previsto alcun limite per il periodo di tempo per cui i dati possono essere archiviati in archivio data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestazioni ottimizzate per l'analisi di Big Data

Gen1 di archivio Data Lake è pensato per l'esecuzione di sistemi di analisi su larga scala che richiedono una velocità effettiva di query e analizzare grandi quantità di dati. Il Data Lake propaga parti di un file su un numero di singoli server di archiviazione. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto per l'azienda: Sicurezza e disponibilità elevate

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente creando copie ridondanti per salvaguardarsi da eventuali errori imprevisti.

Data Lake Storage Gen1 offre anche la protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Tutti i dati

Data Lake archiviazione Gen1 può immagazzinare i dati nel relativo formato nativo, senza alcuna trasformazione. Data Lake Storage Gen1 non richiede la definizione di uno schema prima che i dati vengano caricati, lasciando al singolo framework di analisi l'interpretazione dei dati e la definizione di uno schema al momento dell'analisi. La possibilità di archiviare i file di dimensioni e formati arbitrari rende possibile per Data Lake archiviazione Gen1 gestire dati strutturati, semistrutturati e strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. Si opera sui dati archiviati mediante SDK, il portale di Azure e Azure Powershell. Se si inseriscono i dati nell'archivio usando queste interfacce e i contenitori appropriati, è possibile archiviare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione particolare dei dati in base al tipo di dati archiviati.

## <a name="DataLakeStoreSecurity"></a>Protezione dei dati

Data Lake archiviazione Gen1 utilizza Azure Active Directory (Azure AD) per l'autenticazione e accesso controllo elenchi (ACL) per gestire l'accesso ai dati.

| Funzionalità | DESCRIZIONE |
| --- | --- |
| Authentication |Gen1 di archivio Data Lake si integra con Azure AD per la gestione delle identità e degli accessi per tutti i dati archiviati in Data Lake archiviazione Gen1. A causa dell'integrazione, i vantaggi di Data Lake archiviazione Gen1 da tutte le Azure AD di funzionalità, ad esempio multi-factor authentication, l'accesso condizionale, controllo degli accessi in base al ruolo, monitoraggio dell'utilizzo dell'applicazione, il monitoraggio della protezione e gli avvisi e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione nell'interfaccia REST. Visualizzare [autenticazione di Data Lake archiviazione Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. È possibile abilitare gli ACL per la cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionano degli ACL nel contesto di Data Lake archiviazione Gen1, vedere [controllo di accesso in Data Lake archiviazione Gen1](data-lake-store-access-control.md). |
| Crittografia |Data Lake archiviazione Gen1 fornisce anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](data-lake-store-encryption.md). Per istruzioni su come specificare la configurazione relativa alla crittografia, vedere [Introduzione a Data Lake archiviazione Gen1 usando il portale di Azure](data-lake-store-get-started-portal.md). |

Per istruzioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilità tra le versioni

Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra anche con altri servizi di Azure. Per informazioni su come è possibile usare Data Lake archiviazione Gen1 con componenti open source e altri servizi di Azure, usare i collegamenti seguenti:

- Consultare [Applicazioni e servizi compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che è possibile usare con Data Lake Storage Gen1.
- Visualizzare [l'integrazione con altri servizi di Azure](data-lake-store-integrate-with-other-services.md) per comprendere come usare Data Lake archiviazione Gen1 con altri servizi di Azure per abilitare una vasta gamma di scenari.
- Vedere [Scenari per l'uso di Data Lake Storage Gen1](data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="data-lake-storage-gen1-file-system"></a>Sistema di data Lake archiviazione Gen1 file

Data Lake archiviazione Gen1 è accessibile tramite il file System, AzureDataLakeFilesystem (adl: / /) in ambienti Hadoop (disponibili con il cluster HDInsight). Le applicazioni e servizi che usano adl: / / possibile sfruttare altre ottimizzazioni delle prestazioni che non sono attualmente disponibili in WebHDFS. Di conseguenza, usano Data Lake archiviazione Gen1 offre la flessibilità necessaria per entrambi è apportata di ottenere prestazioni ottimali con l'opzione consigliata di utilizzo ADL://: / / o mantenere il codice esistente continuando a usare l'API WebHDFS direttamente. Azure HDInsight usa AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

È possibile accedere ai dati in Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Per altre informazioni su come accedere ai dati in Data Lake archiviazione Gen1, vedere [visualizzare le proprietà dei dati archiviati](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data Lake archiviazione Gen1 usando il portale di Azure](data-lake-store-get-started-portal.md)
- [Introduzione a Data Lake archiviazione Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)