<properties 
   pageTitle="Distribuire più macchine virtuali della scheda di interfaccia di rete tramite un modello in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuire più macchine virtuali della scheda di interfaccia di rete tramite un modello di Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
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

# Distribuire più macchine virtuali della scheda di interfaccia di rete tramite un modello

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poiché in questo momento non è possibile disporre di macchine virtuali con una singola scheda di interfaccia di rete e di macchine virtuali con più schede di interfaccia di rete nello stesso gruppo di risorse, i server back-end verranno implementati in un gruppo di risorse e tutti gli altri componenti in un altro gruppo di sicurezza. La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per il gruppo di risorse principale e *IaaSStory-BackEnd* per i server back-end.

## Prerequisiti

Prima di distribuire i server back-end, è necessario distribuire il gruppo di risorse principale con tutte le risorse necessarie per questo scenario. Per distribuire tali risorse, seguire questa procedura.

1. Passare alla [pagina del modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina del modello, a destra del **gruppo di risorse padre**, fare clic su **Deploy to Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di anteprima di Azure per distribuire il gruppo di risorse.

> [AZURE.IMPORTANT]Assicurarsi che i nomi degli account di archiviazione siano univoci. In Azure non sono infatti ammessi nomi di account di archiviazione duplicati.

## Informazioni sul modello di distribuzione

Prima di distribuire il modello fornito con questa documentazione, assicurarsi di comprenderne le caratteristiche. La procedura seguente fornisce un'utile panoramica del modello in questione.

1. Passare alla [pagina del modello](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC).
2. Fare clic su **azuredeploy.json** per aprire il file del modello.
3. Si noti il parametro *osType* elencato di seguito. Questo parametro viene usato per selezionare l'immagine di macchina virtuale da usare per il server di database e le impostazioni correlate del sistema operativo.

	    "osType": {
	      "type": "string",
	      "defaultValue": "Windows",
	      "allowedValues": [
	        "Windows",
	        "Ubuntu"
	      ],
	      "metadata": {
	        "description": "Type of OS to use for VMs: Windows or Ubuntu."
	      }
	    },

4. Scorrere fino all'elenco delle variabili e controllare la definizione per le variabili *dbVMSetting* elencate di seguito. Viene ricevuto uno degli elementi della matrice contenuti nella variabile *dbVMSettings*. Se si ha familiarità con la terminologia di sviluppo software, è possibile considerare la variabile *dbVMSettings* come una tabella hash o un dizionario.

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Si supponga di voler distribuire le macchine virtuali di Windows che eseguono SQL nel back-end. Quindi il valore per *osType* sarebbe *Windows* e la variabile *dbVMSetting* conterrà l'elemento elencato di seguito, che rappresenta il primo valore nella variabile *dbVMSettings*.

	      "Windows": {
	        "vmSize": "Standard_DS3",
	        "publisher": "MicrosoftSQLServer",
	        "offer": "SQL2014SP1-WS2012R2",
	        "sku": "Standard",
	        "version": "latest",
	        "vmName": "DB",
	        "osdisk": "osdiskdb",
	        "datadisk": "datadiskdb",
	        "nicName": "NICDB",
	        "ipAddress": "192.168.2.",
	        "extensionDeployment": "",
	        "avsetName": "ASDB",
	        "remotePort": 3389,
	        "dbPort": 1433
	      },

6. Si noti che *vmSize* contiene il valore *Standard\_DS3*. Solo alcune dimensioni di macchine virtuali consentono l'uso di più schede di interfaccia di rete. Per verificare quali siano tali dimensioni, è possibile consultare la [panoramica relativa all'uso di più schede di interfaccia di rete](virtual-networks-multiple-nics.md).
7. Scorrere le **risorse** e notare il primo elemento. Questo descrive un account di archiviazione che verrà usato per gestire i dischi dati usati da ogni macchina virtuale del database. In questo scenario, ogni macchina virtuale del database dispone di un disco del sistema operativo archiviato nell'archiviazione consueta e due dischi dati archiviati nell'archiviazione dell'unità SSD (premium).

	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Storage/storageAccounts",
	      "name": "[parameters('prmStorageName')]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "Storage Account - Premium"
	      },
	      "properties": {
	        "accountType": "[parameters('prmStorageType')]"
	      }
	    },

