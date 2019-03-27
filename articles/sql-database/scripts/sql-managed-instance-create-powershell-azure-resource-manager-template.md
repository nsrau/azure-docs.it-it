---
title: Esempio di PowerShell - Creare un'istanza gestita nel database SQL di Azure | Microsoft Docs
description: Script di esempio di Azure PowerShell per creare un'istanza gestita nel database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 33a0e90f1a0b63138168c44b87385c05aac39c69
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57869762"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Usare PowerShell con un modello di Azure Resource Manager per creare un'istanza gestita nel database SQL di Azure

È possibile creare un'istanza gestita di database SQL di Azure usando la libreria di Azure PowerShell e i modelli di Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Con i comandi di Azure PowerShell è possibile avviare la distribuzione usando il modello di Azure Resource Manager predefinito. Nel modello possono essere specificate le proprietà seguenti:

- Nome dell'istanza
- Nome utente e password dell'amministratore SQL.
- Dimensioni dell'istanza (numero di core e dimensioni massime di archiviazione).
- Rete virtuale e subnet in cui verrà inserita l'istanza.
- Regole di confronto a livello di server dell'istanza (anteprima).

Il nome dell'istanza, il nome utente dell'amministratore SQL, la rete virtuale/subnet e le regole di confronto non possono essere modificati in seguito. Altre proprietà dell'istanza possono invece essere modificate.

## <a name="prerequisites"></a>Prerequisiti

In questo esempio si presuppone di disporre di aver [creato un ambiente di rete valido](../sql-database-managed-instance-create-vnet-subnet.md) oppure di aver [modificato una rete virtuale esistente](../sql-database-managed-instance-configure-vnet-subnet.md) per l'istanza gestita. L'esempio usa i commandlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) per assicurarsi che siano installati i moduli di PowerShell seguenti:

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Il contenuto seguente deve essere inserito in un file che rappresenta un modello che verrà usato per creare l'istanza:

```
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Si presuppone che esista giù una rete virtuale di Azure con la subnet correttamente configurata. Se non sia ha una subnet configurata in modo corretto, preparare l'ambiente di rete usando un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) distinto che può essere eseguito in modo indipendente o incluso in questo modello.

Salvare il contenuto di questo file come file JSON, inserire il percorso del file nello script di PowerShell seguente e cambiare i nomi degli oggetti nello script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Dopo aver eseguito correttamente lo script, è possibile accedere al database SQL da tutti i servizi di Azure e dall'indirizzo IP configurato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
