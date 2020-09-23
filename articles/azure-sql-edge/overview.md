---
title: Che cos'è Azure SQL Edge?
description: Informazioni su Azure SQL Edge
keywords: introduzione a SQL Edge, informazioni su SQL Edge, panoramica di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e34cd0907320f96f2846c1f424e678555381cccc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907123"
---
# <a name="what-is-azure-sql-edge"></a>Che cos'è Azure SQL Edge?

Azure SQL Edge è un motore di database relazionale ottimizzato, incentrato per le distribuzioni di Internet e IoT Edge. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati ad alte prestazioni per le applicazioni e le soluzioni IoT. SQL Edge di Azure offre funzionalità per lo streaming, l'elaborazione e l'analisi di dati relazionali e non relazionali, ad esempio JSON, grafi e serie temporali, che lo rendono la scelta ideale per un'ampia gamma di applicazioni IoT moderne.

Azure SQL Edge è basato sulle versioni più recenti del [SQL Server motore di database](/sql/sql-server/sql-server-technical-documentation), che offre funzionalità leader del settore per le prestazioni, la sicurezza e l'elaborazione delle query. Poiché Azure SQL Edge si basa sullo stesso motore di [SQL Server](/sql/sql-server/sql-server-technical-documentation) e [Azure SQL](https://docs.microsoft.com/azure/azure-sql/), fornisce la stessa area di progettazione Transact-SQL (T-SQL) che rende più semplice e veloce lo sviluppo di applicazioni o soluzioni e rende la portabilità delle applicazioni tra i dispositivi IOT Edge, i Data Center e il cloud in modo semplice.

Che cos'è il video di Azure SQL Edge su Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Modelli di distribuzione

Azure SQL Edge supporta due modalità di distribuzione.

- Distribuzione connessa tramite Azure IoT Edge: Azure SQL Edge è disponibile in Azure Marketplace e può essere distribuito come modulo per [Azure IOT Edge](../iot-edge/about-iot-edge.md). Per altre informazioni, vedere [Distribuire SQL Edge di Azure](deploy-portal.md).<br>

![Diagramma della panoramica di SQL Edge](media/overview/overview.png)

- Distribuzione disconnessa: le immagini del contenitore Edge di Azure SQL possono essere estratte dall'hub Docker e distribuite come contenitore Docker autonomo o in un cluster kubernetes. Per altre informazioni, vedere [distribuire Azure SQL Edge con Docker](disconnected-deployment.md) e [distribuire un contenitore Edge di Azure SQL in Kubernetes](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Edizioni di SQL Edge

SQL Edge è disponibile con due edizioni o piani software diversi. Queste edizioni hanno set di funzionalità identici e si differenziano solo in termini di diritti di utilizzo e per la quantità di CPU/memoria supportata.

   |**Pianificare**  |**Descrizione**  |
   |---------|---------|
   |SQL Edge di Azure Developer  |  SKU solo per lo sviluppo, ogni contenitore SQL Edge è limitato a 4 core e 32 GB di memoria  |
   |SQL Edge di Azure    |  SKU di produzione, ogni contenitore SQL Edge è limitato a fino a 8 core e 64 GB di memoria. |

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Azure SQL Edge è ora disponibile a livello generale. Per altre informazioni sui prezzi e sulla disponibilità in aree specifiche, vedere [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

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
- [Visual Studio Code](https://code.visualstudio.com/docs) - Editor di codice gratuito, scaricabile, open source per Windows, macOS e Linux. Supporta le estensioni, tra cui l' [estensione MSSQL](https://aka.ms/mssql-marketplace) per l'esecuzione di query Microsoft SQL Server, il database SQL di Azure e l'analisi delle sinapsi di Azure.


## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge tramite il portale di Azure](deploy-portal.md)
- [Machine Learning e intelligenza artificiale con SQL Edge](onnx-overview.md)
- [Creazione di una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