8. Scorrere fino alla risorsa successiva, come illustrato di seguito. Questa risorsa rappresenta la scheda di interfaccia di rete usata per l'accesso al database in ogni macchina virtuale del database. Si noti l'uso della funzione di **copia** in questa risorsa. Il modello consente di distribuire il numero di macchine virtuali che si desidera, in base al parametro *dbCount*. È pertanto necessario creare la stessa quantità di schede di interfaccia di rete per l'accesso al database, una per ogni macchina virtuale.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB DA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

9. Scorrere fino alla risorsa successiva, come illustrato di seguito. Questa risorsa rappresenta la scheda di interfaccia di rete usata per la gestione in ogni macchina virtuale del database. Ancora una volta, è necessaria una di queste schede di interfaccia di rete per ogni macchina virtuale del database. Si noti l'elemento *networkSecurityGroup*, che collega un gruppo di sicurezza di rete che consente l'accesso a RDP o SSH solo a tale scheda di interfaccia di rete.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB RA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "networkSecurityGroup": {
	                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
	              },
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

10. Scorrere fino alla risorsa successiva, come illustrato di seguito. Questa risorsa rappresenta un set di disponibilità condiviso tra tutte le macchine virtuali del database. In questo modo si garantisce che nel set sarà sempre presente una macchina virtuale in esecuzione durante la manutenzione.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. Scorrere fino alla risorsa successiva. Questa risorsa rappresenta le macchine virtuali del database, come illustrato nelle prime righe elencate di seguito. Si noti nuovamente l'uso della funzione di **copia**, assicurandosi che più macchine virtuali vengano create in base al parametro *dbCount*. Si noti inoltre la raccolta *dependsOn*. Vengono elencate due schede di interfaccia di rete che devono essere create prima che venga distribuita la macchina virtuale, insieme al set di disponibilità e all'account di archiviazione.

		  "apiVersion": "2015-06-15",
		  "type": "Microsoft.Compute/virtualMachines",
		  "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
		  "location": "[variables('location')]",
		  "dependsOn": [
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
		    "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
		    "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
		  ],
		  "tags": {
		    "displayName": "VMs - DB"
		  },
		  "copy": {
		    "name": "dbvmcount",
		    "count": "[parameters('dbCount')]"
		  },

12. Scorrere verso il basso nella risorsa della macchina virtuale fino all'elemento **networkProfile**, come illustrato di seguito. Si noti che sono presenti due schede di interfaccia di rete a cui ogni macchina virtuale fa riferimento. Quando si creano più schede di interfaccia di rete per una macchina virtuale, è necessario impostare la proprietà *primary* di una delle schede di interfaccia di rete su *true* e le restanti su *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## Distribuire il modello ARM tramite clic per la distribuzione

> [AZURE.IMPORTANT]Assicurarsi di seguire la procedura illustrata in [Prerequisiti](#Pre-requisites) prima delle istruzioni seguenti.

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Per distribuire questo modello mediante clic per la distribuzione, seguire [questo collegamento](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC), a destra di **Gruppo di risorse di back-end (vedere la documentazione)** fare clic su **Distribuisci in Azure**, sostituire i valori del parametro predefinito se necessario e seguire le istruzioni nel portale.

La figura seguente illustra i contenuti del nuovo gruppo di risorse, dopo la distribuzione.

![Gruppo di risorse di back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire questa procedura.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Per creare un gruppo di risorse usando il modello, eseguire il cmdlet **New-AzureRmResourceGroup**.

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'	

	Output previsto:

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                 Type                                 Location
		                    ===================  ===================================  ========
		                    ASDB                 Microsoft.Compute/availabilitySets   westus  
		                    DB1                  Microsoft.Compute/virtualMachines    westus  
		                    DB2                  Microsoft.Compute/virtualMachines    westus  
		                    NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
		                    wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## Distribuire il modello tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello tramite l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

3. Aprire il [file del parametro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selezionare i relativi contenuti e salvarlo in un file nel computer. In questo esempio il file dei parametri è stato salvato in *parameters.json*.

4. Eseguire il cmdlet **azure group deployment create** per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

	Output previsto:

		info:    Executing command group create
		+ Getting resource group IaaSStory-Backend
		+ Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_1203_2015-->