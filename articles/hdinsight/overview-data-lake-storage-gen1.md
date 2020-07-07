---
title: Panoramica di Azure Data Lake Storage Gen1 in HDInsight
description: Panoramica di Data Lake Storage Gen1 in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82187246"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Panoramica di Azure Data Lake Storage Gen1 in HDInsight

Azure Data Lake Storage Gen1 è un repository con iperscalabilità a livello aziendale per carichi di lavoro di analisi di Big Data. Con Azure Data Lake è possibile acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento. E in un'unica posizione per le analisi operative ed esplorative.

Accedere a Data Lake Storage Gen1 da Hadoop (disponibile con un cluster HDInsight) mediante le API REST compatibili con WebHDFS. Data Lake Storage Gen1 è progettato per abilitare l'analisi dei dati archiviati e le prestazioni sono ottimizzate per scenari di analisi dei dati. Gen1 include le funzionalità essenziali per i casi d'uso aziendali reali. Queste funzionalità includono sicurezza, gestibilità, adattabilità, affidabilità e disponibilità.

Per altre informazioni su Azure Data Lake Storage Gen1, vedere l'articolo dettagliato [Panoramica di Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Di seguito sono riportate le principali funzionalità di Data Lake Storage Gen1.

## <a name="compatibility-with-hadoop"></a>Compatibilità con Hadoop

Data Lake Storage Gen1 è un Apache Hadoop file system compatibile con l'ambiente HDFS e Hadoop.  Le applicazioni o i servizi HDInsight che usano l'API WebHDFS possono essere facilmente integrati con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni.

I dati archiviati in Data Lake Storage Gen1 possono essere facilmente analizzati con i Framework di analisi Hadoop. Framework come MapReduce o hive. È possibile eseguire il provisioning dei cluster Azure HDInsight e configurarli per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake Storage Gen1 offre un'archiviazione illimitata ed è adatta per l'archiviazione di diversi tipi di dati per l'analisi. Non impone limiti per le dimensioni degli account o per le dimensioni dei file. O la quantità di dati che possono essere archiviati in un data Lake. I singoli file hanno dimensioni comprese tra kilobyte e petabyte, rendendo Data Lake Storage Gen1 un'ottima scelta per archiviare qualsiasi tipo di dati. I dati vengono archiviati in modo durevole mediante l'esecuzione di più copie. Non vi sono limiti per quanto tempo i dati possono essere archiviati in data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Ottimizzazione delle prestazioni per l'analisi di Big Data

Data Lake Storage Gen1 è progettato per i sistemi analitici. Sistemi che richiedono una velocità effettiva elevata per eseguire query e analizzare grandi quantità di dati. Il Data Lake distribuisce parti di un file su più server di archiviazione singoli. Durante l'analisi dei dati, questa configurazione migliora la velocità effettiva di lettura quando il file viene letto in parallelo.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Conformità per Enterprise: disponibilità elevata e sicurezza

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente: copie ridondanti salvaguardano da errori imprevisti. Le aziende possono usare Data Lake Storage Gen1 nelle loro soluzioni come una parte importante della piattaforma di dati esistente.

Data Lake Storage Gen1 offre anche protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Strutture di dati flessibili

Data Lake Storage Gen1 può archiviare i dati nel formato nativo, così come sono, senza alcuna trasformazione preliminare. Non richiede la definizione di uno schema prima che i dati vengono caricati. Il singolo framework di analisi interpreta i dati e definisce uno schema al momento dell'analisi. Data Lake Storage Gen1 possibile gestire i dati strutturati. E semistrutturati nelle e i dati non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. È possibile agire sui dati archiviati mediante SDK, il portale di Azure e Azure PowerShell. I dati inseriti nell'archivio con tali interfacce e contenitori possono archiviare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue alcuna gestione speciale dei dati in base al tipo di dati.

## <a name="data-security-in-data-lake-storage-gen1"></a>Sicurezza dei dati in Data Lake Storage Gen1

Data Lake Storage Gen1 usa Azure Active Directory per l'autenticazione e gli elenchi di controllo di accesso (ACL) per la gestione dell'accesso ai dati.

| **Funzionalità** | **Descrizione** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure Active Directory (Azure AD) per la gestione delle identità e degli accessi per tutti i dati in esso archiviati. Grazie a questa integrazione, Data Lake Storage Gen1 usufruisce di tutte le funzionalità di Azure AD. Queste funzionalità includono l'autenticazione a più fattori, l'accesso condizionale e il controllo degli accessi in base al ruolo. Inoltre, il monitoraggio dell'utilizzo delle applicazioni, il monitoraggio e gli avvisi di sicurezza e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione nell'interfaccia REST. Vedere [Autenticazione con Azure Data Lake Storage Gen1 tramite Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. Gli elenchi di controllo di accesso possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento degli ACL nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 offre anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Per istruzioni su come specificare una configurazione relativa alla crittografia, vedere [Iniziare a usare Azure Data Lake Storage Gen1 tramite il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Per altre informazioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Applicazioni compatibili con Data Lake Storage Gen1

Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ambiente Hadoop. Si integra bene anche con altri servizi di Azure.  Seguire i collegamenti seguenti per altre informazioni sul modo in cui è possibile usare Data Lake Storage Gen1 sia con componenti open source che con altri servizi di Azure.

* Vedere [applicazioni open source Big data che funzionano con Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Vedere [Integrazione di Azure Data Lake Storage Gen1 con altri servizi di Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) per informazioni su come usare Data Lake Storage Gen1 con altri servizi di Azure per abilitare una più ampia gamma di scenari.
* [Per i requisiti Big data, vedere uso di Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>File system di Data Lake Storage Gen1 (adl://)

Negli ambienti Hadoop è possibile accedere a Data Lake Storage Gen1 tramite la nuova file system, ovvero AzureDataLakeFilesystem (adl://). Le prestazioni delle applicazioni e dei servizi che usano `adl://` possono essere ottimizzate in modi che non sono attualmente disponibili in WebHDFS. Di conseguenza, è possibile ottenere la flessibilità necessaria per ottenere le migliori prestazioni usando la adl://consigliata. In alternativa, mantenere il codice esistente continuando a usare direttamente l'API WebHDFS. Azure HDInsight sfrutta al massimo AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

Accedere ai dati in Data Lake Storage Gen1 usando l'URI seguente:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Per altre informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Azioni disponibili sui dati archiviati](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)
* [Panoramica di Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)