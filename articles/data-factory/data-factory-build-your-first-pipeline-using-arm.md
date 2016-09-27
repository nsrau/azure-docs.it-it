<properties
	pageTitle="Creare la prima data factory (modello di Resource Manager) | Microsoft Azure"
	description="In questa esercitazione si crea una pipeline di esempio di Azure Data Factory usando il modello di Azure Resource Manager."
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
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Esercitazione: Creare la prima data factory di Azure usando il modello di Azure Resource Manager
> [AZURE.SELECTOR]
- [Panoramica e prerequisiti](data-factory-build-your-first-pipeline.md)
- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

In questo articolo viene usato un modello di Azure Resource Manager per creare la prima data factory di Azure.

## Prerequisiti
- Vedere la [panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi relativi ai **prerequisiti**.
- Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.
- Per informazioni sulla creazione di modelli di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md).

## Creare un modello di Resource Manager

In questa sezione vengono create le entità di Data Factory seguenti:

1. Una **data factory** denominata **TutorialDataFactoryARM**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input.
2. Due **servizi collegati**: **StorageLinkedService** e **HDInsightOnDemandLinkedService**. I servizi collegati collegano l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. Identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati.
3. Due **set di dati** (input/output): **AzureBlobInput** e **AzureBlobOutput**. Questi set di dati rappresentano i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **StorageLinkedService** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.

Fare clic sulla scheda **Tramite l'editor di Data factory** per passare all'articolo che fornisce informazioni dettagliate sulle proprietà JSON usate in questo modello.

Creare un file JSON denominato **ADFTutorialARM.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

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
                                "osType": "windows",
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
						        "start": "2016-04-01T00:00:00Z",
						        "end": "2016-04-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

Fare clic sulla scheda **Con l'editor di Data Factory** per passare all'articolo che fornisce informazioni dettagliate sulle proprietà JSON usate in questo modello.

Tenere presente quanto segue:

- Data Factory crea automaticamente un cluster HDInsight **basato su Windows** con il codice JSON precedente. È anche possibile creare automaticamente un cluster HDInsight **basato su Linux**. Per i dettagli, vedere [Servizio collegato Azure HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
- È possibile usare il **proprio cluster HDInsight** anziché un cluster HDInsight su richiesta. Per i dettagli, vedere [Servizio collegato Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione.

	Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono questo schema: "adf**nomedatafactory**-**nomeserviziocollegato**-datetimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [AZURE.NOTE] Un altro esempio di modello di Resource Manager per la creazione di un'istanza di Azure Data Factory è disponibile in [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Creare un'istanza di Data Factory

1. Avviare **Azure PowerShell** ed eseguire questo comando:
	- Eseguire `Login-AzureRmAccount` e immettere il nome utente e la password usati per accedere al portale di Azure.
	- Eseguire `Get-AzureRmSubscription` per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
1. Eseguire il comando seguente per distribuire entità di Data Factory usando il modello di Resource Manager creato nel Passaggio 1.

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Monitorare la pipeline
 
1.	Dopo l'accesso al [portale di Azure](https://portal.azure.com/) fare clic su **Esplora** e selezionare **Data factory**. ![Esplora -> Data factory](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Nel pannello **Data factory** fare clic sulla data factory **TutorialFactoryARM** creata.
2.	Nel pannello **Data factory** relativo alla data factory scelta fare clic su **Diagramma**. ![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	In **Vista diagramma** saranno visualizzati una panoramica delle pipeline e i set di dati usati in questa esercitazione.
	
	![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. In Vista diagramma fare doppio clic sul set di dati **AzureBlobOutput**. Viene visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png) 
9. Al termine dell'elaborazione lo stato della sezione è **Pronta**. La creazione di un cluster HDInsight su richiesta di solito richiede tempo (circa 20 minuti). Di conseguenza, prevedere **circa 30 minuti** per l'elaborazione della sezione nella pipeline.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)  
10. Quando lo stato della sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB.

Per istruzioni su come usare i pannelli del portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory](data-factory-monitor-manage-pipelines.md).

È anche possibile usare l'app di monitoraggio e gestione per monitorare le pipeline di dati. Per i dettagli sull'uso dell'applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

> [AZURE.IMPORTANT] Il file di input viene eliminato quando la sezione viene elaborata correttamente. Per eseguire di nuovo la sezione o ripetere l'esercitazione, caricare quindi il file di input (input.log) nella cartella inputdata del contenitore adfgetstarted.

## Modello di Resource Manager per la creazione di un gateway
Di seguito è disponibile un modello di Resource Manager per la creazione di un gateway logico nel back-end. Installare un gateway nel computer locale o nella VM IaaS di Azure e registrare il gateway con il servizio Data Factory usando una chiave. Per altre informazioni vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "GatewayUsingArmDF",
	        "apiVersion": "2015-10-01",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "eastus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "gateways",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "name": "GatewayUsingARM",
	                    "properties": {
	                    	"description": "my gateway"
						}
	                }            
				]
	        }
	    ]
	}

Questo modello crea una data factory denominata GatewayUsingArmDF con un gateway denominato GatewayUsingARM.

## Vedere anche
| Argomento | Descrizione |
| :---- | :---- |
| [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) | Questo articolo fornisce un elenco di attività di trasformazione dei dati (ad esempio, la trasformazione Hive di HDInsight usata in questa esercitazione) supportate da Azure Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 

  

<!---HONumber=AcomDC_0921_2016-->