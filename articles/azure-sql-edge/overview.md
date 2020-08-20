---
title: Che cos'è SQL Edge di Azure (anteprima)?
description: Informazioni su SQL Edge di Azure (anteprima)
keywords: introduzione a SQL Edge, informazioni su SQL Edge, panoramica di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2c96e4b7baa2c463c42db9440cadb3cb396fde1b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642470"
---
# <a name="what-is-azure-sql-edge-preview"></a>Che cos'è SQL Edge di Azure (anteprima)?

SQL Edge di Azure (anteprima) è un motore di database relazionale ottimizzato e progettato per distribuzioni IoT e IoT Edge. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati ad alte prestazioni per le applicazioni e le soluzioni IoT. SQL Edge di Azure offre funzionalità per lo streaming, l'elaborazione e l'analisi di dati relazionali e non relazionali, ad esempio JSON, grafi e serie temporali, che lo rendono la scelta ideale per un'ampia gamma di applicazioni IoT moderne.

Azure SQL Edge è basato sulle versioni più recenti di Microsoft SQL motore di database (/SQL/SQL-Server/SQL-Server-Technical-Documentation? TOC =/Azure/Azure-SQL-Edge/toc.json), che offre funzionalità leader del settore per le prestazioni, la sicurezza e l'elaborazione delle query. Poiché Azure SQL Edge si basa sullo stesso motore di [SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/azure-sql-edge/toc.json) e di [Azure SQL](https://docs.microsoft.com/azure/azure-sql/), fornisce la stessa area della piattaforma di programmazione T-SQL che rende più semplice e veloce lo sviluppo di applicazioni o soluzioni, e allo stesso tempo rende la portabilità delle applicazioni tra i dispositivi IOT Edge, i Data Center e il cloud in modo semplice.

> [!NOTE]
> SQL Edge di Azure è attualmente in anteprima e pertanto NON deve essere usato in ambienti di produzione.

## <a name="deployment-models"></a>Modelli di distribuzione

SQL Edge di Azure è disponibile in Azure Marketplace e può essere distribuito come modulo per [Azure IoT Edge](../iot-edge/about-iot-edge.md). Per altre informazioni, vedere [Distribuire SQL Edge di Azure](deploy-portal.md).<br>

![Diagramma della panoramica di SQL Edge](media/overview/overview.png)

## <a name="editions-of-sql-edge"></a>Edizioni di SQL Edge

SQL Edge è disponibile con due edizioni o piani software diversi. Queste edizioni hanno set di funzionalità identici e si differenziano solo in termini di diritti di utilizzo e per la quantità di CPU/memoria supportata.

   |**Pianificare**  |**Descrizione**  |
   |---------|---------|
   |SQL Edge di Azure Developer  |  SKU solo per lo sviluppo, ogni contenitore SQL Edge è limitato a 4 core e 32 GB di memoria  |
   |SQL Edge di Azure    |  SKU di produzione, ogni contenitore SQL Edge è limitato a 8 core e 64 GB di memoria. |

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

SQL Edge di Azure è attualmente in anteprima. Per altre informazioni sui prezzi e sulla disponibilità, vedere [SQL Edge di Azure](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Per informazioni sulle differenze tra le funzionalità di SQL Edge di Azure e SQL Server, nonché sulle differenze tra le diverse opzioni di SQL Edge di Azure, vedere [Funzionalità supportate di SQL Edge di Azure](features.md).

## <a name="streaming-capabilities"></a>Funzionalità di streaming  

SQL Edge di Azure offre funzionalità di streaming predefinite per l'analisi in tempo reale e l'elaborazione di eventi complessi. La funzionalità di streaming è realizzata con gli stessi costrutti di [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e funzionalità simili ad [Analisi di flusso di Azure in IoT Edge](../stream-analytics/stream-analytics-edge.md).

Il motore di streaming per SQL Edge di Azure è progettato per offrire bassa latenza, resilienza, uso efficiente della larghezza di banda e conformità. 

Per altre informazioni sullo streaming dei dati in SQL Edge, vedere [Streaming dei dati](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Funzionalità di Machine Learning e intelligenza artificiale

SQL Edge di Azure offre funzionalità di Machine Learning e analisi grazie all'integrazione del runtime ONNX (Open Neural Network Exchange) in formato aperto, che consente lo scambio di modelli di Deep Learning e di rete neurale tra framework diversi. Per altre informazioni su ONNX, vedere [questa pagina](https://onnx.ai/). Il runtime di ONNX offre la flessibilità necessaria per sviluppare modelli con il linguaggio o gli strumenti prescelti, che possono essere convertiti nel formato ONNX per l'esecuzione all'interno di SQL Edge. Per altre informazioni, vedere [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Uso di SQL Edge di Azure

SQL Edge di Azure consente di sviluppare e gestire le applicazioni in modo più facile e produttivo. Gli utenti possono usare tutti gli strumenti e le competenze già familiari per creare app e soluzioni di alto livello per le proprie esigenze nel contesto IoT Edge. Per lo sviluppo in SQL Edge è possibile usare strumenti come i seguenti:

- [Portale di Azure](https://portal.azure.com/) - Applicazione Web per la gestione di tutti i servizi di Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Applicazione client gratuita e scaricabile per la gestione di qualsiasi infrastruttura SQL, da SQL Server al database SQL.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) - Applicazione client gratuita e scaricabile per lo sviluppo di database relazionali di SQL Server, database SQL, pacchetti di Integration Services, modelli di dati di Analysis Services e report di Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uno strumento di database multipiattaforma scaricabile e gratuito per professionisti della gestione di dati che usano la famiglia Microsoft di piattaforme dati locali e cloud in Windows, macOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Editor di codice gratuito, scaricabile, open source per Windows, macOS e Linux. Supporta estensioni, tra cui l'[estensione mssql](https://aka.ms/mssql-marketplace) per l'esecuzione di query in Microsoft SQL Server, database SQL di Azure e Azure SQL Data Warehouse.


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md)
- [Machine Learning e intelligenza artificiale con SQL Edge](onnx-overview.md)
- [Creazione di una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
