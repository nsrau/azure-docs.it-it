<properties
	pageTitle="Creare la prima pipeline di Data factory di Azure con Azure PowerShell"
	description="In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando Azure PowerShell."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline di Data factory di Azure con Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Questo articolo descrive come usare Gestione risorse di Azure per creare la prima pipeline. Questa esercitazione include i passaggi seguenti:

1.	Creazione della data factory.
2.	Creazione dei servizi collegati (archivi dati, risorse di calcolo) e dei set di dati.
3.	Creazione della pipeline.

 

## Prerequisiti
Oltre ai prerequisiti elencati nell'argomento di panoramica dell'esercitazione, sarà necessario installare quanto segue:

- **Installare Azure PowerShell**. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.
- Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere [Introduzione al servizio Data factory di Azure](data-factory-introduction.md). 
- Per informazioni sulla creazione di modelli di Gestione risorse, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md). 
 

## Passaggio 1: Creare il modello di Gestione risorse di Azure

Creare un file JSON denominato **ADFTutorialARM.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

> [AZURE.IMPORTANT]Modificare i valori per le variabili **storageAccountName** e **storageAccountKey**. Modificare anche **dataFactoryName** perché il nome deve essere univoco.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                      "properties": {
	                        "type": "AzureBlob",
	                        "linkedServiceName": "StorageLinkedService",
	                        "typeProperties": {
	                          "folderPath": "data/partitioneddata",
	                          "format": {
	                            "type": "TextFormat",
	                            "columnDelimiter": ","
	                          }
	                        },
	                        "availability": {
	                          "frequency": "Month",
	                          "interval": 1
	                        }
	                      }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
	                        "properties": {
	                            "description": "My first Azure Data Factory pipeline using ARM",
	                            "activities": [
	                              {
	                                "type": "HDInsightHive",
	                                "typeProperties": {
	                                  "scriptPath": "script/partitionweblogs.hql",
	                                  "scriptLinkedService": "StorageLinkedService",
	                                  "defines": {
                                        "partitionedtable": "[concat('wasb://data@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"	                                  }
	                                },
	                                "outputs": [
	                                  {
	                                    "name": "[variables('blobOutputDataset')]"
	                                  }
	                                ],
	                                "scheduler": {
	                                    "frequency": "Month",
	                                    "interval": 1
	                                },
	                                "policy": {
	                                  "concurrency": 1,
	                                  "retry": 3
	                                },
	                                "name": "RunSampleHiveActivity",
	                                "linkedServiceName": "HDInsightOnDemandLinkedService"
	                              }
	                            ],
	                            "start": "2014-01-01",
	                            "end": "2014-01-02"
	                        }
	                    }
	            ]
	        }
	    ]
	}


## Passaggio 2: Distribuire entità della data factory con il modello di Gestione risorse di Azure

1. Aprire Azure PowerShell ed eseguire il comando seguente. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription SubscriptionName** per selezionare la sottoscrizione da utilizzare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
1. Eseguire il comando seguente per distribuire entità della data factory usando il modello di Gestione risorse di Azure creato nel passaggio 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Monitorare la pipeline
 
1.	Dopo l'accesso al [portale di Azure](http://portal.azure.com/) fare clic su **Esplora** e selezionare **Data factory**. ![Esplora -> Data factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Nel pannello **Data factory** fare clic sulla data factory **TutorialFactoryARM** creata.	
2.	Nel pannello **Data factory** relativo alla data factory scelta fare clic su **Diagramma**. ![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	In **Vista diagramma** saranno visualizzati i set di dati usati in questa esercitazione e una panoramica della pipeline.
	
	![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. In Vista diagramma fare doppio clic sul set di dati **AzureBlobOutput**. Verrà visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Al termine dell'elaborazione lo stato della sezione sarà **Pronto**. La creazione di un cluster HDInsight su richiesta di solito richiede tempo. 

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Quando lo stato sella sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **data** nell'archivio BLOB.  
 

<!---HONumber=AcomDC_1217_2015-->