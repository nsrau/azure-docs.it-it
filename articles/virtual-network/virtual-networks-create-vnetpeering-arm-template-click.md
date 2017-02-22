---
title: Peering reti virtuali di Azure - Modello | Documentazione Microsoft
description: Informazioni su come creare un peering reti virtuali con un modello di Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: narayanannamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 75f8d10e-23e8-44bd-9972-aab74048cf38
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: ddfa2998d7e9305891c5a976dff5c2365d5eb1d4
ms.openlocfilehash: 351b77482c07160fa62155e90dcb8eb280e9087c


---
# <a name="create-a-virtual-network-peering-using-an-azure-resource-manager-template"></a>Creare un peering reti virtuali con un modello di Azure Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un peering reti virtuali con i modelli di Resource Manager, seguire questa procedura:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

   > [!NOTE]
   > Il cmdlet di PowerShell per la gestione di un peering reti virtuali viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. Il testo seguente illustra la definizione del collegamento per il peering reti virtuali da VNet1 a VNet2 in base allo scenario precedente. Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet1.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
    ```
3. La sezione seguente illustra la definizione del collegamento per un peering reti virtuali da VNet2 a VNet1 in base allo scenario precedente.  Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet2.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
    Come si può vedere nel modello precedente, esistono alcune proprietà configurabili per il peering reti virtuali:
   
   | Opzione | Descrizione | Default |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Indica se lo spazio indirizzi di un rete virtuale peer è incluso come parte del tag virtual_network. |Sì |
   | AllowForwardedTraffic |Consente di accettare o eliminare il traffico che non ha origine da una rete virtuale con peering. |No |
   | AllowGatewayTransit |Consente alla rete virtuale peer di usare il gateway di rete virtuale. |No |
   | UseRemoteGateways |Consente di usare il gateway di rete virtuale del peer. Per la rete virtuale peer deve essere configurato un gateway ed essere selezionata l'opzione AllowGatewayTransit. Non è possibile usare questa opzione se si ha un gateway configurato. |No |
   
    Ogni collegamento nel peering reti virtuali include un set delle proprietà precedenti. Ad esempio, è possibile impostare AllowVirtualNetworkAccess su True per il collegamento per il peering reti virtuali da VNet1 a VNet2 e su False per il collegamento per il peering reti virtuali nella direzione opposta.
4. Per distribuire il file modello è possibile eseguire `New-AzureRmResourceGroupDeployment` per creare o aggiornare la distribuzione. Per altre informazioni sull'uso di modelli di Resource Manager, vedere questo [articolo](../azure-resource-manager/resource-group-template-deploy.md).

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
    ```

   > [!NOTE]
   > Sostituire il nome del gruppo di risorse e il file modello in base alle esigenze.
   > 
   > 
   
    Di seguito è riportato un esempio basato sullo scenario precedente:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
    ```

    Output restituito:
   
        DeploymentName        : VNetPeeringVNet1
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
    ```

    Output restituito:
   
        DeploymentName        : VNetPeeringVNet2
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Al termine della distribuzione eseguire questo cmdlet per visualizzare lo stato del peering:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
    ```

    Output restituito:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : VNet101
        VirtualNetworkName    : VNet1
        ProvisioningState        : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Dopo aver stabilito il peering in questo scenario, sarà possibile avviare le connessioni da qualsiasi macchina virtuale a qualsiasi macchina virtuale in entrambe le reti virtuali. Per impostazione predefinita, `AllowVirtualNetworkAccess` è impostato su *True* e il peering reti virtuali effettuerà il provisioning degli ACL appropriati per consentire la comunicazione tra le reti virtuali. È comunque possibile applicare le regole del gruppo di sicurezza di rete per bloccare la connettività tra subnet o macchine virtuali specifiche per ottenere il controllo granulare dell'accesso tra due reti virtuali. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare un peering reti virtuali tra sottoscrizioni, seguire questa procedura:

1. Accedere ad Azure con l'account utente A con privilegi nella sottoscrizione A ed eseguire il cmdlet seguente:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
    ```

    Questo non è un requisito. Il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di utenti con privilegi dell'altra rete virtuale come utenti nella rete virtuale locale facilita la configurazione.
