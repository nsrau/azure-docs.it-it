---
title: Argomento di avvio rapido sulla distribuzione automatizzata di VM con Configurazione app di Azure
description: Questo argomento di avvio rapido illustra come usare il modulo di Azure PowerShell e i modelli di Azure Resource Manager per distribuire un archivio di Configurazione app di Azure. I valori dell'archivio verranno quindi usati per distribuire una VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom: mvc
ms.openlocfilehash: 7c6f4b2ea9494c004067a8b19df2c2f098ac2b7f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274510"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration"></a>Guida introduttiva: Distribuzione automatizzata di VM con Configurazione app

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure usando cmdlet o script di PowerShell. Questo argomento di avvio rapido illustra come usare Azure PowerShell e i modelli di Azure Resource Manager per distribuire un archivio di Configurazione app di Azure. Si apprenderà quindi come usare le coppie chiave-valore dell'archivio per distribuire una VM.

Usare il modello indicato nei prerequisiti per creare un archivio di Configurazione app e quindi aggiungervi le coppie chiave-valore tramite il portale di Azure o l'interfaccia della riga di comando di Azure. Il modello principale fa riferimento a configurazioni di coppie chiave-valore esistenti di un archivio di configurazione esistente. I valori recuperati vengono usati per impostare le proprietà delle risorse create dal modello, come una VM in questo esempio.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Per questa guida di avvio rapido è richiesto il modulo Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata nel computer locale. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e immettere le credenziali di Azure nella finestra del browser a comparsa:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Se si hanno più sottoscrizioni, selezionare quella che si vuole usare per questo argomento di avvio rapido eseguendo i cmdlet seguenti. Assicurarsi di sostituire `<your subscription name>` con il nome della sottoscrizione:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Distribuire un archivio di Configurazione app di Azure

Per poter applicare le coppie chiave-valore alla macchina virtuale, è necessario avere un archivio di Configurazione app di Azure esistente. Questa sezione illustra come distribuire un archivio di Configurazione app di Azure usando un modello di Azure Resource Manager. Se è già disponibile un archivio di Configurazione app, è possibile passare alla sezione successiva di questo articolo. 

1. Copiare e incollare il codice JSON seguente in un nuovo file denominato *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "free",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Copiare e incollare il codice JSON seguente in un nuovo file denominato *prereq.azuredeploy.parameters.json*. Sostituire **GET-UNIQUE** con un nome univoco per l'archivio di configurazione.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. Nella finestra di PowerShell eseguire il comando seguente per distribuire l'archivio di Configurazione app di Azure. Assicurarsi di sostituire il nome del gruppo di risorse, il percorso del file del modello e il percorso del file di parametri del modello.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Aggiungere le coppie chiave-valore di configurazione della VM

È possibile creare un archivio di Configurazione app usando un modello di Azure Resource Manager, ma è necessario aggiungere le coppie chiave-valore usando il portale di Azure o l'interfaccia della riga di comando di Azure. In questo argomento di avvio rapido le coppie chiave-valore vengono aggiunte con il portale di Azure.

1. Al termine della distribuzione, passare all'archivio di Configurazione app appena creato nel [portale di Azure](https://portal.azure.com).

1. Selezionare **Impostazioni** > **Chiavi di accesso**. Prendere nota della stringa di connessione della chiave primaria di sola lettura. Questa stringa di connessione verrà usata in seguito per configurare l'applicazione in modo da comunicare con l'archivio di Configurazione app creato.

1. Selezionare **Esplora configurazioni** > **Crea** per aggiungere le coppie chiave-valore seguenti:

   |Chiave|valore|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Immettere *modello* per **Etichetta**, ma lasciare vuoto il campo **Tipo di contenuto**.

## <a name="deploy-vm-using-stored-key-values"></a>Distribuire una VM con le coppie chiave-valore archiviate

Ora che sono state aggiunte le coppie chiave-valore nell'archivio, è possibile distribuire una VM usando un modello di Azure Resource Manager. Il modello fa riferimento alle chiavi **windowsOsVersion** e **diskSizeGB** create.

1. Copiare e incollare il codice JSON seguente in un nuovo file denominato *azuredeploy.json* oppure scaricare il file dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Copiare e incollare il codice JSON seguente in un nuovo file denominato *azuredeploy.parameters.json* oppure scaricare il file dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Sostituire i valori dei parametri nel modello con i valori seguenti:

   |Parametro|valore|
   |-|-|
   |adminPassword|Una password amministratore per la macchina virtuale.|
   |appConfigStoreName|Il nome dell'archivio di Configurazione app di Azure.|
   |appConfigStoreResourceGroup|Il gruppo di risorse che contiene l'archivio di Configurazione app.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Un nome utente di amministratore per la VM.|
   |storageAccountName|Un nome univoco per un account di archiviazione associato alla macchina virtuale.|
   |domainNameLabel|Un nome di dominio univoco.|

1. Nella finestra di PowerShell eseguire il comando seguente per distribuire l'archivio di Configurazione app di Azure. Assicurarsi di sostituire il nome del gruppo di risorse, il percorso del file del modello e il percorso del file di parametri del modello.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Congratulazioni! È stata distribuita una macchina virtuale usando le configurazioni archiviate in Configurazione app di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'archivio di Configurazione app, la VM e tutte le risorse correlate. Se si prevede di usare l'archivio di Configurazione app o la VM in futuro, è possibile evitare di eliminarli. Se non si intende continuare a usare il processo, eliminare tutte le risorse create con questa guida introduttiva eseguendo il cmdlet seguente:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata distribuita una macchina virtuale usando un modello di Azure Resource Manager e le coppie chiave-valore di Configurazione app di Azure.

Per informazioni sulla creazione di altre applicazioni con Configurazione app di Azure, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure](quickstart-aspnet-core-app.md)
