---
title: 'Esercitazione: Creare una pipeline usando il modello di Resource Manager | Documentazione Microsoft'
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un&quot;attività di copia usando il modello di Azure Resource Manager."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 535eb3c22dad35da3c1dbc10be5a7c11c6bb8d00


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Esercitazione: Creare una pipeline con l'attività di copia usando il modello di Azure Resource Manager
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Questa esercitazione illustra come creare e monitorare una data factory di Azure con un modello di Azure Resource Manager. La pipeline nella data factory copia i dati dall'archivio BLOB di Azure al database SQL di Azure.

## <a name="prerequisites"></a>Prerequisiti
* Vedere [Panoramica e prerequisiti dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti**.
* Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer. In questa esercitazione si usa PowerShell per distribuire le entità di Data Factory. 
* (facoltativo) Per informazioni sulla creazione di modelli di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md).

## <a name="in-this-tutorial"></a>Contenuto dell'esercitazione:
In questa esercitazione si crea una data factory con le entità di Data Factory seguenti:

| Entità | Descrizione |
| --- | --- |
| Servizio collegato Archiviazione di Azure |Collega l'account di archiviazione di Azure alla data factory. Archiviazione di Azure è l'archivio dati di origine e il database SQL di Azure è l'archivio dati del sink per l'attività di copia nell'esercitazione. Specifica l'account di archiviazione che contiene i dati di input per l'attività di copia. |
| Servizio collegato per il database SQL Azure |Collega il database SQL di Azure alla data factory. Specifica il database SQL di Azure che contiene i dati di output per l'attività di copia. |
| Set di dati di input del BLOB di Azure |Fa riferimento al servizio collegato di archiviazione di Azure. Il servizio collegato fa riferimento a un account di archiviazione di Azure e il set di dati del BLOB di Azure specifica il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input. |
| Set di dati di output di SQL Azure |Fa riferimento al servizio collegato SQL di Azure. Il servizio collegato SQL di Azure fa riferimento a un server di Azure SQL e il set di dati SQL di Azure specifica il nome della tabella che contiene i dati di output. |
| Data Pipeline |La pipeline ha un'attività di tipo copia che accetta il set di dati del BLOB di Azure come input e il set di dati SQL di Azure come output. L'attività di copia esegue la copia dei dati da un BLOB di Azure a una tabella nel database SQL di Azure. |

Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Esistono due tipi di attività: [attività di spostamento dei dati](data-factory-data-movement-activities.md) e [attività di trasformazione dei dati](data-factory-data-transformation-activities.md). In questa esercitazione si crea una pipeline con una sola attività (attività di copia).

