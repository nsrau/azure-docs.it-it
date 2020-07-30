---
title: Creare un'istanza gestita (modello di ARM e PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Usare questo script di esempio di Azure PowerShell per creare un'istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 8ac742c42a32e8b864c4be71e786c292ceb1514d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073379"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Usare PowerShell con un modello di Azure Resource Manager per creare un'istanza gestita

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

È possibile creare un'istanza gestita usando la libreria di Azure PowerShell e i modelli di Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario Azure PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, eseguire `Connect-AzAccount` per creare una connessione ad Azure.

Con i comandi di Azure PowerShell è possibile avviare la distribuzione usando un modello di Azure Resource Manager predefinito. Nel modello possono essere specificate le proprietà seguenti:

- Nome istanza gestita
- Nome utente e password dell'amministratore SQL.
- Dimensioni dell'istanza (numero di core e dimensioni massime di archiviazione).
- Rete virtuale e subnet in cui verrà inserita l'istanza.
- Regole di confronto a livello di server dell'istanza (anteprima).

Il nome dell'istanza, il nome utente dell'amministratore SQL, la rete virtuale/subnet e le regole di confronto non possono essere modificati in un secondo momento. Altre proprietà dell'istanza possono invece essere modificate.

## <a name="prerequisites"></a>Prerequisiti

Questo esempio presuppone che sia stato [creato un ambiente di rete valido](../virtual-network-subnet-create-arm-template.md) o che sia stata [modificata una rete virtuale esistente](../vnet-existing-add-subnet.md) per l'istanza gestita. Se necessario, è possibile preparare l'ambiente di rete usando un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) separato. 


L'esempio usa i cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) per assicurarsi che siano installati i moduli di PowerShell seguenti:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager


Salvare lo script seguente in un file JSON e prendere nota del percorso del file: 

```json
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
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
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

Aggiornare lo script di PowerShell seguente con il percorso di file corretto del file JSON salvato in precedenza e cambiare i nomi degli oggetti nello script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
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

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Al termine dell'esecuzione dello script, è possibile accedere all'istanza gestita da tutti i servizi di Azure e dall'indirizzo IP configurato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Esempi di script di PowerShell aggiuntivi per Istanza gestita di SQL di Azure sono disponibili in [Script di PowerShell per Istanza gestita di SQL di Azure](../../database/powershell-script-content-guide.md).
