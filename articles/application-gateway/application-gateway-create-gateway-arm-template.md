
<properties 
   pageTitle="Creare il gateway applicazione usando i modelli di Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione di un gateway applicazione tramite un modello di Gestione risorse di Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Creare il gateway applicazione tramite il modello di Gestione risorse di Azure

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7. Fornisce servizi di failover e richieste HTTP di routing delle prestazioni tra server diversi, sia che si trovino sul cloud che in locale. Il gateway applicazione offre le funzionalità di distribuzione delle applicazioni seguenti: bilanciamento del carico HTTP, affinità delle sessioni basata sui cookie, offload SSL.

> [AZURE.SELECTOR]
- [Azure Classic Powershell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template steps](application-gateway-create-gateway-arm-template.md)


<BR>

Verrà illustrato come scaricare e modificare un modello di Gestione risorse di Azure esistente da GitHub e distribuire il modello da GitHub, PowerShell e dall'interfaccia della riga di comando di Azure.

Se si sta distribuendo semplicemente il modello di Gestione risorse di Azure direttamente da GitHub, senza alcuna modifica, andare al passaggio per distribuire un modello da github.


>[AZURE.IMPORTANT]Prima di lavorare con le risorse di Azure, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione delle risorse e Classico. È importante conoscere i [modelli e gli strumenti di distribuzione](azure-classic-rm.md) prima di usare le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustrerà la creazione di un gateway applicazione tramite Gestione risorse di Azure. Per usare la versione classica, passare all'articolo relativo alla [creazione di una distribuzione classica del gateway applicazione tramite PowerShell](application-gateway-create-gateway.md).




## Scenario

In questo scenario verrà creato quanto segue:

- Un gateway applicazione con due istanze.
- Una rete virtuale denominata VirtualNetwork1 con un blocco CIDR riservato 10.0.0.0/16.
- Una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
- I due indirizzi IP back-end di installazione precedentemente configurati per i server Web desiderati per bilanciare il carico del traffico. In questo esempio di modello verranno usati gli indirizzi IP back-end 10.0.1.10 e 10.0.1.11.

>[AZURE.NOTE]Questi sono i parametri per il modello. È possibile modificare le regole, il listener e il protocollo SSL aprendo il file azuredeploy.json per personalizzare il modello.



