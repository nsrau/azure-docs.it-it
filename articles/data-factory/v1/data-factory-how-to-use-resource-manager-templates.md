---
title: Usare i modelli di Resource Manager in Data Factory | Microsoft Docs
description: "Informazioni su come creare e usare modelli di Azure Resource Manager per la creazione di entità di Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: d0c047c17608635b0d5fb0fb1353c6f8515d7a71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Usare modelli per creare entità di Azure Data Factory
## <a name="overview"></a>Panoramica
Durante l'uso di Azure Data Factory per le esigenze di integrazione dei dati, può essere necessario riusare lo stesso modello in ambienti diversi o implementare ripetutamente la stessa attività all'interno di una soluzione. I modelli consentono di implementare e gestire questi scenari in modo semplificato. I modelli di Azure Data Factory rappresentano la soluzione ideale per gli scenari che implicano riusabilità e ripetizione.

Si consideri la situazione in cui un'organizzazione ha 10 impianti di produzione a livello globale. I log provenienti da ogni impianto vengono archiviati in un database SQL Server locale separato. La società vuole compilare un unico data warehouse nel cloud per l'analisi ad hoc. Vuole inoltre sfruttare la stessa logica, ma configurazioni diverse per gli ambienti di sviluppo, test e produzione.

In questo caso, è necessario ripetere un'attività all'interno dello stesso ambiente, ma con valori diversi nelle 10 data factory per ogni impianto di produzione. In effetti, l'elemento della **ripetizione** è presente. La creazione di modelli consente l'astrazione di questo flusso generico (ovvero di pipeline con le stesse attività in ogni data factory), ma usa un file dei parametri separato per ogni impianto di produzione.

L'organizzazione vuole inoltre distribuire le 10 data factory più volte in ambienti diversi, pertanto i modelli possono sfruttare questa **riusabilità** utilizzando file dei parametri separati per gli ambienti di sviluppo, test e produzione.

## <a name="templating-with-azure-resource-manager"></a>Creazione di modelli con Azure Resource Manager
I [modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) rappresentano un'ottima soluzione per la creazione di modelli in Azure Data Factory. I modelli di Resource Manager consentono di definire l'infrastruttura e la configurazione della soluzione di Azure tramite un file JSON. Poiché i modelli di Azure Resource Manager funzionano con tutti i servizi di Azure o con la maggior parte di essi, Resource Manager può essere ampiamente usato per gestire facilmente tutte le risorse degli asset di Azure. Per informazioni generali sui modelli di Azure Resource Manager, vedere [Creazione di modelli di Gestione risorse di Azure](../../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="tutorials"></a>Esercitazioni
Per istruzioni dettagliate sulla creazione di entità di Data Factory tramite i modelli di Resource Manager, vedere le esercitazioni seguenti:

* [Tutorial: Create a pipeline to copy data by using Azure Resource Manager template](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md) (Esercitazione: Creare una pipeline per copiare dati usando il modello di Azure Resource Manager)
* [Tutorial: Create a pipeline to process data by using Azure Resource Manager template](data-factory-build-your-first-pipeline.md) (Esercitazione: Creare una pipeline per elaborare dati usando il modello di Azure Resource Manager)

## <a name="data-factory-templates-on-github"></a>Modelli di data factory in GitHub
Vedere i modelli di avvio rapido di Azure in GitHub elencati di seguito:

* [Create a Data factory to copy data from Azure Blob Storage to Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) (Creare una data factory per copiare dati dall'archivio BLOB di Azure al database SQL di Azure)
* [Create a Data factory with Hive activity on Azure HDInsight cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) (Creare una data factory con un'attività Hive in un cluster Azure HDInsight)
* [Create a Data factory to copy data from Salesforce to Azure Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) (Creare una data factory per copiare dati da Salesforce a BLOB di Azure)
* [Creare una Data factory che collega le attività: copia i dati da un server FTP ai BLOB di Azure, richiama uno script hive in un cluster HDInsight su richiesta per trasformare i dati e copia i risultati nel database SQL di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

È possibile condividere i modelli di Azure Data Factory in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Durante lo sviluppo di modelli che possono essere condivisi tramite questo repository, consultare la [guida alla collaborazione](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).

Le sezioni seguenti includono informazioni dettagliate sulla definizione delle risorse di Data Factory in un modello di Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definizione delle risorse di Data Factory nei modelli
Il modello principale per la definizione di una data factory è il seguente:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definire una data factory
È possibile definire una data factory nel modello di Resource Manager come illustrato nell'esempio seguente:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
Il valore dataFactoryName viene definito in "variables" come segue:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definire servizi collegati

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Per informazioni dettagliate sulle proprietà JSON per il servizio collegato specifico da distribuire, vedere [Servizio collegato Archiviazione](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [Servizi collegati di calcolo](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Il parametro "dependsOn" specifica il nome della data factory corrispondente. Un esempio di definizione di un servizio collegato per Archiviazione di Azure è illustrato nella definizione JSON seguente:

### <a name="define-datasets"></a>Definire i set di dati

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Per informazioni dettagliate sulle proprietà JSON per il tipo di set di dati specifico da distribuire, vedere [Archivi dati e formati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Il parametro "dependsOn" specifica il nome della data factory e del servizio collegato di archiviazione corrispondenti. Un esempio di definizione di un tipo di set di dati per l'archivio BLOB di Azure è illustrato nella definizione JSON seguente:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definire pipeline

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Per informazioni dettagliate sulle proprietà JSON per la definizione di attività e pipeline specifiche da distribuire, vedere la sezione relativa alla [definizione di pipeline](data-factory-create-pipelines.md#pipeline-json). Il parametro "dependsOn" specifica il nome della data factory e di tutti i servizi collegati o dei set di dati corrispondenti. Un esempio di pipeline che copia i dati dall'archivio BLOB di Azure al database SQL di Azure è illustrato nel frammento di codice JSON seguente:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Uso di parametri nel modello di Data Factory
Per le procedure consigliate sull'uso dei parametri, vedere l'articolo [Procedure consigliate per la creazione di modelli di Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md#parameters). In generale l'utilizzo dei parametri deve essere ridotto al minimo, soprattutto se è possibile usare variabili anziché parametri. Specificare i parametri solo negli scenari seguenti:

* Le impostazioni variano a seconda dell'ambiente, ad esempio di sviluppo, test e produzione
* Segreti (password)

Se è necessario eseguire il pull di segreti dall'[Insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md) quando si distribuiscono entità di Azure Data Factory tramite modelli, specificare l'**insieme di credenziali delle chiavi** e il **nome del segreto** come illustrato nell'esempio seguente:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Attualmente l'esportazione di modelli per le data factory esistenti non è ancora supportata, ma è in via di realizzazione.
>
>
