---
title: Domande frequenti sul pool SQL dedicato (in precedenza SQL DW)
description: Questo articolo elenca le domande frequenti sul pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics da clienti e sviluppatori.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460526"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics Domande frequenti

## <a name="general"></a>Generale

Q. Che cos'è Azure Synapse?

R. Azure sinapsi è un servizio di analisi che riunisce data warehouse e analisi di Big Data. Le sinapsi di Azure offrono questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la gestione dei dati per le esigenze di business intelligence e machine learning. Per altre informazioni, vedere [che cos'è Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cosa è successo a Azure SQL Data Warehouse?

R. La sinapsi di Azure è Azure SQL Data Warehouse evoluzione. Abbiamo adottato lo stesso data warehouse leader di settore per un nuovo livello di prestazioni e funzionalità. È possibile continuare a eseguire i carichi di lavoro di data warehouse esistenti in produzione con un pool SQL dedicato (in precedenza SQL DW) in sinapsi di Azure. Per altre informazioni, vedere [Informazioni su Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cos'è un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics?

R. Il pool SQL dedicato (in precedenza SQL DW) si riferisce alle funzionalità di data warehousing aziendali disponibili a livello generale con le sinapsi di Azure. Per altre informazioni, vedere [che cos'è Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

Q. Ricerca per categorie iniziare a usare la sinapsi di Azure?

R. Per ulteriori informazioni, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/sql-data-warehouse/) o [contattare le vendite](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Q. Cosa offre Azure sinapsi per la sicurezza dei dati?

R. La funzionalità sinapsi di Azure offre diverse soluzioni per la protezione dei dati, ad esempio la crittografia e il controllo. Per altre informazioni, vedere [Sicurezza](sql-data-warehouse-overview-manage-security.md).

Q. Dove è possibile scoprire quali standard legali o aziendali sono conformi a sinapsi di Azure?

R. Visitare la pagina [Conformità di Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) per le diverse offerte di conformità per prodotto, ad esempio SOC e ISO. Per prima cosa, scegliere in base al titolo di conformità. Quindi espandere Azure nella sezione servizi cloud di Microsoft nell'ambito sul lato destro della pagina per vedere quali servizi sono conformi a sinapsi di Azure.

Q. È possibile connettersi Power BI?

R. Sì. Tuttavia Power BI supporta le query dirette con la sinapsi di Azure, non è destinata a un numero elevato di utenti o a dati in tempo reale. Per ottimizzare ulteriormente le prestazioni di Power BI, è consigliabile usare Power BI su Azure Analysis Services o IaaS di Analysis Services.

Q. Che cosa sono i limiti di capacità di un pool SQL dedicato (in precedenza SQL DW)?

R. Vedere la pagina relativa ai [limiti di capacità](sql-data-warehouse-service-capacity-limits.md) correnti.

Q. Perché le operazioni di ridimensionamento, sospensione o ripresa richiedono così tanto tempo?

R. Molti fattori possono influire sul tempo per le operazioni di gestione di calcolo. Un caso comune per le operazioni a esecuzione prolungata è il rollback transazionale. Quando viene avviata un'operazione di sospensione o di ridimensionamento, vengono bloccate tutte le sessioni in ingresso e le query vengono svuotate. Per lasciare il sistema in uno stato stabile, è necessario eseguire il rollback delle transazioni prima di procedere con un'operazione. Maggiore è il numero di transazioni e maggiori sono le dimensioni del log delle transazioni, più duraturo sarà lo stallo dell'operazione durante il ripristino del sistema a uno stato stabile.

## <a name="user-support"></a>Supporto per l'utente

Q. Ho una richiesta di funzionalità, dove posso inviarla?

R. Se si dispone di una richiesta di funzionalità, inviarla alla pagina [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Come posso fare?

R. Per informazioni sullo sviluppo con la sinapsi di Azure, è possibile porre domande nella pagina [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) .

Q. Come posso inviare un ticket di supporto?

R. [I ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) possono essere inviati tramite il Portale di Azure.

## <a name="sql-languagefeature-support"></a>Supporto per le funzionalità/linguaggio SQL

Q. Quali tipi di dati sono supportati?

R. Vedere  [tipi di dati](sql-data-warehouse-tables-data-types.md).

Q. Quali funzionalità delle tabelle sono supportate?

R. Sono supportate molte funzionalità. Le funzionalità non supportate sono disponibili nelle [funzionalità di tabella](sql-data-warehouse-tables-data-types.md)non supportate.

## <a name="tooling-and-administration"></a>Strumenti e amministrazione

Q. Il pool SQL dedicato (in precedenza SQL DW) supporta le API REST?

R. Sì. La maggior parte delle funzionalità REST che è possibile usare con il database SQL è disponibile anche con il pool SQL dedicato (in precedenza SQL DW). È possibile trovare informazioni sulle API nelle pagine o nei [database](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)di documentazione Rest.

## <a name="loading"></a>Caricamento

Q. Quali driver client sono supportati?

R. Il supporto driver per il pool SQL dedicato (denominato in precedenza SQL DW) si trova nella pagina [stringhe di connessione](sql-data-warehouse-connection-strings.md)

D: quali formati di file sono supportati da polibase?

R: Orc, RC, Parquet e testo delimitato semplice

D: a quali origini dati è possibile connettersi tramite la funzione polibase?

R: [Azure Data Lake storage](sql-data-warehouse-load-from-azure-data-lake-store.md) e [BLOB di archiviazione di Azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

D: calcolo distribuzione possibile quando ci si connette a BLOB di archiviazione di Azure o ADLS?

R: No, polibase interagisce solo con i componenti di archiviazione.

D: È possibile connettersi ad HDI?

R: HDI può usare ADLS o WASB come livello HDFS. Se si dispone di un livello di HDFS, è possibile caricare i dati in un pool SQL dedicato (in precedenza SQL DW). Tuttavia, non è possibile generare il calcolo di distribuzione per l'istanza HDI.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi, vedere la pagina [Panoramica](sql-data-warehouse-overview-what-is.md) .
