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
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 5f21f33678b8cf09d9dbd8966d42b1a5ebac9ffb
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224653"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Trasformare i dati eseguendo un notebook di Databricks

L'attività dei notebook di Azure Databricks in una [pipeline di Data Factory](concepts-pipelines-activities.md) esegue un notebook di Databricks nell'area di lavoro di Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate. Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

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
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Proprietà dell'attività dei notebook di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|DESCRIZIONE|Obbligatoria|
|---|---|---|
|name|Nome dell'attività nella pipeline.|Yes|
|description|Testo che descrive l'attività.|No |
|type|Per l'attività dei notebook di Databricks il tipo di attività è DatabricksNotebook.|Yes|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione il notebook di Databricks. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).|Yes|
|notebookPath|Percorso assoluto del notebook da eseguire nell'area di lavoro di Databricks. Questo percorso deve iniziare con una barra.|Yes|
|baseParameters|Matrice di coppie chiave-valore. I parametri base possono essere usati per ogni esecuzione attività. Se il notebook accetta un parametro non specificato, verrà usato il valore predefinito del notebook. Per altre informazioni sui parametri, vedere [Notebook di Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|No |
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di \<stringa, oggetto>.|No |


## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di Databricks

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
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Per altre informazioni, consultare la [documentazione di Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) per i tipi di libreria.

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Uso dell'interfaccia utente dell'area di lavoro di Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Per ottenere il percorso dbfs della libreria aggiunto tramite l'interfaccia utente, è possibile usare [Databricks CLI (installazione)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencarle tutte tramite la CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copia della libreria con Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Esempio: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
