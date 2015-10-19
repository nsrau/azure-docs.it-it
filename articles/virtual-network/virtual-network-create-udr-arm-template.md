<properties 
   pageTitle="Controllare il routing e usare dispositivi virtuali di rete in Gestione risorse mediante un modello | Microsoft Azure"
   description="Informazioni su come controllare il routing e usare dispositivi virtuali di rete in Azure mediante modelli"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/30/2015"
   ms.author="telmos" />

#Creare route definite dall'utente (UDR) mediante un modello

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È possibile anche [creare route definite dall'utente nel modello di distribuzione classica](virtual-networks-udr-how-to.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## Risorse UDR in un file di modello

È possibile visualizzare e scaricare il [modello di esempio](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/).

La sezione seguente illustra la definizione di route definita dall'utente front-end nel file azuredeploy-vnet-nsg-udr.json, in base allo scenario precedente.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/routeTables",
	"name": "[parameters('frontEndRouteTableName')]",
	"location": "[resourceGroup().location]",
	"tags": {
	  "displayName": "UDR - FrontEnd"
	},
	"properties": {
	  "routes": [
	    {
	      "name": "RouteToBackEnd",
	      "properties": {
	        "addressPrefix": "[parameters('backEndSubnetPrefix')]",
	        "nextHopType": "VirtualAppliance",
	        "nextHopIpAddress": "[parameters('vmaIpAddress')]"
	      }
	    }
	  ]


Per associare la route definita dall'utente alla subnet front-end, è necessario modificare la definizione della subnet nel modello e usare l'ID di riferimento per la route.

	"subnets": [
		"name": "[parameters('frontEndSubnetName')]",
		"properties": {
		  "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
		  "networkSecurityGroup": {
		    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
		  },
		  "routeTable": {
		      "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
		  }
		},
		...]

Si noti che va effettuata la stessa operazione per l'NSG back-end della subnet back-end nel modello.

È anche necessario assicurarsi che nella macchina virtuale **FW1** la proprietà di inoltro dell'indirizzo IP sia attivata nella scheda di interfaccia di rete che verrà usata per ricevere e inoltrare i pacchetti. La sezione seguente illustra la definizione della scheda di interfaccia di rete per FW1 nel file azuredeploy-nsg-udr.json, in base allo scenario precedente.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/networkInterfaces",
	"location": "[variables('location')]",
	"tags": {
	  "displayName": "NetworkInterfaces - DMZ"
	},
	"name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
	"dependsOn": [
	  "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
	  "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
	],
	"properties": {
	  "ipConfigurations": [
	    {
	      "name": "ipconfig1",
	      "properties": {
	        "enableIPForwarding": true,
	        "privateIPAllocationMethod": "Static",
	        "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
	        "publicIPAddress": {
	          "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
	        },
	        "subnet": {
	          "id": "[variables('dmzSubnetRef')]"
	        }
	      }
	    }
	  ]
	},
	"copy": {
	  "name": "fwniccount",
	  "count": "[parameters('fwCount')]"
	}

## Distribuire il modello ARM tramite clic per la distribuzione

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Distribuire questo modello tramite clic per la distribuzione, selezionare [questo collegamento](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR), fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale.

## Distribuire il modello ARM tramite PowerShell

Per distribuire il modello ARM scaricato tramite PowerShell, attenersi alla procedura seguente.

1. Se è la prima volta che si usa Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Eseguire il cmdlet **Switch-AzureMode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		Switch-AzureMode AzureResourceManager

	Di seguito è riportato l'output previsto per il comando precedente:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Il cmdlet Switch-AzureMode verrà presto dichiarato obsoleto. Di conseguenza, tutti i cmdlet di Gestione risorse verranno rinominati.

3. Per creare un gruppo di risorse usando il modello, eseguire il cmdlet **New-AzureResourceGroup**.

		New-AzureResourceGroup -Name TestRG -Location westus `
		    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json'	

	Output previsto:

		ResourceGroupName : TestRG
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                Type                                     Location
		                    ==================  =======================================  ========
		                    ASFW                Microsoft.Compute/availabilitySets       westus  
		                    ASSQL               Microsoft.Compute/availabilitySets       westus  
		                    ASWEB               Microsoft.Compute/availabilitySets       westus  
		                    FW1                 Microsoft.Compute/virtualMachines        westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    WEB1                Microsoft.Compute/virtualMachines        westus  
		                    WEB2                Microsoft.Compute/virtualMachines        westus  
		                    NICFW1              Microsoft.Network/networkInterfaces      westus  
		                    NICSQL1             Microsoft.Network/networkInterfaces      westus  
		                    NICSQL2             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB1             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB2             Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    PIPFW1              Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
		                    UDR-BackEnd         Microsoft.Network/routeTables            westus  
		                    UDR-FrontEnd        Microsoft.Network/routeTables            westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG

## Distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure, seguire la procedura di seguito.

1. Se è la prima volta che si usa l'interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

3. Dal browser passare a ****https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**, copiare il contenuto del file JSON e incollarlo in un nuovo file nel computer. Per questo scenario, copiare i valori seguenti in un file denominato **c:\\udr\\azuredeploy.parameters.json**.

		{
		  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "fwCount": {
		      "value": 1
		    },
		    "webCount": {
		      "value": 2
		    },
		    "sqlCount": {
		      "value": 2
		    }
		  }
		}

4. Eseguire il cmdlet **azure group create** per distribuire la nuova rete virtuale usando il modello e i file dei parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

	Output previsto:

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Updating resource group TestRG
		info:    Updated resource group TestRG
		info:    Initializing template configurations and parameters
		info:    Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    
		info:    group create command OK

5. Eseguire il comando **azure group show** per visualizzare le risorse create nel nuovo gruppo di risorse.

		azure group show TestRG

	Risultato previsto
		
		info:    Executing command group show
		info:    Listing resource groups
		info:    Listing resources for the group
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    Resources:
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/availabilitySets/ASFW
		data:      Name    : ASFW
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - DMZ
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/availabilitySets/ASSQL
		data:      Name    : ASSQL
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/availabilitySets/ASWEB
		data:      Name    : ASWEB
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/virtualMachines/FW1
		data:      Name    : FW1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - DMZ
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/virtualMachines/SQL1
		data:      Name    : SQL1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/virtualMachines/SQL2
		data:      Name    : SQL2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/virtualMachines/WEB1
		data:      Name    : WEB1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Compute/virtualMachines/WEB2
		data:      Name    : WEB2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkInterfaces/NICFW1
		data:      Name    : NICFW1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - DMZ
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkInterfaces/NICSQL1
		data:      Name    : NICSQL1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkInterfaces/NICSQL2
		data:      Name    : NICSQL2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkInterfaces/NICWEB1
		data:      Name    : NICWEB1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkInterfaces/NICWEB2
		data:      Name    : NICWEB2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkSecurityGroups/NSG-BackEnd
		data:      Name    : NSG-BackEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Front End
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:      Name    : NSG-FrontEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Remote Access
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/publicIPAddresses/PIPFW1
		data:      Name    : PIPFW1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - DMZ
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/publicIPAddresses/PIPSQL1
		data:      Name    : PIPSQL1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/publicIPAddresses/PIPSQL2
		data:      Name    : PIPSQL2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/publicIPAddresses/PIPWEB1
		data:      Name    : PIPWEB1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/publicIPAddresses/PIPWEB2
		data:      Name    : PIPWEB2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/routeTables/UDR-BackEnd
		data:      Name    : UDR-BackEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=Route Table - Back End
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/routeTables/UDR-FrontEnd
		data:      Name    : UDR-FrontEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=UDR - FrontEnd
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Network/virtualNetworks/TestVNet
		data:      Name    : TestVNet
		data:      Type    : virtualNetworks
		data:      Location: westus
		data:      Tags    : displayName=VNet
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Storage/storageAccounts/testvnetstorageprm
		data:      Name    : testvnetstorageprm
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Premium
		data:    
		data:      Id      : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mi
		crosoft.Storage/storageAccounts/testvnetstoragestd
		data:      Name    : testvnetstoragestd
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Simple
		data:    
		data:    Permissions:
		data:      Actions: *
		data:      NotActions: 
		data:    
		info:    group show command OK

>[AZURE.TIP]Se non è possibile visualizzare tutte le risorse, eseguire il comando **azure group deployment show** per assicurarsi che lo stato di provisioning della distribuzione sia *Riuscito*.

<!---HONumber=Oct15_HO2-->