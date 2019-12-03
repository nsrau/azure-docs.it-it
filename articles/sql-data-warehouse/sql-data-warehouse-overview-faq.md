---
title: Domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW)
description: Questo articolo elenca le domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW) da clienti e sviluppatori
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9e64d184ef0fd120d1bc64ce274ee882b7938df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708640"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Domande frequenti su Azure sinapsi Analytics (noto in precedenza come SQL DW)

## <a name="general"></a>Informazioni di carattere generale

D: Che cos'è Azure Synapse?

R. La sinapsi di Azure è un servizio di analisi senza limiti che riunisce data warehousing e analisi di Big Data. Consente di eseguire query sui dati in base alle proprie esigenze, usando risorse su richiesta senza server o con provisioning, su larga scala. Azure Synapse offre questi due mondi insieme a un'esperienza unificata per l'inserimento, la preparazione, la gestione e la distribuzione dei dati per esigenze immediate di business intelligence e Machine Learning. Per altre informazioni, vedere informazioni su [Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

D: Che cosa è successo ad Azure SQL Data Warehouse?

R. La sinapsi di Azure è Azure SQL Data Warehouse (SQL DW) è stata sviluppata. Abbiamo adottato lo stesso data warehouse leader del settore per un livello completamente nuovo di prestazioni e funzionalità. È possibile continuare a eseguire i carichi di lavoro di data warehouse esistenti attualmente in produzione con le sinapsi di Azure e trarre automaticamente vantaggio dalle nuove funzionalità disponibili in anteprima. Per altre informazioni, vedere informazioni su [Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

D: Che cos'è SQL Analytics?

R. Analisi SQL si riferisce alle funzionalità di data warehousing aziendali disponibili a livello generale con Azure Synapse. Per altre informazioni, vedere informazioni su [Azure sinapsi Analytics](sql-data-warehouse-overview-what-is.md).

D: Ricerca per categorie iniziare a usare la sinapsi di Azure?

R. Per ulteriori informazioni, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/sql-data-warehouse/) o [contattare le vendite](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

D: Cosa offre Azure sinapsi per la sicurezza dei dati?

R. La funzionalità sinapsi di Azure offre diverse soluzioni per la protezione dei dati, ad esempio la crittografia e il controllo. Per altre informazioni, vedere [Sicurezza].

D: Dove è possibile scoprire quali standard legali o aziendali sono conformi a sinapsi di Azure?

R. Visitare la pagina [Conformità di Microsoft] per le diverse offerte di conformità per prodotto, ad esempio SOC e ISO. Prima di tutto scegliere il titolo di conformità e quindi espandere Azure nella sezione servizi cloud in ambito Microsoft sul lato destro della pagina per vedere quali servizi sono conformi a sinapsi di Azure.

D: È possibile connettersi Power BI?

R. Sì. Tuttavia Power BI supporta le query dirette con la sinapsi di Azure, non è destinata a un numero elevato di utenti o a dati in tempo reale. Per ottimizzare ulteriormente le prestazioni di Power BI, è consigliabile usare Power BI su Azure Analysis Services o IaaS di Analysis Services.

D: Quali sono i limiti di capacità di analisi SQL?

R. Vedere la pagina relativa ai [limiti di capacità] correnti. 

D: Perché le operazioni di ridimensionamento, sospensione o ripresa richiedono così tanto tempo?

R. Le operazioni di gestione di calcolo possono essere influenzate da una serie di fattori. Un caso che si verifica spesso nelle operazioni a esecuzione prolungata è il rollback transazionali. Quando viene avviata un'operazione di sospensione o di ridimensionamento, vengono bloccate tutte le sessioni in ingresso e le query vengono svuotate. Per lasciare il sistema in uno stato stabile, è necessario eseguire il rollback delle transazioni prima di procedere con un'operazione. Maggiore è il numero di transazioni e maggiori sono le dimensioni del log delle transazioni, più duraturo sarà lo stallo dell'operazione durante il ripristino del sistema a uno stato stabile.

## <a name="user-support"></a>Supporto per l'utente

D: Ho una richiesta di funzionalità, dove posso inviarla?

R. Se si dispone di una richiesta di funzionalità, inviarla alla pagina [UserVoice]

D: Come posso fare?

R. Per informazioni sullo sviluppo con le sinapsi di Azure, è possibile porre domande nella pagina [stack overflow] . 

D: Come posso inviare un ticket di supporto?

R. [Ticket di supporto] possono essere inviati tramite il Portale di Azure.

## <a name="sql-languagefeature-support"></a>Supporto per le funzionalità/linguaggio SQL 

D: Quali tipi di dati sono supportati?

R. Vedere [tipi di dati].

D: Quali funzionalità delle tabelle sono supportate?

R. Sono supportate molte funzionalità, quelle che non sono supportate e sono documentate in [Funzionalità non supportate delle tabelle]non supportate.

## <a name="tooling-and-administration"></a>Strumenti e amministrazione

D: Analisi SQL supporta le API REST?

R. Sì. La maggior parte delle funzionalità REST che è possibile usare con il database SQL è disponibile anche con analisi SQL. È possibile trovare informazioni sulle API nelle pagine di documentazione REST o in [MSDN].


## <a name="loading"></a>Caricamento

D: Quali driver client sono supportati?

R. Il supporto dei driver per DW è reperibile nella pagina delle[Stringhe di connessione]

D: quali formati di file sono supportati da polibase?

R: Orc, RC, Parquet e testo delimitato semplice

D: a quali origini dati è possibile connettersi tramite la funzione polibase? 

R: [Archivio Data Lake di Azure] e [BLOB di archiviazione di Azure]

D: calcolo distribuzione possibile quando ci si connette a BLOB di archiviazione di Azure o ADLS? 

R: No, polibase interagisce solo con i componenti di archiviazione. 

D: È possibile connettersi ad HDI?

R: HDI può usare ADLS o WASB come livello HDFS. Se si dispone di un livello HDFS, è possibile caricare i dati in SQL DW. Tuttavia, non è possibile generare il calcolo di distribuzione per l'istanza HDI. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'intera sinapsi di Azure, vedere la pagina [Panoramica] .


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stringhe di connessione]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Sicurezza]: ./sql-data-warehouse-overview-manage-security.md
[Conformità di Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limiti di capacità]: ./sql-data-warehouse-service-capacity-limits.md
[tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Funzionalità non supportate delle tabelle]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Archivio Data Lake di Azure]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[BLOB di archiviazione di Azure]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects feature request]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Panoramica]: ./sql-data-warehouse-overview-faq.md
