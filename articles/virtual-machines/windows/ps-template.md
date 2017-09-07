---
title: Creare una macchina virtuale Windows usando un modello di Azure | Documentazione Microsoft
description: Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ddab80262fe27c1f5995858ec7de75d7c46df081
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Creare una macchina virtuale Windows usando un modello di Resource Manager

Questo articolo descrive come distribuire un modello di Azure Resource Manager tramite PowerShell. Il modello creato consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

Per una descrizione dettagliata della risorsa macchina virtuale, vedere [Virtual machines in an Azure Resource Manager template](template-description.md) (Macchine virtuali in un modello di Azure Resource Manager). Per altre informazioni su tutte le risorse in un modello, vedere [Azure Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md) (Procedura dettagliata sui modelli di Azure Resource Manager).

L'esecuzione della procedura illustrata in questo articolo richiede circa cinque minuti.

## <a name="install-azure-powershell"></a>Installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](../../powershell-install-configure.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Tutte le risorse devono essere distribuite in un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md).

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Creare il gruppo di risorse nella località selezionata. Questo esempio descrive la creazione di un gruppo di risorse denominato **myResourceGroup** nella località **Stati Uniti occidentali**:

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>Creare i file

In questo passaggio si crea un file di modello che consente di distribuire le risorse e un file di parametri che fornisce i valori dei parametri nel modello. È possibile anche creare un file di autorizzazione da usare per eseguire operazioni in Azure Resource Manager.

1. Creare un file denominato *CreateVMTemplate.json* e aggiungervi questo codice JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

2. Creare un file denominato *Parameters.json* e aggiungervi questo codice JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. Creare un nuovo account di archiviazione e un nuovo contenitore:

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. Caricare i file nell'account di archiviazione:

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    Cambiare i percorsi -File nella posizione in cui sono stati archiviati i file.

## <a name="create-the-resources"></a>Creare le risorse

Distribuire il modello usando i parametri:

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> È inoltre possibile distribuire modelli e parametri da file locali. Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../../storage/common/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Informazioni su come creare e gestire una macchina virtuale sono disponibili in [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


