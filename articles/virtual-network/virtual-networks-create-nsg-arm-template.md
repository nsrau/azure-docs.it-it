<properties 
   pageTitle="Come creare gruppi di sicurezza di rete in modalità ARM usando un modello | Microsoft Azure"
   description="Informazioni su come creare e distribuire gruppi di sicurezza di rete in ARM utilizzando un modello"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

# Come creare NSG utilizzando un modello

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È inoltre possibile [creare NSG nel modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Risorse NSG in un file di modello

È possibile visualizzare e scaricare il [modello di esempio](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/).

La sezione seguente illustra la definizione di NSG front-end in base allo scenario precedente.

      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('frontEndNSGName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "NSG - Front End"
      },
      "properties": {
        "securityRules": [
          {
            "name": "rdp-rule",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 100,
              "direction": "Inbound"
            }
          },
          {
            "name": "web-rule",
            "properties": {
              "description": "Allow WEB",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 101,
              "direction": "Inbound"
            }
          }
        ]
      }

Per associare l’NSG alla subnet front-end, è necessario modificare la definizione della subnet nel modello e utilizzare l'id di riferimento per l’NSG.

        "subnets": [
          {
            "name": "[parameters('frontEndSubnetName')]",
            "properties": {
              "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
              }
            }
          }, ...

Si noti che va effettuata la stessa operazione per l’NSG back-end della subnet back-end nel modello.

## Distribuire il modello ARM tramite clic per la distribuzione

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Distribuire [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG) tramite clic per la distribuzione, fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale.

## Distribuire il modello ARM tramite PowerShell

Per distribuire il modello ARM scaricato tramite PowerShell, attenersi alla procedura seguente.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.

3. Per creare un gruppo di risorse usando il modello, eseguire il cmdlet **New-AzureRmResourceGroup**.

		New-AzureRmResourceGroup -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'	

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
		                    sqlAvSet            Microsoft.Compute/availabilitySets       westus  
		                    webAvSet            Microsoft.Compute/availabilitySets       westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    Web1                Microsoft.Compute/virtualMachines        westus  
		                    Web2                Microsoft.Compute/virtualMachines        westus  
		                    TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
		                    TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
		                    TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
		                    TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
		                    TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
		                    TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
		                    TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure, seguire la procedura di seguito.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

4. Eseguire il cmdlet **azure group deployment create** per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

	Output previsto:

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Creating resource group TestRG
		info:    Created resource group TestRG
		info:    Initializing template configurations and parameters
		info:    Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    
		info:    group create command OK

	- **-n (or --name)**. Nome del gruppo di risorse da creare.
	- **-l (or --location)**. L'area di Azure in cui verrà creato il gruppo di risorse.
	- **-f (or --template-file)**. Percorso del file di modello ARM.
	- **-e (or --parameters-file)**. Percorso del file di parametri ARM.

<!---HONumber=AcomDC_1203_2015-->