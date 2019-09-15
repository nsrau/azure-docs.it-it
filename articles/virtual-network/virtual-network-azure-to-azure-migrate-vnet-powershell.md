---
title: Spostare la rete virtuale di Azure in un'altra area di Azure usando Azure PowerShell
description: Usare il modello di Azure Resource Manager per spostare la rete virtuale di Azure da un'area di Azure a un'altra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0037419570ceffcd11dbc8dfe85b45d7ec0ee8ce
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997445"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Spostare la rete virtuale di Azure in un'altra area con Azure PowerShell

Esistono diversi scenari in cui si vuole spostare le reti virtuali di Azure esistenti (reti virtuali) da un'area a un'altra. Ad esempio, è possibile creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. Potrebbe anche essere necessario spostare una rete virtuale di produzione in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per altre informazioni su Gestione risorse e sui modelli, vedere [esportare gruppi di risorse nei modelli](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello.  È necessario rimuovere tutti i peer di rete virtuale prima di esportare il modello e ristabilire i peer dopo lo spostamento della rete virtuale.
    
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include, tuttavia, i bilanciamenti del carico, i gruppi di sicurezza di rete (gruppi) e gli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare la rete virtuale per lo spostamento usando un modello di Gestione risorse e spostare la rete virtuale nell'area di destinazione usando Azure PowerShell comandi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Esportare la rete virtuale e distribuire la rete virtuale di destinazione con PowerShell

1. Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID sottoscrizione in cui si vuole distribuire la rete virtuale di destinazione e posizionarla in una variabile usando [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):
   
    ```azurepowershell-interactive
    Get-AzSubscription | format-table

    $Subscription = "MySubscriptionID"
    ```
3. Ottenere l'ID risorsa della rete virtuale che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
4. Esportare la rete virtuale di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

5. Il file scaricato verrà denominato dopo il gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando denominato **< Resource-Group-name >. JSON** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

6. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **DefaultValue** del nome della rete virtuale di origine con il nome della rete virtuale di destinazione, verificare che il nome sia racchiuso tra virgolette:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

7.  Per modificare l'area di destinazione in cui verrà spostato il VNET, modificare la proprietà **location** in Resources:

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
  
8. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) eseguendo il comando seguente:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
9.  È anche possibile modificare altri parametri nel file **< Resource-Group-name >. JSON** se si sceglie e sono facoltativi in base ai requisiti:

    * **Spazio degli indirizzi** : lo spazio degli indirizzi di VNET può essere modificato prima del salvataggio modificando la sezione **Resources** > **addressSpace** e modificando la proprietà **addressPrefixes** in **< Resource-Group-Name File >. JSON** :
    
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

    * **Subnet** : il nome della subnet e lo spazio degli indirizzi della subnet possono essere modificati o aggiunti a modificando la sezione **subnet** del file **< resource-Group-Name >. JSON** . Il nome della subnet può essere modificato modificando la proprietà **Name** . Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà **addressPrefix** nel file **< Resource-group-name >. JSON** :
    
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
                    ],
    ```

    Nel file **< Resource-Group-name >. JSON** , per modificare il prefisso dell'indirizzo, è necessario modificarlo in due posizioni, la sezione sopra indicata e la sezione **Type** elencata di seguito.  Modificare la proprietà **addressPrefix** in modo che corrisponda a quella precedente:
                
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

10. Salvare il file **< Resource-Group-name >. JSON** .

11. Creare un gruppo di risorse nell'area di destinazione per la distribuzione di VNET di destinazione usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
12. Distribuire il file modificato **< Resource-Group-name >. JSON** al gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

13. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Annulla 

Dopo la distribuzione, se si vuole avviare o rimuovere la rete virtuale nella destinazione, eliminare il gruppo di risorse creato nella destinazione e la rete virtuale spostata verrà eliminata.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale di origine o il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
