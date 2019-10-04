---
title: Visualizzazione dei dati di Esplora dati Azure
description: Scopri i diversi modi, che è possibile visualizzare i dati di Esplora dati di Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536718"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualizzazione dei dati con Esplora dati di Azure 

Esplora dati di Azure è un servizio di esplorazione dei dati rapido e a scalabilità elevata per i dati di log e dati di telemetria che consente di creare soluzioni complesse analitica per grandi quantità di dati. Esplora i dati di Azure si integra con vari strumenti di visualizzazione, in modo da poter visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Questi dati possono essere trasformati in analisi approfondite per avere un impatto sull'azienda.

Visualizzazione dei dati e creazione di report è un passaggio critico del processo di analitica dei dati. Esplora i dati di Azure supporta molti servizi di BI in modo che è possibile usare quello che meglio si adatta lo scenario e budget.

## <a name="kusto-query-language-visualizations"></a>Visualizzazioni del linguaggio di query Kusto

Il linguaggio di query Kusto [ `render operator` ](/azure/kusto/query/renderoperator) offre visualizzazioni diverse, ad esempio tabelle, grafici a torta e istogrammi per rappresentare i risultati della query. Le visualizzazioni di query sono utili per il rilevamento delle anomalie e previsione, machine learning e altro ancora.

## <a name="power-bi"></a>Power BI

Esplora i dati di Azure offre la possibilità di connettersi al [Power BI](https://powerbi.microsoft.com) usando vari metodi: 

  * [Connettore di Power BI nativo incorporato](/azure/data-explorer/power-bi-connector)

  * [Importa query da Esplora dati di Azure in Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Esplora i dati di Azure offre la possibilità di connettersi al [Microsoft Excel](https://products.office.com/excel) usando l'oggetto nativo incorporato connettore Excel oppure importare una query da Esplora dati di Azure in Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) offre un plug-in Esplora dati di Azure che consente di visualizzare i dati da Esplora dati di Azure. Si [impostati Esplora dati di Azure come un'origine dati di Grafana e quindi visualizzare i dati](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Connettore ODBC

Esplora dati Azure offre un' [connettore Open Database Connectivity (ODBC)](connect-odbc.md) in modo che qualsiasi applicazione che supporti ODBC possa connettersi a Esplora dati di Azure.

## <a name="tableau"></a>Tableau

Esplora i dati di Azure offre la possibilità di connettersi al [Tableau](https://www.tableau.com) usando la [connettore ODBC](/azure/data-explorer/connect-odbc) e quindi [visualizzare i dati in Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Esplora i dati di Azure offre la possibilità di connettersi al [Qlik](https://www.qlik.com) usando la [connettore ODBC](/azure/data-explorer/connect-odbc) Qlik Sense possibile creare dashboard e visualizzare i dati. Usa il video seguente, è possibile imparare a visualizzare i dati di Esplora dati di Azure con Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Esplora i dati di Azure offre la possibilità di connettersi al [Sisense](https://www.sisense.com) usando il connettore JDBC. Si [configurare Esplora dati di Azure come origine dati per Sisense e quindi visualizzare i dati](/azure/data-explorer/sisense).