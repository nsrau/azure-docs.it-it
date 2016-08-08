<properties
   pageTitle="Creare cluster Hadoop basati su Windows in HDInsight tramite modelli di Gestione risorse di Azure | Microsoft Azure"
   	description="Informazioni su come creare cluster per Azure HDInsight usando modelli di Gestione risorse di Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

# Creare cluster Hadoop basati su Windows in HDInsight tramite modelli di Gestione risorse di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight tramite modelli di Gestione risorse di Azure. Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../resource-group-template-deploy.md). Per altri strumenti e funzionalità per la creazione di cluster, fare clic sulla scheda Seleziona nella parte superiore di questa pagina o vedere [Metodi di creazione di cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- [Sottoscrizione di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell o interfaccia della riga di comando di Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Modelli di Gestione risorse di Azure

Il modello di Gestione risorse di Azure semplifica la creazione di cluster HDInsight, delle risorse dipendenti (ad esempio, l'account di archiviazione predefinito) e di altre risorse (ad esempio, il database SQL di Azure per usare Apache Sqoop) per l'applicazione in un'unica operazione coordinata. Nel modello vengono definite le risorse necessarie per l'applicazione e vengono specificati i parametri di distribuzione per immettere valori nei diversi ambienti di distribuzione. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

Nell'[Appendice A](#appx-a-arm-template) è disponibile un modello di Azure Resource Manager per la creazione di un cluster HDInsight e dell'account di Archiviazione di Azure dipendente. Usare un editor di testo per salvare il modello in un file nella workstation. Si apprenderà come chiamare il modello usando diversi strumenti.

Per altre informazioni sul modello di Gestione risorse di Azure, vedere

- [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md)
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](../resource-group-template-deploy.md)


## Distribuire con PowerShell

La procedura seguente consente di creare un cluster HDInsight.

**Per distribuire un cluster con il modello ARM**

1. Salvare il file JSON disponibile nell'[Appendice A](#appx-a-arm-template) della workstation.
2. Impostare i parametri, se necessario.
3. Eseguire il modello con lo script PowerShell seguente:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	Lo script di PowerShell configura solo il nome del cluster e il nome dell’account di archiviazione. È possibile impostare altri valori del modello ARM.
	
Per altre informazioni, vedere [Distribuire con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Distribuire con l'interfaccia della riga di comando di Azure

L'esempio seguente crea un cluster e i relativi account di archiviazione e contenitore dipendenti chiamando un modello di Gestione risorse di Azure:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"





## Distribuire con l'API REST

Vedere [Distribuire con l'API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Distribuire con Visual Studio

Con Visual Studio, è possibile creare un progetto del gruppo di risorse e distribuirlo in Azure mediante l'interfaccia utente. Selezionare il tipo di risorse da includere nel progetto e le risorse vengono aggiunte automaticamente al modello di Gestione risorse. Il progetto fornisce anche uno script di PowerShell per distribuire il modello.

Per un'introduzione all'uso di Visual Studio con gruppi di risorse, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:


- Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](../solution-dev-test-environments.md).
- Per informazioni sulle sezioni del modello di Azure Resource Manager, vedere [Creazione di modelli](../resource-group-authoring-templates.md).
- Per un elenco delle funzioni che è possibile usare in un modello di Azure Resource Manager, vedere [Funzioni di modello](../resource-group-template-functions.md).



##Appendice A: Modello di Gestione risorse di Azure

Il modello di Gestione risorse di Azure seguente crea un cluster Hadoop basato su Windows con l'account di archiviazione di Azure dipendente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

<!---HONumber=AcomDC_0727_2016-->