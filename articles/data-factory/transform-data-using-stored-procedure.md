---
title: "Trasformare dati tramite l'attività stored procedure in Azure Data Factory | Microsoft Docs"
description: "Illustra l'uso dell'attività stored procedure di SQL Server per richiamare una stored procedure in un database SQL di Azure o in Data Warehouse da una pipeline di Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 6b4523747b57ee7a3d48211c9bb7fba1123fe4ce
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività stored procedure di SQL Server in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-stored-proc-activity.md)
> * [Versione 2 - Anteprima](transform-data-using-stored-procedure.md)


Le attività di trasformazione dei dati in una [pipeline](concepts-pipelines-activities.md) di Data factory trasformano ed elaborano i dati non elaborati in stime e approfondimenti. L'attività di stored procedure è una delle attività di trasformazione supportate da Data factory. Questo articolo si basa sull'articolo relativo alla [trasformazione dei dati](transform-data.md), che offre una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate in Data Factory.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività stored procedure nella versione 1](v1/data-factory-stored-proc-activity.md).
> 
> Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati). 

È possibile usare l'attività stored procedure per richiamare una stored procedure in uno dei seguenti archivi dati presenti in azienda o in una macchina virtuale di Azure: 

- database SQL di Azure
- Azure SQL Data Warehouse
- Database di SQL Server.  Se si usa SQL Server, è necessario installare Runtime di integrazione (self-hosted) nello stesso computer che ospita il database o in un computer separato che ha accesso al database. Runtime di integrazione (self-hosted) è un componente che connette in modo sicuro e gestito origini dati presenti in locale o in macchine virtuali di Azure ai servizi cloud. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

> [!IMPORTANT]
> Quando si copiano dati in SQL Server o nel Database SQL di Azure, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure tramite la proprietà **sqlWriterStoredProcedureName**. Per informazioni dettagliate sulla proprietà, vedere gli articoli connettore seguenti: [Database SQL di Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Non è possibile richiamare una stored procedure durante la copia dei dati in un Azure SQL Data Warehouse tramite un'attività di copia. Tuttavia, è possibile usare l'attività di stored procedure per richiamare una stored procedure in un SQL Data Warehouse. 
>
> Quando si copiano dati in SQL Server, nel Database SQL di Azure o in Azure SQL Data Warehouse, è possibile configurare **SqlSink** nell'attività di copia per richiamare una stored procedure che consenta di leggere i dati dal database di origine tramite la proprietà **sqlReaderStoredProcedureName**. Per altre informazioni, vedere gli articoli connettore seguenti: [Database SQL di Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Dettagli sintassi
Di seguito è riportato il formato JSON per la definizione di un'attività di Stored Procedure:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

La tabella seguente illustra queste proprietà JSON:

| Proprietà                  | DESCRIZIONE                              | Obbligatoria |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nome dell'attività                     | Sì      |
| description               | Testo descrittivo per lo scopo dell'attività | No        |
| type                      | Per l'attività stored procedure, il tipo di attività corrisponde a **SqlServerStoredProcedure** | Sì      |
| linkedServiceName         | Riferimento al **database SQL di Azure**, ad **Azure SQL Data Warehouse** o a **SQL Server** registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| storedProcedureName       | Specificare il nome della stored procedure da richiamare. | Sì      |
| storedProcedureParameters | Specificare i valori dei parametri della stored procedure. Usare `"param1": { "value": "param1Value","type":"param1Type" }` per passare i valori dei parametri e i tipi nativi corrispondenti supportati dall'origine dati. Se per un parametro è necessario passare Null, usare `"param1": { "value": null }` (tutte lettere minuscole). | No        |

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività di Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
