---
title: Che cos'è Azure Data Lake Storage Gen1? | Microsoft Docs
description: Panoramica di Data Lake Storage Gen1 (precedentemente noto come Azure Data Lake Store) e il valore fornito rispetto ad altri archivi dati
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118798"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Che cos'è Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Si può accedere a Data Lake Storage Gen1 da Hadoop (disponibile con i cluster HDInsight) mediante le API REST compatibili con WebHDFS. È progettato per abilitare l'analisi dei dati archiviati ed è sintonizzato per le prestazioni per gli scenari di analisi dei dati. Data Lake Storage Gen1 include tutte le funzionalità di livello enterprise: sicurezza, gestibilità, scalabilità, affidabilità e disponibilità.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Funzionalità principali

Di seguito sono riportate alcune delle principali funzionalità di Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Creato per Hadoop

Data Lake Storage Gen1 è un file system Apache Hadoop compatibile con Hadoop Distributed File System (HDFS) e compatibile con l'ecosistema Hadoop. Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni.

È possibile analizzare facilmente i dati archiviati in Data Lake Storage Gen1 usando framework analitici Hadoop come MapReduce o Hive.You can easily analyze data stored in Data Lake Storage Gen1 using Hadoop analytic frameworks such as MapReduce or Hive. È possibile eseguire il provisioning dei cluster di Azure HDInsight e configurarli per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.You can provision Azure HDInsight clusters and configure them to directly access data stored in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake Storage Gen1 offre uno spazio di archiviazione illimitato e può archiviare una varietà di dati per l'analisi. Non impone limiti alle dimensioni dell'account, alle dimensioni dei file o alla quantità di dati che possono essere archiviati in un data lake. Le dimensioni dei singoli file possono variare da kilobyte a petabyte. I dati vengono archiviati in modo durevole eseguendo più copie. Non vi è alcun limite alla durata del periodo di tempo per il quale i dati possono essere archiviati nel data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestazioni ottimizzate per l'analisi di Big Data

Data Lake Storage Gen1 è progettato per l'esecuzione di sistemi analitici su larga scala che richiedono una velocità effettiva massiccia per eseguire query e analizzare grandi quantità di dati. Il Data Lake propaga parti di un file su un numero di singoli server di archiviazione. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto per l'azienda: altamente disponibile e sicuro

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente creando copie ridondanti per salvaguardarsi da eventuali errori imprevisti.

Data Lake Storage Gen1 offre anche la protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Tutti i dati

Data Lake Storage Gen1 è in grado di archiviare tutti i dati nel formato nativo, senza richiedere alcuna trasformazione precedente. Data Lake Storage Gen1 non richiede la definizione di uno schema prima che i dati vengano caricati, lasciando al singolo framework di analisi l'interpretazione dei dati e la definizione di uno schema al momento dell'analisi. La possibilità di archiviare file di dimensioni e formati arbitrari consente a Data Lake Storage Gen1 di gestire dati strutturati, semistrutturati e non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. Si opera sui dati archiviati usando SDK, il portale di Azure e Azure Powershell.You operate on the stored data using SdkKs, the Azure portal, and Azure Powershell. Se si inserisceno i dati nell'archivio utilizzando queste interfacce e i contenitori appropriati, è possibile archiviare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione particolare dei dati in base al tipo di dati archiviati.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Protezione dei dati

Data Lake Storage Gen1 usa Azure Active Directory (Azure AD) per l'autenticazione e gli elenchi di controllo di accesso (ACL) per gestire l'accesso ai dati.

| Funzionalità | Descrizione |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure AD per la gestione di identità e accessi per tutti i dati archiviati in Data Lake Storage Gen1. A causa dell'integrazione, Data Lake Storage Gen1 trae vantaggio da tutte le funzionalità di Azure AD, ad esempio l'autenticazione a più fattori, l'accesso condizionale, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo delle applicazioni, il monitoraggio della sicurezza e gli avvisi e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione nell'interfaccia REST. Vedere [Data Lake Storage Gen1 authentication (Autenticazione Gen1 di Data Lake Storage).](data-lakes-store-authentication-using-azure-active-directory.md)|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. È possibile abilitare gli ACL nella cartella principale, nelle sottocartelle e nei singoli file. Per ulteriori informazioni sul funzionamento degli ACL nel contesto di Data Lake Storage Gen1, vedere [Controllo dell'accesso in Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 fornisce anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](data-lake-store-encryption.md). Per istruzioni su come fornire la configurazione correlata alla crittografia, vedere Introduzione a [Data Lake Storage Gen1 usando il portale](data-lake-store-get-started-portal.md)di Azure. |

Per istruzioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilità tra le versioni

Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra bene anche con altri servizi di Azure.It also integrates well with other Azure services. Per altre informazioni su come usare Data Lake Storage Gen1 con componenti open source e altri servizi di Azure, usare i collegamenti seguenti:To learn more about how you can use Data Lake Storage Gen1 with open-source components and other Azure services, use the following links:

- Consultare [Applicazioni e servizi compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che è possibile usare con Data Lake Storage Gen1.
- Vedere [Integrazione con altri servizi](data-lake-store-integrate-with-other-services.md) di Azure per comprendere come usare Data Lake Storage Gen1 con altri servizi di Azure per abilitare una gamma più ampia di scenari.
- Vedere [Scenari per l'uso di Data Lake Storage Gen1](data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="data-lake-storage-gen1-file-system"></a>File system Data Lake Storage Gen1

È possibile accedere a Data Lake Storage Gen1 tramite il file system AzureDataLakeFilesystem (adl://) negli ambienti Hadoop (disponibile con il cluster HDInsight). Le applicazioni e i servizi che utilizzano adl:// possono sfruttare ulteriori ottimizzazioni delle prestazioni attualmente non disponibili in WebHDFS. Di conseguenza, Data Lake Storage Gen1 offre la flessibilità necessaria per sfruttare le prestazioni migliori con l'opzione consigliata di utilizzare adl:// o mantenere il codice esistente continuando a utilizzare direttamente l'API WebHDFS. Azure HDInsight usa AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

È possibile accedere ai dati in Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Per ulteriori informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Visualizzare le proprietà dei dati archiviati.](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data Lake Storage Gen1 usando il portale di AzureGet started with Data Lake Storage Gen1 using the Azure portal](data-lake-store-get-started-portal.md)
- [Introduzione a Data Lake Storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)