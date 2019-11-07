---
title: Richiama stored procedure da Azure Data Factory attività di copia
description: Informazioni su come chiamare una stored procedure nel Database SQL di Azure o in SQL Server da un'attività di copia di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0c5bb7ab4e8932c9568293620352435c7259d810
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682499"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Chiamare una stored procedure da un'attività di copia in Azure Data Factory
> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere [Trasformare dati tramite l'attività stored procedure in Data Factory](../transform-data-using-stored-procedure.md).


Quando si copiano dati in [SQL Server](data-factory-sqlserver-connector.md) o nel [Database SQL di Azure](data-factory-azure-sql-connector.md), è possibile configurare **SqlSink** nell'attività di copia per chiamare una stored procedure. Si consiglia di usare la stored procedure per eseguire eventuali elaborazioni aggiuntive (unione di colonne, ricerca di valori, inserimento in più tabelle e così via) è necessario prima inserire i dati nella tabella di destinazione. Questa funzionalità sfrutta [Table-Valued Parameters](https://msdn.microsoft.com/library/bb675163.aspx). 

L'esempio seguente illustra come chiamare una stored procedure in un database di SQL Server da una pipeline di data factory (attività di copia):  

## <a name="output-dataset-json"></a>Set di dati di output JSON
Nel set di dati di output JSON impostare **type** su **SqlServerTable**. Impostarlo su **AzureSqlTable** per usarlo con un database SQL di Azure. Il valore per la proprietà **tableName** deve corrispondere al nome del primo parametro della stored procedure.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Sezione SqlSink nel file JSON dell'attività di copia
Definire la sezione **SqlSink** nel file JSON dell'attività di copia come indicato di seguito. Per chiamare una stored procedure durante l'inserimento di dati nel sink o nel database di destinazione, specificare i valori per entrambe le proprietà **SqlWriterStoredProcedureName** e **SqlWriterTableType**. Per le descrizioni di queste proprietà, vedere [la sezione SqlSink dell'articolo sul connettore di SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definizione della stored procedure 
Nel database definire la stored procedure con lo stesso nome di **SqlWriterStoredProcedureName**. La stored procedure gestisce i dati di input dall'archivio dati di origine e inserisce i dati in una tabella nel database di destinazione. Il nome del primo parametro della stored procedure deve corrispondere al tableName definito nel set di dati JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definizione del tipo di tabella
Nel database definire il tipo di tabella con lo stesso nome di **SqlWriterTableType**. Lo schema del tipo di tabella deve corrispondere allo schema del set di dati di input.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passaggi successivi
Rivedere gli articoli seguenti sul connettore per gli esempi JSON completi: 

- [Database SQL di Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
