---
title: Spostare una rete virtuale di Azure in un'altra area di Azure usando Azure PowerShell
description: Spostare una rete virtuale di Azure da un'area di Azure a un'altra usando un modello di Gestione risorse e Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 753c239f4bf4d6a8f31d4dc5ca771f312cd34578
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828989"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Spostare una rete virtuale di Azure in un'altra area usando Azure PowerShell

Esistono diversi scenari per lo trasferimento di una rete virtuale di Azure esistente da un'area a un'altra. Ad esempio, potrebbe essere necessario creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. In alternativa, potrebbe essere necessario spostare una rete virtuale di produzione in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area. Per ulteriori informazioni sui modelli di Gestione risorse, vedere [esportare gruppi di risorse nei modelli](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario avere il ruolo Collaboratore rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello. Prima di esportare il modello, è necessario rimuovere tutti i peer di rete virtuale. Sarà quindi possibile ristabilirli dopo lo spostamento della rete virtuale.
    
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include, tuttavia, i bilanciamenti del carico, i gruppi di sicurezza di rete (gruppi) e gli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione. Per abilitare la quota richiesta, contattare il supporto tecnico.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparare lo spostamento
In questa sezione si prepara la rete virtuale per lo spostamento usando un modello di Gestione risorse. Si sposta quindi la rete virtuale nell'area di destinazione usando Azure PowerShell comandi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per esportare la rete virtuale e distribuire la rete virtuale di destinazione usando PowerShell, eseguire le operazioni seguenti:

1. Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e quindi seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ottenere l'ID risorsa della rete virtuale che si vuole spostare nell'area di destinazione e quindi posizionarlo in una variabile usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Esportare la rete virtuale di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Il file scaricato ha lo stesso nome del gruppo di risorse da cui è stata esportata la risorsa. Individuare il file *\<resource-Group-name >. JSON* , che è stato esportato con il comando e aprirlo nell'Editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **DefaultValue** del nome della rete virtuale di origine con il nome della rete virtuale di destinazione. Assicurarsi di racchiudere il nome tra virgolette.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in Resources:

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
  
1. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) eseguendo il comando seguente:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Opzionale È anche possibile modificare altri parametri nel file *\<resource-Group-name >. JSON* , a seconda dei requisiti:

    * **Spazio degli indirizzi**: Prima di salvare il file, è possibile modificare lo spazio degli indirizzi della rete virtuale modificando la sezione **resources** > **addressSpace** e modificando la proprietà **addressPrefixes** :

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

    * **Subnet**: È possibile modificare o aggiungere al nome della subnet e allo spazio di indirizzi della subnet modificando la sezione **subnet** del file. È possibile modificare il nome della subnet modificando la proprietà **Name** . È possibile modificare lo spazio degli indirizzi della subnet modificando la proprietà **addressPrefix** :

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

        Per modificare il prefisso dell'indirizzo, modificare il file in due posizioni: nel codice della sezione precedente e nella sezione **tipo** del codice seguente. Modificare la proprietà **addressPrefix** nel codice seguente in modo che corrisponda alla proprietà **addressPrefix** nel codice della sezione precedente.

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

1. Salvare il  *\<file Resource-Group-Name >. JSON* .

1. Creare un gruppo di risorse nell'area di destinazione per la distribuzione della rete virtuale di destinazione usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Distribuire il file modificato *\<resource-Group-name >. JSON* al gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Eliminare la rete virtuale o il gruppo di risorse 

Dopo aver distribuito la rete virtuale, per avviare o rimuovere la rete virtuale nell'area di destinazione, eliminare il gruppo di risorse creato nell'area di destinazione e la rete virtuale spostata verrà eliminata. 

Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eseguire una delle operazioni seguenti:

* Eliminare il gruppo di risorse usando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Eliminare la rete virtuale di origine usando [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale da un'area a un'altra usando PowerShell e quindi sono state rimosse le risorse di origine non necessarie. Per ulteriori informazioni sullo stato di trasferimento delle risorse tra le aree e il ripristino di emergenza in Azure, vedere:

- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
