---
title: Che cos'è Azure SQL Database Edge? | Microsoft Docs
description: Informazioni su Azure SQL Database Edge
keywords: introduzione al perimetro del database sql, che cos'è sql database edge, panoramica sul perimetro del database sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246705"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Che cos'è Azure SQL Database Edge Preview?

Azure SQL Database Edge Preview è un motore di database relazionale ottimizzato destinato alle distribuzioni IoT e IoT Edge.Azure SQL Database Edge Preview is an optimized relational database engine is for IoT and IoT Edge deployments. Fornisce funzionalità per creare un livello di elaborazione e archiviazione dati ad alte prestazioni per le applicazioni e le soluzioni IoT.Provides capabilities to create a high-performance data storage and processing layer for IoT applications and solutions. Azure SQL Database Edge offre funzionalità per lo streaming, l'elaborazione e l'analisi relazionale e non relazionale, ad esempio dati JSON, grafici e serie temporali, che lo rendono la scelta giusta per un'ampia gamma di applicazioni IoT moderne.

Azure SQL Database Edge si basa sulle versioni più recenti del Motore di database di [Microsoft SQL Server Database Engine,](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)che offre funzionalità di elaborazione di query, sicurezza e funzionalità di elaborazione di query leader del settore. Poiché, Azure SQL Database Edge si basa sullo stesso motore di SQL Server e del database SQL di Azure, fornisce la stessa area di programmazione T-SQL che semplifica e velocizza lo sviluppo di applicazioni o soluzioni e allo stesso tempo rende l'applicazione portabilità tra dispositivi IoT Edge, data center e il cloud in avanti.

## <a name="deployment-models"></a>Modelli di distribuzione

Azure SQL Database Edge è disponibile in Azure Marketplace e può essere distribuito come modulo per [Azure IoT Edge.](../iot-edge/about-iot-edge.md) Per altre informazioni, vedere [Distribuire Azure SQL Database Edge.](deploy-portal.md)<br>

![Diagramma di panoramica di SQL Database Edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edizioni di SQL Database Edge

SQL Database Edge è disponibile con tre diverse edizioni o piani software. Queste edizioni hanno set di funzionalità identici e differiscono solo in termini di diritti di utilizzo e la quantità di cpu/memoria che supportano.

   |**Piano**  |**Descrizione**  |
   |---------|---------|
   |Sviluppatore di Edge del database SQL di AzureAzure SQL Database Edge Developer  |  Solo sku di sviluppo, ogni contenitore di SQL Database Edge è limitato a un massimo di 4 core e 32 GB di memoria  |
   |Database SQL Edge di Azure    |  Sku di produzione, ogni contenitore di SQL Database Edge è limitato a un massimo di 8 core e 64 GB di memoria. |

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Il database SQL di Azure è attualmente in anteprima. Per altre informazioni sui prezzi e sulla disponibilità, vedere [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Per comprendere le differenze di funzionalità tra Edge del database SQL di Azure e SQL Server, nonché le differenze tra le diverse opzioni di Edge del database SQL di Azure, vedere Funzionalità del [database perimetrale del database SQL.](https://azure.microsoft.com/services/sql-database-edge/)

## <a name="streaming-capabilities"></a>Funzionalità di streaming  

Azure SQL Database Edge offre funzionalità di streaming integrate per l'analisi in tempo reale e l'elaborazione di eventi complessi. La funzionalità di streaming viene compilata usando gli stessi costrutti di [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e offre funzionalità simili a quelle di Analisi di flusso di Azure in [IoT Edge.](../stream-analytics/stream-analytics-edge.md)

Il motore di streaming per Azure SQL Database Edge è progettato per un utilizzo efficiente della larghezza di banda e della conformità a bassa latenza, resilienza ed efficienti.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Funzionalità di Machine Learning e Intelligenza Artificiale

Azure SQL Database Edge offre funzionalità di analisi e apprendimento automatico integrate integrando il runtime OPEN format ONNX (Open Neural Network Exchange), che consente lo scambio di modelli di rete neurale e di deep learning tra framework diversi. Per ulteriori informazioni su ONNX, vedere [qui](https://onnx.ai/). Il runtime ONNX offre la flessibilità necessaria per sviluppare modelli in un linguaggio di propria scelta, che può quindi essere convertito nel formato ONNX per l'esecuzione all'interno di SQL Database Edge. Per ulteriori informazioni, vedere [Machine Learning e Intelligenza artificiale con ONNX in SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Utilizzo di Azure SQL Database Edge

Azure SQL Database Edge semplifica e semplifica lo sviluppo e la manutenzione delle applicazioni. Gli utenti possono utilizzare tutti gli strumenti e le competenze familiari per creare app e soluzioni eccezionali per le loro esigenze di IoT Edge. L'utente può sviluppare in SQL Database Edge utilizzando strumenti come i seguenti:

- [Portale di Azure:](https://portal.azure.com/) un'applicazione basata sul Web per la gestione di tutti i servizi di Azure.The Azure portal - A web-based application for managing all Azure services.
- [SQL Server Management StudioSQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) : applicazione client gratuita e scaricabile per la gestione di qualsiasi infrastruttura SQL, da SQL Server sql a Database SQL.
- [SQL Server Data Tools in Visual Studio:](/sql/ssdt/download-sql-server-data-tools-ssdt/) applicazione client scaricabile gratuita per lo sviluppo di database relazionali di SQL Server, database SQL, pacchetti di Integration ServicesIntegration Services, modelli di dati di Analysis Services e report di Reporting ServicesReporting Services .
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uno strumento di database multipiattaforma gratuito, scaricabile e per i professionisti dei dati che utilizzano la famiglia Microsoft di piattaforme dati locali e cloud su Windows, MacOS e Linux.
- Codice di [Visual Studio:](https://code.visualstudio.com/docs) editor di codice open source gratuito, scaricabile per Windows, macOS e Linux.Visual Studio Code - A free, downloadable, open-source code editor for Windows, macOS, and Linux. Supporta le estensioni, tra cui [l'estensione mssql per l'esecuzione](https://aka.ms/mssql-marketplace) di query su Microsoft SQL Server, il database SQL di Azure e Azure SQL Data Warehouse.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sui prezzi e sulla disponibilità, vedere [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Richiedere di abilitare Azure SQL Database Edge per la sottoscrizione.
- Per iniziare, vedere quanto segue:
  - [Distribuire Edge del database SQL tramite il portale di AzureDeploy SQL Database Edge through Azure portal](deploy-portal.md)
  - [Machine Learning e Intelligenza Artificiale con SQL Database Edge](onnx-overview.md)
