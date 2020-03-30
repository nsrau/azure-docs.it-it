---
title: Spostare il servizio di bilanciamento del carico interno di Azure in un'altra area di Azure usando Azure PowerShellMove Azure internal Load Balancer to another Azure region using Azure PowerShell
description: Usare il modello di Azure Resource Manager per spostare Azure Internal Load Balancer da un'area di Azure a un'altra usando Azure PowerShellUse Azure Resource Manager template to move Azure internal Load Balancer from an Azure region to another using Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644274"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Spostare il servizio di bilanciamento del carico interno di Azure in un'altra area usando PowerShellMove Azure internal Load Balancer to another region using PowerShell

Esistono vari scenari in cui si desidera spostare il servizio di bilanciamento del carico interno esistente da un'area a un'altra. Ad esempio, è possibile creare un servizio di bilanciamento del carico interno con la stessa configurazione per il test. È anche possibile spostare un servizio di bilanciamento del carico interno in un'altra area come parte della pianificazione del ripristino di emergenza.

I servizi di bilanciamento del carico interni di Azure non possono essere spostati da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e la rete virtuale di un servizio di bilanciamento del carico interno.  È quindi possibile creare temporaneamente la risorsa in un'altra area esportando il servizio di bilanciamento del carico e la rete virtuale in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire i modelli nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere Esportare gruppi di risorse in [modelliFor more information](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) on Resource Manager and templates, see Export resource groups to templates


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che il servizio di bilanciamento del carico interno di Azure si trovi nell'area di Azure da cui si vuole spostare.

- I servizi di bilanciamento del carico interni di Azure non possono essere spostati da un aree all'altro.  È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico interno e distribuire un modello per creare un servizio di bilanciamento del carico interno in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a servizi di bilanciamento del carico, gruppi di sicurezza di rete, macchine virtuali e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare servizi di bilanciamento del carico interni nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di servizi di bilanciamento del carico per questo processo.  Vedere Limiti, quote e vincoli di sottoscrizione e servizio di [AzureSee Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
The following steps show how to prepare the internal load balancer for the move using a Resource Manager template, and move the internal load balancer configuration to the target region using Azure PowerShell.  Come parte di questo processo, la configurazione della rete virtuale del servizio di bilanciamento del carico interno deve essere inclusa e deve essere eseguita prima di spostare il servizio di bilanciamento del carico interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Esportare il modello di rete virtuale e distribuirlo da Azure PowerShellExport the virtual network template and deploy from Azure PowerShell

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Ottenere l'ID risorsa della rete virtuale che si desidera spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la rete virtuale di origine in un file con estensione json nella directory in cui viene eseguito il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Il file scaricato verrà denominato in base al gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file esportato dal comando denominato ** \<nome-gruppo-risorse>.json** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **defaultValue** del nome della rete virtuale di origine con il nome della rete virtuale di destinazione, assicurarsi che il nome sia tra virgolette:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in risorse:

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
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell Get-AzLocation eseguendo il comando seguente:To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  È anche possibile modificare altri parametri nel file ** \<resource-group-name>.json,** se lo si desidera, e sono facoltativi a seconda dei requisiti:You can also change other parameters in the resource-group-name>.json file if you choose, and are optional depending on your requirements:

    * **Spazio indirizzi:** lo spazio degli indirizzi della rete virtuale può essere modificato prima di salvare modificando la sezione**addressSpace** delle **risorse** > e modificando la proprietà **addressPrefixes** nel file ** \<>.json** del gruppo di risorse:

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

    * **Subnet:** il nome della subnet e lo spazio di indirizzi della subnet possono essere modificati o aggiunti modificando la sezione **subnet** del ** \<file>.json** del gruppo di risorse. Il nome della subnet può essere modificato modificando la proprietà **name.** Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà addressPrefix nel file>.json del gruppo di risorse:The subnet address space can be changed by altering the **addressPrefix** property in the ** \<resource-group-name>.json** file:

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

         Nel file **type** **resource-group-name>.json, per modificare il prefisso dell'indirizzo, è necessario modificarlo in due posizioni, la sezione elencata in precedenza e la sezione type elencata di \<** seguito.  Modificare la proprietà **addressPrefix** in modo che corrisponda a quella precedente:

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

9.  Salvare il ** \<nome del gruppo di risorse>** file json.

