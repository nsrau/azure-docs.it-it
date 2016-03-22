<properties
	pageTitle="Creare la prima data factory (modello ARM) | Microsoft Azure"
	description="In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando il modello di Gestione risorse di Azure."
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
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# Esercitazione: Creare la prima data factory di Azure usando il modello di Azure Resource Manager
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md)
- [Con l'editor di Data factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Tramite PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Con Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Con il modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)


Questo articolo descrive come usare un modello di Gestione risorse di Azure per creare la prima data factory di Azure.


## Prerequisiti
Oltre ai prerequisiti elencati nell'argomento di panoramica dell'esercitazione, sarà necessario installare quanto segue:

- Prima di procedere è **necessario** leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi richiesti. 
- **Installare Azure PowerShell**. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.
- Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere [Introduzione al servizio Data factory di Azure](data-factory-introduction.md). 
- Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md). 

> [AZURE.IMPORTANT]
Per eseguire la procedura dettagliata illustrata in questo articolo, è necessario completare le procedure preliminari illustrate in [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md).

## Passaggio 1: Creare il modello di Gestione risorse di Azure

Creare un file JSON denominato **ADFTutorialARM.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

Il modello consente di creare le seguenti entità di Data factory.

1. Una **data factory** denominata **TutorialDataFactoryARM**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. 
2. Due **servizi collegati**: **StorageLinkedService** e **HDInsightOnDemandLinkedService**. I servizi collegati collegano l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. È necessario identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati. 
3. Due **set di dati** (input/output): **AzureBlobInput** e **AzureBlobOutput**. Questi set di dati rappresentano i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **StorageLinkedService** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.   

Fare clic sulla scheda **Tramite l'editor di Data factory** per passare all'articolo che fornisce informazioni dettagliate sulle proprietà JSON usate in questo modello.

> [AZURE.IMPORTANT] Modificare i valori per le variabili **storageAccountName** e **storageAccountKey**. Modificare anche **dataFactoryName** perché il nome deve essere univoco.


	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
	        "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "AzureStorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
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
                                "clusterSize": 4,
                                "version":  "3.2",
            					"timeToLive": "00:05:00",
                                "osType": "linux",
            					"linkedServiceName": "[variables('storageLinkedServiceName')]",
    						}
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
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
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
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
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

Fare clic sulla scheda **Con l'editor di Data Factory** per passare all'articolo che fornisce informazioni dettagliate sulle proprietà JSON usate in questo modello.

Tenere presente quanto segue:

- Data Factory crea automaticamente un cluster HDInsight **basato su Linux** con il codice JSON precedente. È anche possibile fare in modo che crei un cluster HDInsight **basato su Windows**. Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
- È possibile usare **il proprio cluster HDInsight** invece di un cluster HDInsight su richiesta. Per i dettagli, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Si tratta di un comportamento previsto da progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione.

	Man mano che vengono elaborate sempre più sezioni, verranno visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. Il nome di questi contenitori segue uno schema: "adf**nomedatafactory**-**nomeserviziocollegato**-datatimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [AZURE.NOTE] Un altro esempio di modello di Azure Resource Manager per la creazione di una data factory di Azure è disponibile in [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Passaggio 2: Distribuire entità della data factory con il modello di Gestione risorse di Azure

1. Aprire **Azure PowerShell** ed eseguire il comando seguente. 
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire il comando seguente per selezionare una sottoscrizione in cui si vuole creare la data factory. Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
1. Eseguire il comando seguente per distribuire entità della data factory usando il modello di Gestione risorse di Azure creato nel passaggio 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Monitorare la pipeline
 
1.	Dopo l'accesso al [portale di Azure](https://portal.azure.com/) fare clic su **Esplora** e selezionare **Data factory**. ![Esplora -> Data factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Nel pannello **Data factory** fare clic sulla data factory **TutorialFactoryARM** creata.	
2.	Nel pannello **Data factory** relativo alla data factory scelta fare clic su **Diagramma**. ![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	In **Vista diagramma** saranno visualizzati una panoramica delle pipeline e i set di dati usati in questa esercitazione.
	
	![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. In Vista diagramma fare doppio clic sul set di dati **AzureBlobOutput**. Verrà visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Al termine dell'elaborazione lo stato della sezione sarà **Pronto**. La creazione di un cluster HDInsight su richiesta di solito richiede tempo (circa 20 minuti). 

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Quando lo stato della sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB.  
 

<!---HONumber=AcomDC_0316_2016-->