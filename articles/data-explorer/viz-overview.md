---
title: Visualizzazione dei dati di Azure Data ExplorerAzure Data Explorer data visualization
description: Informazioni sui diversi modi in cui è possibile visualizzare i dati di Azure Data ExplorerLearn about the different ways you can visualize your Azure Data Explorer data
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139063"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Data visualization with Azure Data Explorer 

Azure Data Explorer è un servizio di esplorazione dati veloce e altamente scalabile per i dati di log e telemetria usato per creare soluzioni di analisi complesse per grandi quantità di dati. Azure Data Explorer si integra con vari strumenti di visualizzazione, in modo da poter visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Questi dati possono essere trasformati in informazioni utili per avere un impatto sul tuo business.

La visualizzazione e la creazione di report dei dati è un passaggio fondamentale del processo di analisi dei dati. Azure Data Explorer supporta molti servizi di business intelligence in modo da poter usare quello più adatto allo scenario e al budget.

## <a name="kusto-query-language-visualizations"></a>Visualizzazioni del linguaggio di query Kusto

Il linguaggio [`render operator`](/azure/kusto/query/renderoperator) di query Kusto offre varie visualizzazioni, ad esempio tabelle, grafici a torta e grafici a barre, per rappresentare i risultati delle query. Le visualizzazioni di query sono utili per il rilevamento e la previsione delle anomalie, l'apprendimento automatico e altro ancora.

## <a name="power-bi"></a>Power BI

Azure Data Explorer offre la possibilità di connettersi a Power BI usando vari metodi:Azure Data Explorer provides the capability to connect to [Power BI](https://powerbi.microsoft.com) using various methods: 

  * [Connettore Power BI nativo incorporato](/azure/data-explorer/power-bi-connector)

  * [Query import from Azure Data Explorer into Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Query SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer offre la possibilità di connettersi a Microsoft Excel usando il connettore nativo incorporato di Excel o di importare una query da Esplora dati di Azure in Excel.Azure Data Explorer provides the capability to connect to [Microsoft Excel](https://products.office.com/excel) using the [built-in native Excel connector,](excel-connector.md)or [import a query](excel-blank-query.md) from Azure Data Explorer into Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) offre un plug-in di Azure Data Explorer che consente di visualizzare i dati da Azure Data Explorer.Grafana provides an Azure Data Explorer plugin that enables you to visualize data from Azure Data Explorer. Impostare [Azure Data Explorer come origine dati per Grafana e quindi visualizzare i dati](/azure/data-explorer/grafana). 

## <a name="kibana"></a>Kibana

Azure Data Explorer offre la possibilità di connettersi a [Kibana (la pagina Scopri)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) usando K2Bridge, un connettore open source. Impostare [Azure Data Explorer come origine dati per Kibana e quindi visualizzare i dati](/azure/data-explorer/k2bridge).

## <a name="odbc-connector"></a>Connettore ODBC

Azure Data Explorer offre un [connettore ODBC (Open Database Connectivity),](connect-odbc.md) in modo che qualsiasi applicazione che supporta ODBC possa connettersi ad Azure Data Explorer.Azure Data Explorer provides an Open Database Connectivity (ODBC) connector so any application that supports ODBC can connect to Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer offre la possibilità di connettersi a [Tableau](https://www.tableau.com) utilizzando il [connettore ODBC](/azure/data-explorer/connect-odbc) e quindi [visualizzare i dati in Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer offre la possibilità di connettersi a [Qlik](https://www.qlik.com) usando il [connettore ODBC,](/azure/data-explorer/connect-odbc) quindi creare dashboard Qlik Sense e visualizzare i dati. Usando il video seguente, è possibile imparare a visualizzare i dati di Azure Data Explorer con Qlik.Using the following video, you can learn to visualize Azure Data Explorer data with Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer offre la possibilità di connettersi a Sisense usando il connettore JDBC.Azure Data Explorer provides the capability to connect to [Sisense](https://www.sisense.com) using the JDBC connector. Impostare [Azure Data Explorer come origine dati per Sisense e quindi visualizzare i dati](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

È possibile utilizzare [Redash](https://redash.io/) per creare dashboard e visualizzare i dati. [Configurare Azure Data Explorer come origine dati per Redash e quindi visualizzare i dati.](/azure/data-explorer/redash)