10. Creare un gruppo di risorse nell'area di destinazione per il vNET di destinazione da distribuire tramite [New-AzResourceGroupCreate](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) a resource group in the target region for the target VNET to be deployed using New-AzResourceGroup
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuire il file ** \<modificato nome-gruppo di risorse>.json** nel gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Esportare il modello di bilanciamento del carico interno e distribuirlo da Azure PowerShellExport the internal load balancer template and deploy from Azure PowerShell

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa del servizio di bilanciamento del carico interno che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la configurazione del servizio di bilanciamento del carico interno di origine in un file con estensione json nella directory in cui è stato eseguito il comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Il file scaricato verrà denominato in base al gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file esportato dal comando denominato ** \<nome-gruppo-risorse>.json** e aprirlo in un editor di propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome del servizio di bilanciamento del carico interno, modificare la proprietà **defaultValue** del nome del servizio di bilanciamento del carico interno di origine con il nome del servizio di bilanciamento del carico interno di destinazione, verificare che il nome sia tra virgolette:

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
 
6. Per modificare il valore della rete virtuale di destinazione spostata in precedenza, è necessario innanzitutto ottenere l'ID risorsa, quindi copiarlo e incollarlo nel ** \<file>.json** del gruppo di risorse.  Per ottenere l'ID, utilizzare [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digitare la variabile e premere Invio per visualizzare l'ID risorsa.  Evidenziare il percorso ID e copiarlo negli Appunti:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Nel file ** \<>.json nomegruppo di risorse** incollare l'ID **risorsa** dalla variabile al posto di **defaultValue** nel secondo parametro per l'ID di rete virtuale di destinazione, assicurarsi di racchiudere il percorso tra virgolette:
   
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

8. Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico interno, modificare la proprietà **location** in **resources** nel file ** \<resource-group-name>.json:**

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

11. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell Get-AzLocation eseguendo il comando seguente:To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. È inoltre possibile modificare altri parametri nel modello, se lo si desidera, e sono facoltativi a seconda dei requisiti:
    
    * **Sku-** È possibile modificare lo sku del servizio di bilanciamento del carico interno nella configurazione da standard a basic o basic a standard modificando la proprietà del nome sku nel file>.json del nome del gruppo di risorse:Sku - You can change the sku of the internal load balancer in the configuration from standard to basic or basic to standard by altering the **sku** > **name** property in the ** \<resource-group-name>.json** file:

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
      Per altre informazioni sulle differenze tra i servizi di bilanciamento del carico sku di base e standard, vedere Panoramica di [Azure Standard Load BalancerFor](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) more information on the differences between basic and standard sku load balancers, see Azure Standard Load Balancer overview

    * Regole di **bilanciamento del carico:** è possibile aggiungere o rimuovere regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **loadBalancingRules** del file ** \<>.json** del gruppo di risorse:

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
       Per altre informazioni sulle regole di bilanciamento del carico, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Probe:** è possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **probe** del file ** \<>.json** del gruppo di risorse:

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
       Per altre informazioni sui probe di integrità di Azure Load Balancer, vedere Test di integrità di Load BalancerFor more information on Azure Load Balancer health [probes,](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) see Load Balancer health probes

    * **Regole NAT in ingresso:** è possibile aggiungere o rimuovere regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo voci nella sezione **inboundNatRules** del ** \<file>.json** del gruppo di risorse:

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
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, la regola deve essere presente o rimossa come proprietà **di tipo** alla fine del file ** \<>.json** del gruppo di risorse:

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
        Per altre informazioni sulle regole NAT in ingresso, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Salvare il ** \<nome del gruppo di risorse>** file json.
    
10. Creare o un gruppo di risorse nell'area di destinazione per il servizio di bilanciamento del carico interno di destinazione da distribuire tramite [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Il gruppo di risorse esistente dall'alto può anche essere riutilizzato come parte di questo processo:The existing resource group from above can also be reused as part of this process:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il file ** \<modificato nome-gruppo di risorse>.json** nel gruppo di risorse creato nel passaggio precedente usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Discard 

Dopo la distribuzione, se si desidera ricominciare o eliminare la rete virtuale e il servizio di bilanciamento del carico nella destinazione, eliminare il gruppo di risorse creato nella destinazione e la rete virtuale spostata e il servizio di bilanciamento del carico verranno eliminati.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del gruppo di sicurezza di rete, eliminare il gruppo di sicurezza di rete o il gruppo di risorse di origine, usare [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) o [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) e [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico interno di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