![Copiare il BLOB di Azure nel database SQL di Azure](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

La sezione seguente contiene il modello di Resource Manager completo per la definizione di entità di Data Factory. In questo modo sarà possibile eseguire rapidamente l'esercitazione e testare il modello. Per conoscere come viene definita ogni entità di Data Factory, vedere la sezione [Entità di Data Factory nel modello](#data-factory-entities-in-the-template).

## <a name="data-factory-json-template"></a>Modello JSON di Data Factory
Il modello di Resource Manager principale per la definizione di una data factory è il seguente: 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Creare un file JSON denominato **ADFCopyTutorialARM.json** nella cartella **C:\ADFGetStarted** con il contenuto seguente:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
        },
        "resources": [
          {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
              {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
                }
              },
              {
                "type": "linkedservices",
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
                  }
                }
              },
              {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  }
                }
              },
              {
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
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>Parametri JSON
Creare un file JSON denominato **ADFCopyTutorialARM-Parameters.json** contenente i parametri per il modello di Azure Resource Manager. 

> [!IMPORTANT]
> Specificare il nome e la chiave dell'account di archiviazione di Azure per i parametri **storageAccountName** e **storageAccountKey**.  
> 
> 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": {    "value": "<Name of the Azure storage account>"    },
            "storageAccountKey": {
                     "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [!IMPORTANT]
> Si possono avere file JSON di parametri separati per gli ambienti di sviluppo, test e produzione che è possibile usare con lo stesso modello JSON di Data Factory. Usando uno script di Power Shell, è possibile automatizzare la distribuzione delle entità di Data Factory in questi ambienti.  
> 
> 

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
1. Avviare **Azure PowerShell** ed eseguire questo comando:
   * Eseguire `Login-AzureRmAccount` e immettere il nome utente e la password usati per accedere al portale di Azure.  
   * Eseguire `Get-AzureRmSubscription` per visualizzare tutte le sottoscrizioni per l'account.
   * Eseguire `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` per selezionare la sottoscrizione da usare. 
2. Eseguire il comando seguente per distribuire entità di Data Factory usando il modello di Resource Manager creato nel Passaggio 1.
   
        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Monitorare la pipeline
1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.
2. Scegliere **Data factory** dal menu a sinistra o fare clic su **Altri servizi** e quindi su **Data factory** nella categoria **INTELLIGENCE E ANALISI**.
   
    ![Menu Data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Nella pagina **Data factory** cercare e trovare la data factory. 
   
    ![Cercare la data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Fare clic sulla data factory di Azure. Viene visualizzata la home page della data factory.
   
    ![Home page della data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Fare clic sul riquadro **Diagramma** per visualizzare la vista diagramma della data factory.
   
    ![Vista diagramma della data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. Nella vista diagramma fare doppio clic sul set di dati **SQLOutputDataset**. Viene visualizzato lo stato della sezione. Quando l'operazione di copia termina, lo stato risulta impostato su **Pronto**.
   
    ![Sezione di output nello stato Pronto](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Quando la sezione è nello stato **Pronto**, verificare che i dati vengano copiati nella tabella **emp** nel database SQL di Azure.

Per istruzioni su come usare i pannelli del portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory](data-factory-monitor-manage-pipelines.md) .

È anche possibile usare l'app di monitoraggio e gestione per monitorare le pipeline di dati. Per i dettagli sull'uso dell'applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md) .

## <a name="data-factory-entities-in-the-template"></a>Entità di Data Factory nel modello
### <a name="define-data-factory"></a>Definire una data factory
È possibile definire una data factory nel modello di Resource Manager come illustrato nell'esempio seguente:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

Il valore dataFactoryName viene definito come segue: 

    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

È una stringa univoca basata sull'ID del gruppo di risorse.  

### <a name="defining-data-factory-entities"></a>Definizione di entità di Data factory
Le entità di Data Factory seguenti vengono definite nel modello JSON: 

1. [Servizio collegato Archiviazione di Azure](#azure-storage-linked-service)
2. [Servizio collegato SQL di Azure](#azure-sql-database-linked-service)
3. [Set di dati del BLOB di Azure](#azure-blob-dataset)
4. [Set di dati di Azure SQL](#azure-sql-dataset)
5. [Pipeline di dati con un'attività di copia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
In questa sezione si specificano il nome e la chiave dell'account di archiviazione di Azure. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato di Archiviazione di Azure, vedere [Servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

    {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureStorage",
            "description": "Azure Storage linked service",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
            }
        }
    }

connectionString usa i parametri storageAccountName e storageAccountKey. I valori per questi parametri sono stati passati usando un file di configurazione. La definizione usa anche le variabili azureStroageLinkedService e dataFactoryName definite nel modello. 

#### <a name="azure-sql-database-linked-service"></a>Servizio collegato per il database SQL Azure
In questa sezione si specificano il nome del server di Azure SQL, il nome del database, il nome utente e la password utente. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato di Azure SQL, vedere [Servizio collegato Azure SQL](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
              }
        }
    }

connectionString usa i parametri sqlServerName, databaseName, sqlServerUserName e sqlServerPassword i cui valori vengono passati usando un file di configurazione. La definizione usa anche le variabili seguenti del modello: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Set di dati del BLOB di Azure
Vengono specificati i nomi del contenitore BLOB, della cartella e del file contenente i dati di input. Per informazioni dettagliate sulle proprietà JSON usate per definire un set di dati del BLOB di Azure, vedere [Proprietà del set di dati del BLOB di Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 

    {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureBlob",
              "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
            "structure": [
            {
                  "name": "Column0",
                  "type": "String"
            },
            {
                  "name": "Column1",
                  "type": "String"
            }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              },
              "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Set di dati di Azure SQL
Viene specificato il nome della tabella nel database SQL di Azure che contiene i dati copiati dall'archivio BLOB di Azure. Per informazioni dettagliate sulle proprietà JSON usate per definire un set di dati di Azure SQL, vedere [Proprietà del set di dati di Azure SQL](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties). 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
            {
                  "name": "FirstName",
                  "type": "String"
            },
            {
                  "name": "LastName",
                  "type": "String"
            }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Day",
                "interval": 1
              }
        }
    }

#### <a name="data-pipeline"></a>Data Pipeline
Viene definita una pipeline che copia i dati dal set di dati del BLOB di Azure al set di dati di Azure SQL. Per le descrizioni degli elementi JSON usati per definire una pipeline in questo esempio, vedere [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json). 

    {
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
              "start": "2016-10-02T00:00:00Z",
              "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>Riutilizzare il modello
Nell'esercitazione sono stati creati un modello per definire le entità di Data Factory e un modello per passare i valori dei parametri. La pipeline copia i dati da un account di archiviazione di Azure a un database SQL di Azure specificato tramite i parametri. Per usare lo stesso modello per distribuire le entità di Data Factory in ambienti diversi, si crea un file dei parametri per ogni ambiente e lo si usa durante la distribuzione in tale ambiente.     

Esempio:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

Si noti che il primo comando usa il file dei parametri per l'ambiente di sviluppo, il secondo per l'ambiente di test e il terzo per l'ambiente di produzione.  

È anche possibile riutilizzare il modello per eseguire attività ripetute. Ad esempio, è necessario creare più data factory con una o più pipeline che implementano la stessa logica, ma ogni data factory usa account di archiviazione di Azure e di database SQL di Azure diversi. In questo scenario si usa lo stesso modello nello stesso ambiente (sviluppo, test o produzione) con file dei parametri diversi per creare le data factory.   




<!--HONumber=Nov16_HO2-->


