<properties
   pageTitle="Distribuire una VM con un IP pubblico statico con un modello in Gestione risorse | Microsoft Azure"
   description="Scoprire come distribuire le VM con un IP pubblico statico tramite un modello in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="04/27/2016"
   ms.author="jdial" />  

# Distribuire una VM con un IP pubblico statico tramite un modello

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Risorse IP pubblico in un file di modello

È possibile visualizzare e scaricare il [modello di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La sezione seguente illustra la definizione della risorsa IP pubblico in base allo scenario precedente.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Notare la proprietà **publicIPAllocationMethod** impostata su *Statico*. Questa proprietà può essere *Dinamico* (valore predefinito) o *Statico*. Impostarla su Static garantisce che l'indirizzo IP pubblico assegnato non verrà mai modificato.

La sezione seguente illustra l'associazione dell'IP pubblico con un'interfaccia di rete.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Notare che la proprietà **publicIPAddress** punta all'**Id** di una risorsa denominata **variables('webVMSetting').pipName**. È il nome della risorsa IP pubblico illustrata sopra.

Infine, l'interfaccia di rete di cui sopra è elencata nella proprietà **networkProfile** della VM in fase di creazione.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Distribuire il modello tramite clic per la distribuzione

Il modello di esempio disponibile nel repository pubblico usa un file di parametro che contiene i valori predefiniti usati per generare lo scenario descritto in precedenza. Per distribuire questo modello tramite clic per la distribuzione, fare clic su **Deploy to Azure** (Distribuisci in Azure) nel file Readme.md per il modello [VM with static PIP](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) modello(VM con PIP statico). Se richiesto, sostituire i valori predefiniti dei parametri e immettere i valori per i parametri vuoti. Seguire le istruzioni nel portale per creare una macchina virtuale con un indirizzo IP pubblico statico.

## Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire questa procedura.

1. Se si usa Azure PowerShell per la prima volta, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) e seguire le istruzioni nei passaggi da 1 a 3.

2. In una console PowerShell, eseguire il cmdlet **New-AzureRmResourceGroup** per creare un nuovo gruppo di risorse, se necessario. Se è già stato creato un gruppo di risorse, andare al passaggio 3.

		New-AzureRmResourceGroup -Name PIPTEST -Location westus

	Output previsto:

		ResourceGroupName : PIPTEST
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Nella console di PowerShell, eseguire il cmdlet **New-AzureRmResourceGroupDeployment** per distribuire il modello.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	Output previsto:

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : <Deployment date> <Deployment time>
		Mode              : Incremental
		TemplateLink      :
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0

		Parameters        :
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser
		                    adminPassword             SecureString                         

		Outputs           :

## Distribuire il modello tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello tramite l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Se non si è mai usata l'interfaccia della riga di comando di Azure, seguire la procedura nell'articolo [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e quindi la procedura di connessione dell'interfaccia della riga di comando alla sottoscrizione nella sezione "Usare azure login per autenticarsi interattivamente" dell'articolo [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

	Di seguito è riportato l'output previsto per il comando precedente:

		info:    New mode is arm

3. Aprire il [file dei parametri](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), selezionarne il contenuto e salvarlo in un file nel computer in uso. Per questo esempio, i parametri vengono salvati in un file denominato *parameters.json*. Modificare i valori dei parametri all'interno del file, se richiesto, ma, come minimo, è consigliabile modificare il valore del parametro adminPassword in una password complessa e univoca.

4. Eseguire il cmdlet **azure group deployment create** per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza. Nel comando seguente sostituire <path> con il percorso in cui è stato salvato il file.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

	Output previsto (elenca i valori usati per i parametri):

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0810_2016-->