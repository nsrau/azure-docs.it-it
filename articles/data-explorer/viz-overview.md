---
title: Visualizzazione dei dati di Esplora dati Azure
description: Scopri i diversi modi, che è possibile visualizzare i dati di Esplora dati di Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481835"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualizzazione dei dati con Esplora dati di Azure 

Esplora dati di Azure è un servizio di esplorazione dei dati rapido e a scalabilità elevata per i dati di log e dati di telemetria che consente di creare soluzioni complesse analitica per grandi quantità di dati. Esplora i dati di Azure si integra con vari strumenti di visualizzazione, in modo da poter visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Questi dati possono essere trasformati in analisi approfondite per avere un impatto sull'azienda.

Visualizzazione dei dati e creazione di report è un passaggio critico del processo di analitica dei dati. Esplora i dati di Azure supporta molti servizi di BI in modo che è possibile usare quello che meglio si adatta lo scenario e budget.

* Visualizzazioni di Esplora dati Azure: Usando il linguaggio di query Kusto il [ `render operator` ](/azure/kusto/query/renderoperator) offre diversi tipi di visualizzazione per rappresentare i risultati della query. Le visualizzazioni di query sono utili per il rilevamento delle anomalie e previsione, machine learning e altro ancora.

* [Power BI](https://powerbi.microsoft.com): Esplora i dati di Azure offre la possibilità di connettersi a Power BI usando vari metodi: 

  * [Connettore di Power BI nativo incorporato](/azure/data-explorer/power-bi-connector)

  * [Importa query da Esplora dati di Azure in Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Query SQL](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Esplora i dati di Azure offre la possibilità di connettersi a Excel usando il connettore Excel nativo incorporato oppure importare una query da Esplora dati di Azure in Excel.

* [Grafana](https://grafana.com): Grafana fornisce un plug-in Esplora dati di Azure che consente di visualizzare i dati da Esplora dati di Azure. Si [configurare Esplora dati di Azure come origine dati per Grafana e quindi visualizzare i dati](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Esplora i dati di Azure offre la possibilità di connettersi a Sisense usando il connettore JDBC. Si [configurare Esplora dati di Azure come origine dati per Sisense e quindi visualizzare i dati](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Esplora i dati di Azure offre la possibilità di connettersi a Tableau usando il [connettore ODBC e visualizzare i dati in Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Esplora i dati di Azure offre la possibilità di connettersi a Qlik usando il [connettore ODBC](/azure/data-explorer/connect-odbc).