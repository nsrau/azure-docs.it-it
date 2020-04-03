---
title: Domande frequenti su Azure Synapse Analytics (in precedenza SQL DW)
description: Questo articolo elenca le domande frequenti su Azure Synapse Analytics (in precedenza SQL DW) di clienti e sviluppatori
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9ce87bac6fbcc3833684dc540c542bc14c9b4955
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586515"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Domande frequenti su Azure Synapse Analytics (in precedenza SQL DW)

## <a name="general"></a>Generale

Q. Che cos'è Azure Synapse?

R. Azure Synapse è un servizio di analisi illimitato che riunisce il data warehousing e l'analisi dei Big Data. Ti dà la libertà di interrogare i dati alle tue condizioni, utilizzando risorse su richiesta senza server o di cui è stato eseguito il provisioning - su larga scala. Azure Synapse riunisce questi due mondi con un'esperienza unificata per l'inserimento, la preparazione, la gestione e la gestione dei dati per le esigenze immediate di Business Intelligence e apprendimento automatico. Per altre informazioni, vedere [Che cos'è Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cosa è successo ad Azure SQL Data Warehouse?

R. Azure Synapse è Azure SQL Data Warehouse (SQL DW) evoluto. Abbiamo portato lo stesso data warehouse leader del settore a un livello di prestazioni e funzionalità completamente nuovo. È possibile continuare a eseguire i carichi di lavoro del data warehouse esistenti nell'ambiente di produzione con Azure Synapse e trarre automaticamente vantaggio dalle nuove funzionalità, in anteprima. Per altre informazioni, vedere [Che cos'è Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Che cos'è il pool Sql Synapse?

R. Synapse SQL pool refers to the enterprise data warehousing features that are generally available with Azure Synapse. Per altre informazioni, vedere [Che cos'è Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Come si inizia a usare Azure Synapse?

R. È possibile iniziare con un [account gratuito di Azure](https://azure.microsoft.com/free/sql-data-warehouse/) o [contattare le vendite per ulteriori informazioni.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html) 

Q. Cosa offre Azure Synapse per la sicurezza dei dati?

R. Azure Synapse offre diverse soluzioni per la protezione dei dati, ad esempio TDE e il controllo. Per altre informazioni, vedere [Sicurezza](sql-data-warehouse-overview-manage-security.md).

Q. Dove è possibile individuare gli standard legali o aziendali con cui Azure Synapse è conforme?

R. Visitare la pagina [Conformità di Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) per le diverse offerte di conformità per prodotto, ad esempio SOC e ISO. In primo luogo, scegliere in base al titolo di conformità. Espandere quindi Azure nella sezione Servizi cloud nell'ambito Microsoft sul lato destro della pagina per vedere quali servizi sono conformi a Azure Synapse.Then expand Azure in the Microsoft in-scope cloud services section on the right side of the page to see what services are Azure Synapse compliant.

Q. È possibile connettere Power BI?

R.  Sì. Anche se Power BI supporta query dirette con Azure Synapse, non è destinato a un numero elevato di utenti o dati in tempo reale. Per ottimizzare ulteriormente le prestazioni di Power BI, è consigliabile usare Power BI in Azure Analysis Services o Analysis Service IaaS.To optimize Power BI performance further, consider using Power BI on top of Azure Analysis Services or Analysis Service IaaS.

Q. Quali sono i limiti di capacità del pool SQL Synapse?

R. Vedere la pagina relativa ai [limiti di capacità](sql-data-warehouse-service-capacity-limits.md) correnti. 

Q. Perché le operazioni di ridimensionamento, sospensione o ripresa richiedono così tanto tempo?

R. Diversi fattori possono influenzare il tempo per le operazioni di gestione del calcolo. Un caso comune per le operazioni a esecuzione prolungata è il rollback transazionale. Quando viene avviata un'operazione di sospensione o di ridimensionamento, vengono bloccate tutte le sessioni in ingresso e le query vengono svuotate. Per lasciare il sistema in uno stato stabile, è necessario eseguire il rollback delle transazioni prima di procedere con un'operazione. Maggiore è il numero di transazioni e maggiori sono le dimensioni del log delle transazioni, più duraturo sarà lo stallo dell'operazione durante il ripristino del sistema a uno stato stabile.

## <a name="user-support"></a>Supporto per l'utente

Q. Ho una richiesta di funzionalità, dove posso inviarla?

R. Se si dispone di una richiesta di funzionalità, inviarla alla pagina [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Come posso fare?

R. Per assistenza con lo sviluppo con Azure Synapse, è possibile porre domande nella pagina [Overflow dello stack.](https://stackoverflow.com/questions/tagged/azure-sqldw) 

Q. Come posso inviare un ticket di supporto?

R. [I ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) possono essere inviati tramite il Portale di Azure.

## <a name="sql-languagefeature-support"></a>Supporto per le funzionalità/linguaggio SQL 

Q. Quali tipi di dati sono supportati?

R. Vedere [tipi di dati](sql-data-warehouse-tables-data-types.md).

Q. Quali funzionalità delle tabelle sono supportate?

R. Sono supportate molte funzionalità. Le funzionalità non supportate sono disponibili in [Funzionalità tabella non supportate](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Strumenti e amministrazione

Q. Il pool Synapse SQL supporta le API REST?

R. Sì. La maggior parte delle funzionalità REST che possono essere usate con il database SQL è disponibile anche con il pool SQL Synapse. È possibile trovare informazioni sulle API nelle pagine di documentazione REST o in [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx).

## <a name="loading"></a>Caricamento

Q. Quali driver client sono supportati?

R. Il supporto dei driver per il pool Synapse SQL è disponibile nella pagina [Stringhe di connessione](sql-data-warehouse-connection-strings.md)

D: Quali formati di file sono supportati da PolyBase?

R: Orc, RC, Parquet e testo delimitato semplice

D: A quali origini dati è possibile connettersi tramite PolyBase? 

R: [Archiviazione di Azure Data Lake](sql-data-warehouse-load-from-azure-data-lake-store.md) e BLOB di Archiviazione di [Azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

D: È possibile eseguire il pushdown del calcolo quando ci si connette a BLOB di Archiviazione di Azure o ADLS? 

R: No, PolyBase interagisce solo con i componenti di archiviazione. 

D: È possibile connettersi ad HDI?

R: HDI può usare ADLS o WASB come livello HDFS. Se si dispone di un livello HDFS, è possibile caricare i dati in un pool SQL Synapse. Tuttavia, non è possibile generare il calcolo di distribuzione per l'istanza HDI. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Synapse nel suo complesso, vedere la pagina [Panoramica.For](sql-data-warehouse-overview-faq.md) more information on Azure Synapse as a whole, see our Overview page.
