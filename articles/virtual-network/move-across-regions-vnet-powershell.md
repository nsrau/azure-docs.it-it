---
title: Spostare una rete virtuale di Azure in un'altra area di Azure usando Azure PowerShellMove an Azure virtual network to another Azure region by using Azure PowerShell
description: Spostare una rete virtuale di Azure da un'area di Azure a un'altra usando un modello di Resource Manager e Azure PowerShell.Move an Azure virtual network from an Azure region to another by using a Resource Manager template and Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646711"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Spostare una rete virtuale di Azure in un'altra area tramite Azure PowerShellMove an Azure virtual network to another region by using Azure PowerShell

Esistono vari scenari per spostare una rete virtuale di Azure esistente da un'area a un'altra. Ad esempio, è possibile creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. In alternativa, è possibile spostare una rete virtuale di produzione in un'altra area come parte della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area. Per ulteriori informazioni sui modelli di Resource Manager, vedere [Esportare gruppi](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)di risorse in modelli .


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale si trovi nell'area di Azure da cui si vuole spostare il numero.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario disporre del ruolo Collaboratore di rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello. Prima di esportare il modello, è necessario rimuovere tutti i peer di rete virtuale. È quindi possibile ristabilirli dopo lo spostamento della rete virtuale.
    
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato ai servizi di bilanciamento del carico, ai gruppi di sicurezza di rete (NSG) e agli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione. Per abilitare la quota richiesta, contattare il supporto tecnico.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparati per il trasloco
In questa sezione si prepara la rete virtuale per lo spostamento usando un modello di Resource Manager.In this section, you prepare the virtual network for the move by using a Resource Manager template. È quindi possibile spostare la rete virtuale nell'area di destinazione usando i comandi di Azure PowerShell.Then move the virtual network to the target region by using Azure PowerShell commands.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per esportare la rete virtuale e distribuire la rete virtuale di destinazione tramite PowerShell, eseguire le operazioni seguenti:To export the virtual network and deploy the target virtual network by using PowerShell, do the following:

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e quindi seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command, and then follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ottenere l'ID risorsa della rete virtuale che si desidera spostare nell'area di destinazione e quindi inserirlo in una variabile utilizzando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Esportare la rete virtuale di origine in un file con estensione json nella directory in cui viene eseguito il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Il file scaricato ha lo stesso nome del gruppo di risorse da cui è stata esportata la risorsa. Individuare il * \<file>.json* nome gruppo di risorse, esportato con il comando, quindi aprirlo nell'editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **defaultValue** del nome della rete virtuale di origine con il nome della rete virtuale di destinazione. Assicurarsi di racchiudere il nome tra virgolette.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in resources:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell Get-AzLocation eseguendo il comando seguente:To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Facoltativo) È anche possibile modificare altri parametri nel file * \<resource-group-name>.json,* a seconda dei requisiti:You can also change other parameters in the resource-group-name>.json file, depending on your requirements:

    * **Spazio indirizzo**: prima di salvare il file, è possibile modificare lo spazio degli indirizzi della rete virtuale modificando la sezione**addressSpace** delle **risorse** > e modificando la proprietà **addressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Subnet**: È possibile modificare o aggiungere il nome della subnet e lo spazio degli indirizzi della subnet modificando la **sezione** subnet del file. È possibile modificare il nome della subnet modificando la proprietà **name.** È inoltre possibile modificare lo spazio degli indirizzi della subnet modificando la proprietà **addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Per modificare il prefisso dell'indirizzo, modificare il file in due posizioni: nel codice nella sezione precedente e nella sezione **type** del codice seguente. Modificare la proprietà **addressPrefix** nel codice seguente in modo che corrisponda alla proprietà **addressPrefix** nel codice della sezione precedente.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Salvare il * \<nome del gruppo di risorse>* file json.

1. Creare un gruppo di risorse nell'area di destinazione per la rete virtuale di destinazione da distribuire tramite New-AzResourceGroup:Create a resource group in the target region for the target virtual network to be deployed by using [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Distribuire il file * \<>.json* modificato nome gruppo di risorse>.json nel gruppo di risorse creato nel passaggio precedente utilizzando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Per verificare che le risorse siano state create nell'area di destinazione, utilizzare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Eliminare la rete virtuale o il gruppo di risorseDelete the virtual network or resource group 

Dopo aver distribuito la rete virtuale, per rientrare o eliminare la rete virtuale nell'area di destinazione, eliminare il gruppo di risorse creato nell'area di destinazione e la rete virtuale spostata verrà eliminata. 

Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eseguire una delle operazioni seguenti:

* Eliminare il gruppo di risorse usando [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Eliminare la rete virtuale di origine utilizzando [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale da un'area a un'altra tramite PowerShell e quindi è stata pulita le risorse di origine non necessarie. Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, see:

- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare le macchine virtuali di Azure in un'altra areaMove Azure virtual machines to another region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
