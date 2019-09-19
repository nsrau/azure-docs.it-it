---
title: Spostare Load Balancer interni di Azure in un'altra area di Azure usando Azure PowerShell
description: Usare Azure Resource Manager modello per spostare Load Balancer interni di Azure da un'area di Azure a un'altra usando Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 52a43dff5d2e740633675b71d5177d0df876d3cd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092209"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Spostare Load Balancer interni di Azure in un'altra area usando PowerShell

Esistono diversi scenari in cui si vuole spostare il servizio di bilanciamento del carico interno esistente da un'area a un'altra. Ad esempio, è possibile creare un servizio di bilanciamento del carico interno con la stessa configurazione per il test. Potrebbe anche essere necessario spostare un servizio di bilanciamento del carico interno in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

I bilanciamenti del carico interno di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e la rete virtuale di un servizio di bilanciamento del carico interno.  È quindi possibile organizzare la risorsa in un'altra area esportando il servizio di bilanciamento del carico e la rete virtuale in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire i modelli nella nuova area.  Per altre informazioni su Gestione risorse e sui modelli, vedere [esportare gruppi di risorse nei modelli](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il servizio di bilanciamento del carico interno di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Non è possibile spostare i bilanciamenti del carico interno di Azure tra le aree.  È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico interno e distribuire un modello per creare un servizio di bilanciamento del carico interno in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, gruppi di sicurezza di rete, macchine virtuali e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare bilanciamenti del carico interno nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta dei bilanciamenti del carico per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare il servizio di bilanciamento del carico interno per lo spostamento usando un modello di Gestione risorse e spostare la configurazione del servizio di bilanciamento del carico interno nell'area di destinazione usando Azure PowerShell.  Come parte di questo processo, la configurazione della rete virtuale del servizio di bilanciamento del carico interno deve essere inclusa e deve essere eseguita prima di trasferire il servizio di bilanciamento del carico interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Esportare il modello di rete virtuale e distribuirlo da Azure PowerShell

1. Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Ottenere l'ID risorsa della rete virtuale che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la rete virtuale di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Il file scaricato verrà denominato dopo il gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando denominato  **\<Resource-Group-Name >. JSON** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **DefaultValue** del nome della rete virtuale di origine con il nome della rete virtuale di destinazione, verificare che il nome sia racchiuso tra virgolette:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Per modificare l'area di destinazione in cui verrà spostato il VNET, modificare la proprietà **location** in Resources:

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
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) eseguendo il comando seguente:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  È anche possibile modificare altri parametri nel  **\<file Resource-Group-Name >. JSON** se si sceglie e sono facoltativi in base ai requisiti:

    * **Spazio degli indirizzi** : lo spazio degli indirizzi di VNET può essere modificato prima del salvataggio modificando la >   **\< sezione Resources addressSpace e modificando la proprietà addressPrefixes nel Resource-Group-Name > file. JSON** :

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

    * **Subnet** : il nome della subnet e lo spazio degli indirizzi della subnet possono essere modificati o aggiunti a modificando la  **\<** sezione subnet del nome del gruppo di risorse > file con estensione JSON. Il nome della subnet può essere modificato modificando la proprietà **Name** . Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà **addressPrefix** nel  **\<file Resource-Group-Name >. JSON** :

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

         Nel file Resource- **Group-Name >. JSON, per modificare il prefisso dell'indirizzo, è necessario modificarlo in due posizioni, la sezione sopra indicata e la sezione Type elencata di seguito. \<**  Modificare la proprietà **addressPrefix** in modo che corrisponda a quella precedente:

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

9.  Salvare il  **\<file Resource-Group-Name >. JSON** .

10. Creare un gruppo di risorse nell'area di destinazione per la distribuzione di VNET di destinazione usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuire il  **\<nome del gruppo di risorse modificato >** file con estensione JSON al gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Esportare il modello del servizio di bilanciamento del carico interno e distribuirlo da Azure PowerShell

1. Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa del servizio di bilanciamento del carico interno che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la configurazione del servizio di bilanciamento del carico interno di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Il file scaricato verrà denominato dopo il gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando denominato  **\<Resource-Group-Name >. JSON** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome del servizio di bilanciamento del carico interno, modificare la proprietà **DefaultValue** del nome del servizio di bilanciamento del carico interno di origine con il nome del servizio di bilanciamento del carico interno di destinazione, verificare che il nome sia racchiuso tra virgolette:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Per modificare il valore della rete virtuale di destinazione spostata sopra, è innanzitutto necessario ottenere l'ID risorsa e quindi copiarlo e incollarlo nel  **\<file Resource-Group-Name >. JSON** .  Per ottenere l'ID, usare [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digitare la variabile e premere INVIO per visualizzare l'ID della risorsa.  Evidenziare il percorso ID e copiarlo negli Appunti:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Nel file  **Resource-Group-Name>.JSONincollarel'IDrisorsadallavariabilealpostodelDefaultValuenelsecondoparametroperl'IDretevirtualedidestinazione,assicurarsidiracchiudereilpercorso\<** tra virgolette:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico interno, modificare la proprietà **location** in **Resources** nel  **\<file Resource-Group-Name >. JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) eseguendo il comando seguente:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. È anche possibile modificare altri parametri nel modello, se si sceglie, e sono facoltativi in base ai requisiti:
    
    * **SKU** : è possibile modificare lo SKU del servizio di bilanciamento del carico interno nella configurazione da standard a Basic o Basic a standard modificando la proprietà**nome** **SKU** > in **\<Resource-Group-Name >. JSON**file:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Per altre informazioni sulle differenze tra i bilanciamenti del carico SKU Basic e standard, vedere [Panoramica di Azure Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regole di bilanciamento del carico** : è possibile aggiungere o rimuovere le regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella  **\<sezione loadBalancingRules del file Resource-Group-Name >. JSON** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Per ulteriori informazioni sulle regole di bilanciamento del carico, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Probe** : è possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella  **\<sezione probe del file Resource-Group-Name >. JSON** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Per altre informazioni sui Probe di integrità di Azure Load Balancer, vedere [Probe di integrità Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regole NAT in ingresso** : è possibile aggiungere o rimuovere le regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo le voci nella  **\<sezione inboundNatRules del file Resource-Group-Name >. JSON** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, è necessario che la regola sia presente o rimossa come proprietà del **tipo** alla fine del  **\<nome del gruppo di risorse > file con estensione JSON** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Per ulteriori informazioni sulle regole NAT in ingresso, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Salvare il  **\<file Resource-Group-Name >. JSON** .
    
10. Creare o un gruppo di risorse nell'area di destinazione per la distribuzione del servizio di bilanciamento del carico interno di destinazione usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Il gruppo di risorse esistente precedente può essere riutilizzato anche nell'ambito di questo processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il  **\<nome del gruppo di risorse modificato >** file con estensione JSON al gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Annulla 

Dopo la distribuzione, se si vuole avviare o rimuovere la rete virtuale e il servizio di bilanciamento del carico nella destinazione, eliminare il gruppo di risorse creato nella destinazione e la rete virtuale spostata e il servizio di bilanciamento del carico verranno eliminati.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del NSG, eliminare il NSG di origine o il gruppo di risorse, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) e [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico interno di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
