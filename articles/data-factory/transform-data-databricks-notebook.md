---
title: Trasformare i dati con i notebook di Databricks - Azure | Microsoft Docs
description: Informazioni su come elaborare o trasformare i dati eseguendo un notebook di Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: a011c31c5ccf70c379358f2b2c7748011b2fdd44
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Trasformare i dati eseguendo un notebook di Databricks

L'attività dei notebook di Azure Databricks in una [pipeline della data factory](concepts-pipelines-activities.md) esegue un notebook di Databricks nell'area di lavoro di Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate. Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definizione di attività dei notebook di Databricks

Ecco la definizione JSON di esempio di un'attività dei notebook di Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Proprietà dell'attività dei notebook di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|DESCRIZIONE|Obbligatoria|
|---|---|---|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No |
|type|Per l'attività dei notebook di Databricks il tipo di attività è DatabricksNotebook.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione il notebook di Databricks. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).|Sì|
|notebookPath|Percorso assoluto del notebook da eseguire nell'area di lavoro di Databricks. Questo percorso deve iniziare con una barra.|Sì|
|baseParameters|Matrice di coppie chiave-valore. I parametri base possono essere usati per ogni esecuzione attività. Se il notebook accetta un parametro non specificato, verrà usato il valore predefinito del notebook. Per altre informazioni sui parametri, vedere [Notebook di Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|No |