2. Accedere ad Azure con l'account utente B con privilegi per la sottoscrizione B ed eseguire il cmdlet seguente:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
    ```

    > [!IMPORTANT]
    > Se si sta creando il peering tra due reti virtuali create con il modello di distribuzione Azure Resource Manager, continuare con i passaggi rimanenti di questa sezione. Se le due reti virtuali sono state create con modelli di distribuzione diversi, ignorare i passaggi rimanenti di questa sezione e completare la procedura descritta nella sezione [Peering di reti virtuali create con modelli di distribuzione diversi](#x-model) di questo articolo.

3. Nella sessione di accesso dell'utente A eseguire questo cmdlet:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
    ```
   
    Il formato JSON è il seguente:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
    ```
4. Nella sessione di accesso dell'utente B eseguire il cmdlet seguente:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
    ```

    Il formato JSON è il seguente:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
    ```

    Dopo aver stabilito il peering in questo scenario, sarà possibile avviare le connessioni da qualsiasi macchina virtuale a qualsiasi macchina virtuale di entrambe le reti virtuali nelle diverse sottoscrizioni.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario è possibile distribuire il modello di esempio seguente per stabilire il peering reti virtuali. È necessario impostare la proprietà `AllowForwardedTraffic` su *True*, che consente all'appliance di rete virtuale nella rete virtuale con peering di inviare e ricevere traffico.

    Il codice JSON seguente è il contenuto di un modello per la creazione di un peering reti virtuali da HubVNet a VNet1. Si noti che AllowForwardedTraffic è impostato su false.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
2. Il codice JSON seguente è il contenuto di un modello per la creazione di un peering reti virtuali da VNet1 a HubVnet. Si noti che AllowForwardedTraffic è impostato su true.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }
    ```
3. Dopo aver stabilito il peering è possibile vedere questo [articolo](virtual-network-create-udr-arm-ps.md) per creare route definite dall'utente per reindirizzare il traffico di VNet1 attraverso un'appliance virtuale per usarne le funzionalità. Quando si specifica l'indirizzo dell'hop successivo nella route, è possibile impostarlo sull'indirizzo IP dell'appliance virtuale nella rete virtuale peer HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Per creare un peering tra reti virtuali da modelli di distribuzione diversi, seguire questa procedura:

1. Se si sta creando un peering tra reti virtuali distribuite con modelli di distribuzione diversi nella *stessa* sottoscrizione, andare al passaggio 2. La possibilità di creare un peering tra reti virtuali distribuite con modelli di distribuzione diversi in sottoscrizioni *diverse* è disponibile in versione di **anteprima**. Le funzionalità in anteprima non offrono lo stesso livello di affidabilità e lo stesso contratto di servizio delle funzionalità in versione di disponibilità generale. Se si sta creando un peering tra reti virtuali distribuite con modelli di distribuzione diversi in sottoscrizioni diverse, è necessario prima completare le attività seguenti:
    - Registrare la funzionalità in anteprima nella sottoscrizione di Azure immettendo il comando seguente di PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network`
    - Completare i passaggi 1 e 2 della sezione [Peering tra sottoscrizioni](#x-sub) di questo articolo.
2. Il testo seguente illustra la definizione del collegamento per il peering reti virtuali da VNET1 a VNET2 in questo scenario. È necessario un solo collegamento per eseguire il peering di una rete virtuale classica a una rete virtuale di Azure Resource Manager.

    Be sure to put in your subscription ID for where the classic virtual network or VNET2 is located and change MyResouceGroup to the appropriate resource group name.

    ```json
       {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [

            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNET1/LinkToVNET2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"
        }

            }
            }
        ]
        }
    ```
3. Per distribuire il file modello, eseguire questo cmdlet per creare o aggiornare la distribuzione:

    ```powershell
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
    ```

    Output restituito:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
4. Al termine della distribuzione è possibile eseguire il cmdlet seguente per visualizzare lo stato del peering:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
    ```

    Output restituito:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Dopo avere stabilito il peering tra una rete virtuale classica e una rete virtuale di Resource Manager, sarà possibile inizializzare le connessioni da qualsiasi macchina virtuale in VNET1 a qualsiasi macchina virtuale in VNET2 e viceversa.




<!--HONumber=Feb17_HO1-->


