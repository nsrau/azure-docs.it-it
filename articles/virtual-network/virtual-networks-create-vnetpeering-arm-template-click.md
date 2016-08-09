<properties
   pageTitle="Creare un peering reti virtuali usando modelli di Resource Manager | Microsoft Azure"
   description="Informazioni su come creare un peering reti virtuali con i modelli in Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Creare un peering reti virtuali usando modelli di Resource Manager

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un peering reti virtuali con i modelli di Resource Manager, seguire questa procedura:

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

Nota: il cmdlet di PowerShell per la gestione di un peering reti virtuali viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. La sezione seguente illustra la definizione del collegamento per il peering reti virtuali da VNet1 a VNet2 in base allo scenario precedente. Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet1.json

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
    

3. La sezione seguente illustra la definizione del collegamento per il peering reti virtuali da VNet2 a VNet1 in base allo scenario precedente. Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet2.json

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

Come si può vedere nel modello precedente, esistono alcune proprietà configurabili per il peering reti virtuali:

|Opzione|Descrizione|Default|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Indica se lo spazio indirizzi della rete virtuale peer deve essere incluso come parte del tag Virtual\_network.|Sì|
|AllowForwardedTraffic|Consente di accettare o eliminare il traffico che non ha origine dalla rete virtuale con peering.|No|
|AllowGatewayTransit|Consente alla rete virtuale peer di usare il gateway di rete virtuale.|No|
|UseRemoteGateways|Consente di usare il gateway di rete virtuale del peer. Per la rete virtuale peer deve essere configurato un gateway ed essere selezionata l'opzione AllowGatewayTransit. Non è possibile usare questa opzione se si ha un gateway configurato.|No|

Ogni collegamento nel peering reti virtuali include un set delle proprietà precedenti. Ad esempio, è possibile impostare AllowVirtualNetworkAccess su True per il collegamento per il peering reti virtuali da VNet1 a VNet2 e su False per il collegamento per il peering reti virtuali nella direzione opposta.


4. Per distribuire il file di modello, è possibile eseguire il cmdlet New-AzureRmResourceGroupDeployment per creare o aggiornare la distribuzione. Per altre informazioni sull'uso di un modello di Resource Manager, vedere questo [articolo](../resource-group-template-deploy.md)

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

> [AZURE.NOTE] Sostituire il nome del gruppo di risorse e il file modello in base alle esigenze.

Di seguito è che riportato un esempio basato sullo scenario precedente:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

Ecco l'output:

        DeploymentName		: VNetPeeringVNet1
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:05:03 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

Ecco l'output:

        DeploymentName		: VNetPeeringVNet2
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:07:22 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Al termine della distribuzione è possibile eseguire il cmdlet seguente per visualizzare lo stato del peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Ecco l'output:

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: VNet101
        VirtualNetworkName	: VNet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Dopo aver stabilito il peering in questo scenario, sarà possibile avviare le connessioni da qualsiasi macchina virtuale a qualsiasi macchina virtuale di entrambe le reti virtuali. Per impostazione predefinita, AllowVirtualNetworkAccess è impostato su True e il peering reti virtuali effettuerà il provisioning degli ACL appropriati per consentire la comunicazione tra le reti virtuali. È comunque possibile applicare le regole del gruppo di sicurezza di rete per bloccare la connettività, ad esempio tra una subnet o una macchina virtuale specifica per ottenere il controllo granulare dell'accesso tra due reti virtuali. Per altre informazioni sulla creazione di regole del gruppo di sicurezza di rete, vedere questo [articolo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare un peering reti virtuali tra sottoscrizioni, seguire questa procedura:

1. Accedere ad Azure come utente A con privilegi per la sottoscrizione A ed eseguire il cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Questo non è un requisito, perché il peering può essere stabilito anche se gli utenti generano singolarmente richieste di peering per le rispettive reti virtuali, purché le richieste corrispondano. L'aggiunta di utenti con privilegi dell'altra rete virtuale come utenti nella rete virtuale locale facilita la configurazione.

2. Accedere ad Azure come utente B con privilegi per la sottoscrizione B ed eseguire il cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Nella sessione di accesso dell'utente A eseguire quindi il cmdlet

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Ecco come viene definito il file JSON.
    
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
   
4. Nella sessione di accesso dell'utente B eseguire quindi il cmdlet

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
   Ecco come viene definito il file JSON:

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
 
 Dopo aver stabilito il peering in questo scenario, sarà possibile avviare le connessioni da qualsiasi macchina virtuale a qualsiasi macchina virtuale di entrambe le reti virtuali nelle diverse sottoscrizioni.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario è possibile distribuire il modello di esempio seguente per stabilire il peering reti virtuali. In particolare è necessario impostare la proprietà AllowForwardedTraffic su True, che consente all'appliance di rete virtuale nella rete virtuale con peering di inviare e ricevere traffico. Ecco il modello per la creazione di un peering reti virtuali da HubVNet a VNet1. Si noti che AllowForwardedTraffic è impostato su false.

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
            }
        ]
        }

2. Ecco il modello per la creazione di un peering reti virtuali da VNet1 a HubVnet. Si noti che AllowForwardedTraffic è impostato su true.

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


3. Dopo aver stabilito il peering, è possibile vedere questo [articolo](virtual-network-create-udr-arm-ps.md) e creare route definite dall'utente per reindirizzare il traffico di VNet1 attraverso un'appliance virtuale per usarne le funzionalità. Quando si specifica l'indirizzo hop successivo nella route, è possibile impostarlo sull'indirizzo IP dell'appliance virtuale nella rete virtuale peer HubVNet.

<!---HONumber=AcomDC_0803_2016-->