![arm-scenario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Scaricare e comprendere il modello ARM

È possibile scaricare il modello ARM esistente per la creazione di una rete virtuale e di due subnet da github, apportare le modifiche desiderate e riutilizzarlo. A tale scopo, seguire questa procedura:

1. Accedere a https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-applicationgateway-publicip.
2. Fare clic su **azuredeploy.json** e quindi su **RAW**.
3. Salvare il file in una cartella locale nel computer in uso.
4. Se si ha familiarità con i modelli ARM, procedere al passaggio 7.
5. Aprire il file appena salvato e visualizzare il contenuto in **parameters** nella riga 5. I parametri del modello ARM costituiscono un segnaposto per i valori che possono essere compilati durante la distribuzione.

	| Parametro | Descrizione |
	|---|---|
	| **Località** | Area di Azure in cui verrà creato il gateway applicazione |
	| **VirtualNetwork1** | Nome per la nuova rete virtuale |
	| **addressPrefix** | Spazio di indirizzi per la rete virtuale, nel formato CIDR |
	| **ApplicationGatewaysubnet** | Nome della subnet del gateway applicazione |
	| **subnetPrefix** | Blocco CIDR della subnet del gateway applicazione |
	| **skuname** | Dimensione dell'istanza SKU |
	| **capacità** | numero di istanze |
	| **backendaddress1** | Indirizzo IP del primo server Web |
	| **backendaddress2** | Indirizzo IP del secondo server Web|


>[AZURE.IMPORTANT]I modelli ARM conservati in github possono cambiare nel tempo. Assicurarsi di aver controllato il modello prima di utilizzarlo.
	
6. Controllare il contenuto in **resources** e prendere nota di quanto segue:

	- **type**. Tipo di risorsa che sarà creato dal modello. In questo caso, **Microsoft.Network/applicationGateways**, che rappresenta un gateway applicazione.
	- **name**. Nome della risorsa. Si noti l'uso di **[parameters('applicationGatewayName')]** con cui si indica che il nome verrà specificato come input dall'utente o tramite un file di parametri durante la distribuzione.
	- **properties**. Elenco di proprietà per la risorsa. Questo modello usa la rete virtuale e l'indirizzo IP pubblico durante la creazione del gateway applicazione.

7. Tornare a https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-applicationgateway-publicip.
8. Fare clic su **azuredeploy-parameters.json** e quindi su **RAW**.
9. Salvare il file in una cartella locale nel computer in uso.
10. Aprire il file appena salvato e modificare i valori per i parametri. Usare i valori riportati di seguito per la distribuzione del gateway applicazione descritto in questo scenario.

		{
		   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		   "contentVersion": "1.0.0.0",
		   "parameters": {
		     "location": {
		       "value": "East US"
		     },
		     "addressPrefix": {
		      "value": "10.0.0.0/16"
    		 },
		     "subnetPrefix": {
		      "value": "10.0.0.0/24"
		     },
		     "skuName": {
		       "value": "Standard_Small"
		     },
		     "capacity": {
		       "value": 2
		    },
		    "backendIpAddress1": {
		      "value": "10.0.1.10"
		    },
		     "backendIpAddress2": {
		       "value": "10.0.1.11"
		     }
		  }
		}

11. Salvare il file. È possibile testare il modello JSON e un modello di parametro usando strumenti di convalida online di JSON come [JSlint.com](http://www.jslint.com/).
 
## Distribuire il modello di Gestione risorse di Azure tramite PowerShell

1. Se è la prima volta che si usa Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Al prompt di Azure PowerShell eseguire il cmdlet **Switch-AzureMode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		Switch-AzureMode AzureResourceManager
	
Output previsto:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

>[AZURE.WARNING]Il cmdlet Switch-AzureMode verrà presto dichiarato obsoleto. Di conseguenza, tutti i cmdlet di Gestione risorse verranno rinominati.
	
3. Se necessario, creare un nuovo gruppo di risorse usando il cmdlet `New-AzureResourceGroup`. Nell'esempio seguente verrà creato un nuovo gruppo di risorse denominato AppgatewayRG nella località Stati Uniti orientali (eastus):

		PS C:\> New-AzureResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/################################/resourceGroups/AppgatewayRG

4. Eseguire il cmdlet New-AzureResourceGroupDeployment per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza.

		New-AzureResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		   -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

L'output generato dalla riga di comando sarà il seguente:

		DeploymentName    : testappgatewaydeployment
		ResourceGroupName : appgatewayRG
		ProvisioningState : Succeeded
		Timestamp         : 9/19/2015 1:49:41 AM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

		Outputs           :


## Distribuire il modello ARM tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello di Gestione risorse di Azure scaricato tramite l'interfaccia della riga di comando di Azure, seguire questa procedura.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.
2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		azure config mode arm

Di seguito è riportato l'output previsto per il comando precedente:

		info:	New mode is arm

3. Se necessario, eseguire il comando **azure group create** per creare un nuovo gruppo di risorse, come illustrato di seguito. Si noti l'output del comando. Nell'elenco riportato dopo l'output sono indicati i parametri usati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).

		azure group create -n appgatewayRG -l eastus

**-n (o --name)**. Nome del nuovo gruppo di risorse. Per questo scenario, *appgatewayRG*.

**-l (o --location)**. L'area di Azure in cui verrà creato il nuovo gruppo di risorse. Per questo scenario, *Eastus*.

4. Eseguire il cmdlet **azure group deployment create** per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Di seguito è riportato l'output previsto per il comando precedente:

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestAppgatewayDeployment"
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestAppgatewayDeployment
		data:    ResourceGroupName  : appgatewayRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
		data:    Mode               : Incremental
		data:    Name               Type    Value
		data:    -----------------  ------  --------------
		data:    location           String  East US
		data:    addressPrefix      String  10.0.0.0/16
		data:    subnetPrefix       String  10.0.0.0/24	
		data:    skuName            String  Standard_Small
		data:    capacity           Int     2
		data:    backendIpAddress1  String  10.0.1.10
		data:    backendIpAddress2  String  10.0.1.11
		info:    group deployment create command OK

**-g (o --resource-group)**. Nome del gruppo di risorse in cui verrà creata la nuova rete virtuale.

**-f (o --template-file)**. Percorso del file di modello ARM.

**-e (o --parameters-file)**. Percorso del file di parametri ARM.

## Distribuire il modello di Gestione risorse di Azure tramite clic per la distribuzione

Un altro uso dei modelli di Gestione risorse di Azure consiste nel fare clic per la distribuzione. Questo è un modo semplice di usare i modelli con il portale di Azure.


### Passaggio 1 
Il collegamento che indica di [fare clic per distribuire il gateway applicazione](http://azure.microsoft.com/documentation/templates/101-create-applicationgateway-publicip/) reindirizza alla pagina del modello del portale per il gateway applicazione.


### Passaggio 2 

Fare clic su "Distribuisci in Azure".

![arm-scenario](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Passaggio 3

Compilare i parametri per il modello di distribuzione nel portale e fare clic su OK.

![arm-scenario](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Passaggio 4

Selezionare "Note legali" e fare clic su "Acquista".

### Passaggio 5

Nel pannello "Distribuzione personalizzata" fare clic su "Crea".


 
## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione per l'uso con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO3-->