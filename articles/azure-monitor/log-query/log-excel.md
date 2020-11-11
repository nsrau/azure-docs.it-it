---
title: Integrare Log Analytics ed Excel
description: Ottenere una query Log Analytics in Excel e aggiornare i risultati all'interno di Excel.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507525"
---
# <a name="integrate-log-analytics-and-excel"></a>Integrare Log Analytics ed Excel

È possibile integrare monitoraggio di Azure Log Analytics e Microsoft Excel usando la query M e l'API Log Analytics.  Questa integrazione consente di inviare record 500.000 a Excel.

> [!NOTE]
> Poiché Excel è un'applicazione client locale, le limitazioni hardware e software locali influiscano sulle prestazioni e sulla capacità di elaborare set di dati di grandi dimensioni.

## <a name="create-your-m-query-in-log-analytics"></a>Creare la query M in Log Analytics 

1. **Creare ed eseguire la query** in log Analytics normalmente. Non preoccuparsi se si raggiunge il limite di 10.000 record nell'interfaccia utente.  Si consiglia di usare date relative, ad esempio la funzione ' ago ' o la selezione ora dell'interfaccia utente. in questo modo Excel aggiorna il set di dati corretto.
  
2. **Esporta Query** : quando si è soddisfatti della query e dei relativi risultati, esportare la query in M usando log Analytics scegliere **Esporta in Power BI (query m)** dal menu *Esporta* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics query con l'opzione di esportazione e i dati" border="true":::



La scelta di questa opzione consente di scaricare un file con estensione txt contenente il codice M che è possibile utilizzare in Excel.

La query illustrata sopra Esporta il codice M seguente. Di seguito è riportato un esempio del codice M esportato per la query nell'esempio:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Connetti query a Excel 

Per importare la query. 

1. Aprire Microsoft Excel. 
1. Nella barra multifunzione andare al menu **dati** . Selezionare **recuperare i dati**. Da **altre origini** selezionare **query vuota** :
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Importa da blank nell'opzione Excel" border="true":::

1. Nella finestra di Power query selezionare **Editor avanzato** :

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Editor avanzato query di Excel" border="true":::

 
1. Sostituire il testo nell'editor avanzato con la query esportata da Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Creazione di una query vuota" border="true":::
 
1. Selezionare **fine** , quindi **carica e Chiudi**. Excel esegue la query usando l'API di log Analytics e il set di risultati visualizzato.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Risultati delle query in Excel" border="true":::

##  <a name="refreshing--data"></a>Aggiornamento dei dati

È possibile aggiornare i dati direttamente da Excel. Nel gruppo di menu **dati** della barra multifunzione di Excel selezionare il pulsante **Aggiorna** .
 
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle integrazioni di Excel con origini dati esterne, vedere [importare dati da origini dati esterne (Power query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)