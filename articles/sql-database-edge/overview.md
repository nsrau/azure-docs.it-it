---
title: Informazioni sul perimetro del database SQL di Azure | Documentazione Microsoft
description: Informazioni sul perimetro del database SQL di Azure
keywords: Introduzione al database SQL Edge, che cos'è il database SQL Edge, panoramica sul perimetro del database SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514004"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Che cos'è l'anteprima di Edge del database SQL di Azure?

L'anteprima Edge del database SQL di Azure è un motore di database relazionale ottimizzato, progettato per le distribuzioni e IoT Edge. Fornisce funzionalità per la creazione di un livello di elaborazione e archiviazione dei dati a prestazioni elevate per le applicazioni e le soluzioni Internet. Edge del database SQL di Azure offre funzionalità per la trasmissione, l'elaborazione e l'analisi di dati relazionali e non relazionali, ad esempio JSON, grafici e dati di serie temporali, che lo rendono la scelta ideale per un'ampia gamma di moderne applicazioni Internet.

Il database SQL di Azure Edge si basa sulle versioni più recenti del [Microsoft SQL Server motore di database](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), che offre funzionalità leader nel settore delle prestazioni, della sicurezza e dell'elaborazione delle query. Poiché il database SQL di Azure Edge si basa sullo stesso motore di SQL Server e sul database SQL di Azure, fornisce la stessa area della piattaforma di programmazione T-SQL che rende più semplice e veloce lo sviluppo di applicazioni o soluzioni e allo stesso tempo rende l'applicazione portabilità tra dispositivi IoT Edge, Data Center e il cloud in diretta.

## <a name="deployment-models"></a>Modelli di distribuzione

Il database SQL di Azure Edge è disponibile in Azure Marketplace e può essere distribuito come modulo per [Azure IOT Edge](../iot-edge/about-iot-edge.md). Per altre informazioni, vedere [distribuire il database SQL di Azure Edge](deploy-portal.md).<br>

![Diagramma della panoramica del perimetro del database SQL](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edizioni del database perimetrale SQL

Il database SQL Edge è disponibile con tre diverse edizioni o piani software. Queste edizioni hanno set di funzionalità identici e si differenziano solo in termini di diritti di utilizzo e della quantità di CPU/memoria supportata.

   |**Pianificare**  |**Descrizione**  |
   |---------|---------|
   |Developer  |  SKU solo per lo sviluppo, con gli stessi limiti di limitazione imposti dallo SKU standard indicato di seguito |
   |Standard   |  Il piano standard supporta fino a 4 CPU e fino a 32 GB di memoria per il contenitore perimetrale del database SQL. |
   |Premium    |  Lo SKU Premium supporta fino a 8 core e fino a 64 GB di memoria per il contenitore perimetrale del database SQL. |

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

Il database SQL di Azure è attualmente in versione di anteprima. Per altre informazioni sui prezzi e sulla disponibilità, vedere [Edge del database SQL di Azure](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Per comprendere le differenze tra le funzionalità del database SQL di Azure Edge e SQL Server, nonché le differenze tra le diverse opzioni di Edge del database SQL di Azure, vedere [funzionalità del database perimetrale del database SQL](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Funzionalità di streaming  

Edge del database SQL di Azure offre funzionalità di streaming predefinite per l'analisi in tempo reale e l'elaborazione di eventi complessi. La funzionalità di streaming viene creata usando gli stessi costrutti di [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e fornisce funzionalità simili a [analisi di flusso di Azure in IOT Edge](../stream-analytics/stream-analytics-edge.md).

Il motore di flusso per il database SQL di Azure Edge è progettato per una bassa latenza, resilienza, uso efficiente della larghezza di banda e della conformità.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Funzionalità di Machine Learning e intelligenza artificiale

Edge database SQL di Azure offre funzionalità integrate di machine learning e analisi integrando il runtime ONNX (Open Neural Network Exchange), che consente lo scambio di modelli di Deep Learning e di rete neurale tra Framework diversi. Per ulteriori informazioni su ONNX, vedere [qui](https://onnx.ai/). Il runtime di ONNX offre la flessibilità necessaria per sviluppare modelli in un linguaggio o uno strumento di propria scelta, che può quindi essere convertito nel formato ONNX per l'esecuzione all'interno del database SQL Edge. Per altre informazioni, vedere [Machine Learning e intelligenza artificiale con ONNX nel database SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Uso del perimetro del database SQL di Azure

Edge del database SQL di Azure semplifica e rende più produttivo le applicazioni di sviluppo e gestione. Gli utenti possono usare tutti gli strumenti e le competenze comuni per creare app e soluzioni eccezionali per le proprie esigenze IoT Edge. L'utente può sviluppare nel database SQL Edge usando strumenti come i seguenti:

- [Portale di Azure](https://portal.azure.com/) un'applicazione basata sul Web per la gestione di tutti i servizi di Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) un'applicazione client gratuita e scaricabile per la gestione di qualsiasi infrastruttura SQL, da SQL Server a database SQL.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) : un'applicazione client gratuita e scaricabile per lo sviluppo di database relazionali SQL Server, database SQL, pacchetti Integration Services, modelli di dati Analysis Services e report Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) : uno strumento di database multipiattaforma gratuito e scaricabile per data Professional con la famiglia Microsoft di piattaforme dati locali e cloud in Windows, MacOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) : un editor di codice open source gratuito e scaricabile per Windows, MacOS e Linux. Supporta le estensioni, tra cui l' [estensione MSSQL](https://aka.ms/mssql-marketplace) per l'esecuzione di query Microsoft SQL Server, il database SQL di Azure e Azure SQL data warehouse.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui prezzi e sulla disponibilità, vedere [Edge del database SQL di Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Richiedere l'abilitazione del perimetro del database SQL di Azure per la sottoscrizione.
- Per iniziare, vedere gli argomenti seguenti:
  - [Distribuire Edge del database SQL tramite portale di Azure](deploy-portal.md)
  - [Machine Learning e intelligenza artificiale con Edge database SQL](onnx-overview.md)
