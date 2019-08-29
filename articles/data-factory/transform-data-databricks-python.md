---
title: Trasformare i dati con Python di Databricks - Azure | Microsoft Docs
description: Informazioni su come elaborare o trasformare i dati eseguendo un'attività Python di Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 02c4644c4440c3a00a21ef22674bcc0d00902ac2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140796"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Trasformare i dati eseguendo un'attività Python in Azure Databricks

L'attività Python di Azure Databricks in una [pipeline di Data Factory](concepts-pipelines-activities.md) esegue un file Python nel cluster di Azure Databricks. Questo articolo si basa sull'articolo relativo alle  [attività di trasformazione dei dati](transform-data.md) , che presenta una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate. Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

Per un'introduzione di undici minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definizione dell'attività Python di Databricks

Ecco la definizione JSON di esempio di un'attività Python di Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Proprietà dell'attività Python di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|Descrizione|Obbligatorio|
|---|---|---|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No|
|type|Per l'attività Python di Databricks il tipo di attività è DatabricksSparkPython.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione l'attività Python. Per informazioni su questo servizio collegato, vedere l'articolo  [Servizi collegati di calcolo](compute-linked-services.md) .|Yes|
|pythonFile|L'URI del file Python da eseguire. Sono supportati solo i percorsi DBFS.|Sì|
|parameters|Parametri della riga di comando che verranno passati al file Python. È una matrice di stringhe.|No|
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di <stringa, oggetto>|No|

## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di databricks

Nella definizione dell'attività di Databricks precedente si specificano questi tipi di libreria: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Per altre informazioni, consultare la [documentazione di Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) per i tipi di libreria.

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Uso dell'interfaccia utente dell'area di lavoro di Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Per ottenere il percorso dbfs della libreria aggiunto tramite l'interfaccia utente, è possibile usare [Databricks CLI (installazione)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencarle tutte tramite la CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copia della libreria con Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Esempio: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*