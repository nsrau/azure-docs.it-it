---
title: Domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW)
description: Questo articolo elenca le domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW) da clienti e sviluppatori
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 260086d186f7e2b2d6f76c437057f5fbc8af39b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416083"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW)

## <a name="general"></a>Generale

Q. Che cos'è Azure Synapse?

A. La sinapsi di Azure è un servizio di analisi senza limiti che riunisce data warehousing e analisi di Big Data. Consente di eseguire query sui dati in base alle proprie esigenze, usando risorse su richiesta senza server o con provisioning, su larga scala. Azure Synapse offre questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la distribuzione dei dati per esigenze immediate di business intelligence e Machine Learning. Per altre informazioni, vedere [che cos'è Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cosa è successo a Azure SQL Data Warehouse?

A. La sinapsi di Azure è Azure SQL Data Warehouse (SQL DW) è stata sviluppata. Abbiamo adottato lo stesso data warehouse leader di settore per un nuovo livello di prestazioni e funzionalità. È possibile continuare a eseguire i carichi di lavoro di data warehouse esistenti in produzione con la sinapsi di Azure e trarre automaticamente vantaggio dalle nuove funzionalità disponibili in anteprima. Per altre informazioni, vedere informazioni su [Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cos'è il pool SQL sinapsi?

A. Il pool di sinapsi SQL si riferisce alle funzionalità di data warehousing aziendali disponibili a livello generale con la sinapsi di Azure. Per altre informazioni, vedere [che cos'è Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

Q. Ricerca per categorie iniziare a usare la sinapsi di Azure?

A. Per ulteriori informazioni, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/sql-data-warehouse/) o [contattare le vendite](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Q. Cosa offre Azure sinapsi per la sicurezza dei dati?

A. La funzionalità sinapsi di Azure offre diverse soluzioni per la protezione dei dati, ad esempio la crittografia e il controllo. Per altre informazioni, vedere [Sicurezza](sql-data-warehouse-overview-manage-security.md).

Q. Dove è possibile scoprire quali standard legali o aziendali sono conformi a sinapsi di Azure?

A. Visitare la pagina [Conformità di Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) per le diverse offerte di conformità per prodotto, ad esempio SOC e ISO.
Per prima cosa, scegliere in base al titolo di conformità. Quindi espandere Azure nella sezione servizi cloud di Microsoft nell'ambito sul lato destro della pagina per vedere quali servizi sono conformi a sinapsi di Azure.

Q. È possibile connettersi Power BI?

A. Sì. Tuttavia Power BI supporta le query dirette con la sinapsi di Azure, non è destinata a un numero elevato di utenti o a dati in tempo reale. Per ottimizzare ulteriormente le prestazioni di Power BI, è consigliabile usare Power BI su Azure Analysis Services o IaaS di Analysis Services.

Q. Che cosa sono i limiti di capacità del pool SQL di sinapsi?

A. Vedere la pagina relativa ai [limiti di capacità](sql-data-warehouse-service-capacity-limits.md) correnti.

Q. Perché le operazioni di ridimensionamento, sospensione o ripresa richiedono così tanto tempo?

A. Molti fattori possono influire sul tempo per le operazioni di gestione di calcolo. Un caso comune per le operazioni a esecuzione prolungata è il rollback transazionale. Quando viene avviata un'operazione di sospensione o di ridimensionamento, vengono bloccate tutte le sessioni in ingresso e le query vengono svuotate. Per lasciare il sistema in uno stato stabile, è necessario eseguire il rollback delle transazioni prima di procedere con un'operazione. Maggiore è il numero di transazioni e maggiori sono le dimensioni del log delle transazioni, più duraturo sarà lo stallo dell'operazione durante il ripristino del sistema a uno stato stabile.

## <a name="user-support"></a>Supporto per l'utente

Q. Ho una richiesta di funzionalità, dove posso inviarla?

A. Se si dispone di una richiesta di funzionalità, inviarla alla pagina [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Come posso fare?

A. Per informazioni sullo sviluppo con la sinapsi di Azure, è possibile porre domande nella pagina [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) .

Q. Come posso inviare un ticket di supporto?

A. [I ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) possono essere inviati tramite il Portale di Azure.

## <a name="sql-languagefeature-support"></a>Supporto per le funzionalità/linguaggio SQL

Q. Quali tipi di dati sono supportati?

A. Vedere [tipi di dati](sql-data-warehouse-tables-data-types.md).

Q. Quali funzionalità delle tabelle sono supportate?

A. Sono supportate molte funzionalità. Le funzionalità non supportate sono disponibili nelle [funzionalità di tabella](sql-data-warehouse-tables-data-types.md)non supportate.

## <a name="tooling-and-administration"></a>Strumenti e amministrazione

Q. Il pool SQL sinapsi supporta le API REST?

A. Sì. La maggior parte delle funzionalità REST che è possibile usare con il database SQL è disponibile anche con il pool di SQL sinapsi. È possibile trovare informazioni sulle API nelle pagine o nei [database](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)di documentazione Rest.

## <a name="loading"></a>Caricamento

Q. Quali driver client sono supportati?

A. Il supporto driver per il pool SQL sinapsi è reperibile nella pagina [stringhe di connessione](../sql/connection-strings.md)

D: quali formati di file sono supportati da polibase?

R: Orc, RC, Parquet e testo delimitato semplice

D: a quali origini dati è possibile connettersi tramite la funzione polibase?

R: [Azure Data Lake storage](sql-data-warehouse-load-from-azure-data-lake-store.md) e [BLOB di archiviazione di Azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

D: calcolo distribuzione possibile quando ci si connette a BLOB di archiviazione di Azure o ADLS?

R: No, polibase interagisce solo con i componenti di archiviazione.

D: È possibile connettersi ad HDI?

R: HDI può usare ADLS o WASB come livello HDFS. Se si dispone di un livello di HDFS, è possibile caricare i dati in un pool di SQL sinapsi. Tuttavia, non è possibile generare il calcolo di distribuzione per l'istanza HDI.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'intera sinapsi di Azure, vedere la pagina [Panoramica](sql-data-warehouse-overview-faq.md) .
