---
title: Panoramica di Microsoft Azure Data Lake Analytics | Documentazione Microsoft
description: Data Lake Analytics consente di gestire le attività aziendali usando le informazioni dettagliate ricavate dai dati archiviati nel cloud a qualsiasi livello.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: a788c7ad6eca0abea6f6ab400cbe2d0e5914eed8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-azure-data-lake-analytics"></a>Panoramica di Azure Data Lake Analytics

Azure Data Lake Analytics è un servizio per processi di analisi su richiesta che semplifica l'uso dei Big Data. Anziché distribuire, configurare e ottimizzare l'hardware, è possibile scrivere query per trasformare i dati ed estrarre informazioni di interesse. Con il servizio di analisi è possibile gestire processi di qualsiasi dimensione immediatamente definendo il livello e l'ambito necessari. Verrà addebitato un costo per il processo solo durante la sua esecuzione, per la massima convenienza. Il servizio di analisi include U-SQL, un linguaggio che unisce i vantaggi di SQL alla potenza del codice imperativo. U-SQL consente di analizzare i dati tra Data Lake Store, SQL Server in Azure, il database SQL di Azure e Azure SQL Data Warehouse.

### <a name="dynamic-scaling"></a>Scalabilità dinamica
  
Data Lake Analytics effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte o addirittura petabyte di dati. Si paga solo la potenza di elaborazione usata. Se si aumentano o si riducono le dimensioni dei dati archiviati o la quantità di risorse di calcolo usata, non è necessario riscrivere il codice. 

### <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Strumenti familiari per sviluppo più rapido, debug e ottimizzazione avanzata
  
Data Lake Analytics è perfettamente integrato con Visual Studio. È possibile usare strumenti familiari per l'esecuzione, il debug e l'ottimizzazione del codice. Le visualizzazioni dei processi U-SQL permettono di definire la scalabilità con cui viene eseguito il codice, per poter identificare i colli di bottiglia in termini di prestazioni e ridurre i costi.

### <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: semplice, familiare, potente ed estendibile
  
Data Lake Analytics include U-SQL, un linguaggio di query che estende la natura dichiarativa, semplice e familiare di SQL con l'efficacia espressiva di C#. Il linguaggio U-SQL usa lo stesso runtime distribuito su cui si basa il data lake interno a livello di esabyte di Microsoft. Gli sviluppatori SQL e .NET possono ora elaborare e analizzare i propri dati sfruttando le competenze già acquisite.

### <a name="integrates-seamlessly-with-your-it-investments"></a>Integrazione uniforme con gli investimenti IT
  
Data Lake Analytics sfrutta gli investimenti IT esistenti per identità, gestione, e sicurezza. Questo approccio semplifica la governance dei dati e l'estensione delle attuali applicazioni dati. Data Lake Analytics è integrato in Active Directory per la gestione e le autorizzazioni degli utenti e viene fornito con funzionalità integrate di monitoraggio e controllo.

### <a name="affordable-and-cost-effective"></a>Conveniente ed economico

Data Lake Analytics è una soluzione a costi ridotti per l'esecuzione di carichi di lavoro di Big Data. Il pagamento avviene in base a ogni processo quando vengono elaborati i dati. Non sono necessari hardware, licenze o contratti di supporto specifici del servizio. Poiché il sistema è in grado di ridimensionarsi automaticamente all'avvio e al completamento del processo, non si paga mai più del necessario. [Altre informazioni sul controllo dei costi e il risparmio](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c).
    
### <a name="works-with-all-your-azure-data"></a>Per tutti i dati di Azure
  
Data Lake Analytics interagisce con Azure Data Lake Store per fornire il massimo livello di prestazioni, velocità effettiva e parallelizzazione. Interagisce anche con i BLOB di Archiviazione di Azure, database SQL di Azure e Azure SQL Data Warehouse.

### <a name="next-steps"></a>Passaggi successivi
 
  * Introduzione ad Azure Data Lake Analytics con [portale di Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Interfaccia della riga di comando](data-lake-analytics-get-started-cli2.md)
  * Gestire Azure Data Lake Analytics con [portale di Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [interfaccia della riga di comando](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [Come controllare i costi e risparmiare denaro con Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
