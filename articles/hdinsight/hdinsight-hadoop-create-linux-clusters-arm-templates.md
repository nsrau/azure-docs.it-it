<properties
   pageTitle="Creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Gestione risorse di Azure | Microsoft Azure"
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

# Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight tramite modelli di Gestione risorse di Azure. Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../resource-group-template-deploy.md). Per altri strumenti e funzionalità per la creazione di cluster, fare clic sulla scheda Seleziona nella parte superiore di questa pagina o vedere [Metodi di creazione di cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- [Sottoscrizione di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell e/o interfaccia della riga di comando di Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Modelli di Gestione risorse di Azure

Il modello di Gestione risorse di Azure semplifica la creazione di cluster HDInsight, delle risorse dipendenti (ad esempio, l'account di archiviazione predefinito) e di altre risorse (ad esempio, il database SQL di Azure per usare Apache Sqoop) per l'applicazione in un'unica operazione coordinata. Nel modello vengono definite le risorse necessarie per l'applicazione e vengono specificati i parametri di distribuzione per immettere valori nei diversi ambienti di distribuzione. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione.

Nell'[Appendice A](#appx-a-arm-template) è disponibile un modello di Gestione risorse di Azure per la creazione di un cluster HDInsight e dell'account di Archiviazione di Azure dipendente. Usare [VSCode](https://code.visualstudio.com/#alt-downloads) multipiattaforma con l’[estensione ARM](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor di testo per salvare il modello in un file sulla workstation. Si apprenderà come chiamare il modello usando diversi strumenti.

Per altre informazioni sul modello di Gestione risorse di Azure, vedere

- [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md)
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](../resource-group-template-deploy.md)


## Distribuire con PowerShell

Con la procedura seguente si creano cluster HDInsight basati su Linux.

**Per distribuire un cluster con il modello ARM**

1. Salvare il file JSON disponibile nell'[Appendice A](#appx-a-arm-template) della workstation. Nello script PowerShell, il nome di file è *C:\\HDITutorials-ARM\\hdinsight-arm-template.json*.
2. Se necessario, impostare i parametri e le variabili.
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
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	Lo script PowerShell configura soltanto il nome del cluster. Il nome dell’account di archiviazione è hardcoded nel modello. Verrà chiesto di immettere la password utente del cluster (il nome utente predefinito è *admin*); e la password utente SSH (il nome utente SSH predefinito è *sshuser*).
	
Per ulteriori informazioni, vedere [Distribuire con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Distribuire con l'interfaccia della riga di comando di Azure

L'esempio seguente crea un cluster e i relativi account di archiviazione e contenitore dipendenti chiamando un modello di Gestione risorse di Azure:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
Verrà chiesto di immettere il nome del cluster, la password utente del cluster (il nome utente predefinito è *admin*) e la password utente SSH (il nome utente SSH predefinito è *sshuser*). Per fornire i parametri inline:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

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

Il modello di Gestione risorse di Azure seguente crea un cluster Hadoop basato su Linux con l'account di archiviazione di Azure dipendente.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the ssh user."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },  
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }      
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"      
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [],
        "tags": {},
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]"
        ],
        "tags": {},
        "properties": {
            "clusterVersion": "3.2",
            "osType": "Linux",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
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
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Large"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Large"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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