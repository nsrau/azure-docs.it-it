---
title: Visualizzazione dei dati di Azure Esplora dati
description: Informazioni sui diversi modi in cui è possibile visualizzare i dati di Azure Esplora dati
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139063"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualizzazione dei dati con Esplora dati di Azure 

Azure Esplora dati è un servizio di esplorazione dei dati veloce ed estremamente scalabile per i dati di log e di telemetria usati per creare soluzioni di analisi complesse per grandi quantità di dati. Azure Esplora dati si integra con diversi strumenti di visualizzazione, in modo che sia possibile visualizzare i dati e condividere i risultati nell'organizzazione. Questi dati possono essere trasformati in informazioni dettagliate di utilità pratica per avere un effetto sull'azienda.

La visualizzazione dei dati e la creazione di report sono un passaggio fondamentale del processo di analisi dei dati. Azure Esplora dati supporta molti servizi di Business Intelligence per poter usare quello più adatto allo scenario e al budget.

## <a name="kusto-query-language-visualizations"></a>Visualizzazioni del linguaggio di query kusto

Il linguaggio di query kusto [`render operator`](/azure/kusto/query/renderoperator) offre diverse visualizzazioni, ad esempio tabelle, grafici a torta e grafici a barre, per rappresentare i risultati della query. Le visualizzazioni di query sono utili per il rilevamento e la previsione di anomalie, l'apprendimento automatico e altro ancora.

## <a name="power-bi"></a>Power BI

Azure Esplora dati offre la possibilità di connettersi ai [Power bi](https://powerbi.microsoft.com) con diversi metodi: 

  * [Connettore Power BI nativo incorporato](/azure/data-explorer/power-bi-connector)

  * [Eseguire query sull'importazione da Azure Esplora dati in Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Esplora dati offre la possibilità di connettersi a [Microsoft Excel](https://products.office.com/excel) usando il [connettore nativo Excel incorporato](excel-connector.md)o di [importare una query](excel-blank-query.md) da Azure Esplora dati in Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) fornisce un plug-in di Azure Esplora dati che consente di visualizzare i dati da Esplora dati di Azure. [Configurare Esplora dati di Azure come origine dati per Grafana e quindi visualizzare i dati](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

Azure Esplora dati offre la possibilità di connettersi a [Kibana (pagina Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) usando K2Bridge, un connettore open source. [Configurare Esplora dati di Azure come origine dati per Kibana e quindi visualizzare i dati](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Connettore ODBC

Azure Esplora dati fornisce un [connettore Open Database Connectivity (ODBC)](connect-odbc.md) , in modo che tutte le applicazioni che supportano ODBC possano connettersi ad Azure Esplora dati.

## <a name="tableau"></a>Tableau

Azure Esplora dati offre la possibilità di connettersi a [Tableau](https://www.tableau.com) usando il [connettore ODBC](/azure/data-explorer/connect-odbc) e quindi di [visualizzare i dati in Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Esplora dati offre la possibilità di connettersi a [Qlik](https://www.qlik.com) usando [ODBC Connector](/azure/data-explorer/connect-odbc) e quindi di creare dashboard Qlik Sense e visualizzare i dati. Usando il video seguente, è possibile imparare a visualizzare i dati di Azure Esplora dati con Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Esplora dati offre la possibilità di connettersi a [SiSense](https://www.sisense.com) tramite il connettore JDBC. [Configurare Esplora dati di Azure come origine dati per SiSense e quindi visualizzare i dati](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

È possibile usare [redash](https://redash.io/) per creare dashboard e visualizzare i dati. [Configurare Esplora dati di Azure come origine dati per il Ritrattino e quindi visualizzare i dati](/azure/data-explorer/redash).