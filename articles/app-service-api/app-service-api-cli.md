<properties
	pageTitle="Interfaccia della riga di comando di Azure e app per le API | Microsoft Azure"
	description="Come usare l'interfaccia della riga di comando di Microsoft Azure per Mac, Linux e Windows con le app per le API di Azure API."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2015"
	ms.author="tdykstra"/>

# Interfaccia della riga di comando di Azure e app per le API

Questo articolo illustra come creare, gestire ed eliminare le app per le API nel servizio app di Azure mediante l'interfaccia della riga di comando di Azure per Mac, Linux e Windows.

## Prerequisiti

Questo articolo presuppone che sia stata installata l'interfaccia della riga di comando di Azure e che si sia in grado di eseguire le attività di base. Per un'introduzione all'interfaccia della riga di comando, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli.md).

> [AZURE.NOTE]Le istruzioni per la [connessione a una sottoscrizione di Azure](../xplat-cli-connect.md) offrono due alternative, ovvero l'accesso tramite un account aziendale o dell'istituto di istruzione e il download di un file con estensione *publishsettings*. Il metodo di autenticazione tramite il file con estensione *publishsettings* non funzionerà per le app per le API. È infatti necessario usare la modalità Gestione risorse (illustrata nella sezione successiva) per le app per le API e il metodo di autenticazione tramite il file con estensione *publishsettings* non funziona con Gestione risorse.

## Abilitare la modalità Gestione risorse

L'interfaccia della riga di comando può essere usata in modalità [Gestione dei servizi (asm)](../virtual-machines/virtual-machines-command-line-tools.md) o in modalità [Gestione risorse (arm)](../xplat-cli-azure-resource-manager.md). Per le app per le API è necessario usare la modalità Gestione risorse. Poiché la modalità `arm` non è abilitata per impostazione predefinita, usare il comando `config mode arm` per abilitarla.

	azure config mode arm

## Elencare i comandi disponibili per l'utilizzo delle app per le API

Per visualizzare tutti i comandi attualmente disponibili per l'utilizzo delle app per le API, eseguire il comando `apiapp`.

	azure apiapp

## Elencare tutte le app per le API in una sottoscrizione o in un gruppo di risorse

Per elencare tutte le app per le API disponibili nella sottoscrizione, eseguire il comando `apiapp list` senza parametri.

	azure apiapp list

L'elenco mostra il gruppo di risorse, il nome dell'app per le API, l'ID del pacchetto e l'URL di ogni app per le API.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Per limitare l'elenco a un gruppo di risorse specificato, aggiungere il parametro relativo al gruppo (`-g`).

	azure apiapp list -g <resource group name>

Ad esempio:

	azure apiapp list -g mygroup

Per aggiungere all'elenco la versione dell'app per le API e le informazioni sul livello di accesso, aggiungere il parametro relativo ai dettagli (`-d`).

	azure apiapp list -d

L'output `list` con i campi aggiuntivi avrà un aspetto analogo all'esempio seguente:

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Elencare i dettagli relativi a un'app per le API

Per elencare i dettagli per un'app per le API, usare il comando `apiapp show` con i parametri relativi al gruppo (`-g`) e al nome dell'app per le API (`-n`).

	azure apiapp show -g <resource group name> -n <API app name>

Ad esempio:

	azure apiapp show -g mygroup -n SimpleDropbox

L'output è simile al seguente:

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Creare un'app per le API

È possibile creare un'app per le API in due modi. È possibile usare comandi imperativi dell'interfaccia della riga di comando per creare singolarmente le risorse di Azure oppure si può usare la sintassi dichiarativa in un modello per definire contemporaneamente tutte le risorse necessarie e distribuire tale modello con un comando dell'interfaccia della riga di comando. Per l'approccio dichiarativo, vedere [Passaggi successivi](#next-steps).

Per creare un'app per le API usando l'approccio imperativo, eseguire i passaggi seguenti:

1. Scegliere una posizione valida
1. Creare o trovare un gruppo di risorse da usare
2. Creare o trovare un piano del servizio app da usare
4. Creare l'app per le API

### Scegliere una posizione valida

Quando si crea un gruppo di risorse, è necessario specificare una posizione. Ecco alcune delle posizioni valide per le app per le API.

* Stati Uniti Orientali
* Stati Uniti occidentali
* Stati Uniti centro-meridionali
* Europa settentrionale
* Asia orientale
* Giappone orientale
* Europa occidentale
* Asia sudorientale
* Giappone occidentale
* Stati Uniti centro-settentrionali
* Stati Uniti centrali
* Brasile meridionale
* Stati Uniti orientali 2

Per ottenere un elenco completo e aggiornato dell'elenco di posizioni, usare il comando `location list` e verificare la riga `Microsoft.AppService/apiapps` relativa al provider di risorse.

	azure location list

Ecco l'output di esempio del comando `location list`.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Creare o trovare un gruppo di risorse da usare

Per creare un gruppo di risorse, usare il comando `group create` con i parametri relativi al nome (`n`) e alla posizione (`l`).

	azure group create -n <name> -l <location>

Ad esempio:

	azure group create -n "mygroup" -l "West US"

Per trovare un gruppo di risorse esistente, usare il comando `group list` e scegliere un gruppo di risorse in una posizione valida per le app per le API.

	azure group list

### Creare o trovare un piano del servizio app da usare

Per creare un piano del servizio app, usare il comando `resource create` e quindi usare il parametro relativo al tipo di risorsa (`-r`) per specificare che il tipo di risorsa da creare è un piano del servizio app.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Ad esempio:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

La stringa JSON per il parametro `properties` (`-p`) è obbligatoria a causa di alcune modifiche recenti apportate all'API REST. In futuro il parametro `-p` sarà facoltativo.

Il comando di esempio specifica la versione più recente dell'API rispetto alla data in cui è stato scritto questo articolo. Per verificare se è disponibile una versione più recente, usare il comando `provider show` e verificare la matrice `apiVersions` per l'oggetto `sites` nella matrice `resourceTypes`.

	azure provider show -n Microsoft.Web --json
   
Ecco un esempio dell'oggetto `sites` nell'output del comando.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Per elencare i piani esistenti del servizio app, usare il comando `resource list` e specificare il piano del servizio app come tipo di risorsa tramite il parametro `-r`.

	azure resource list -r Microsoft.Web/serverfarms

L'output è simile al seguente:

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Creare un'app per le API vuota (personalizzata)

Per creare un'app per le API vuota, ovvero un'app per cui si scriverà personalmente il codice, usare il comando `apiapp create` e specificare il pacchetto NuGet `Microsoft.ApiApp` (parametro `-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Ad esempio:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

L'output segnala l'avanzamento durante la creazione dell'app per le API.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Creare un'app per le API da Marketplace

Per ottenere un elenco di pacchetti di app per le API disponibili in Marketplace, usare il comando `group template list` e specificare le app per le API tramite il parametro relativo alla categoria (`-c`).

	azure group template list -c apiapps

L'output è simile all'esempio seguente:

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Per creare un'app per le API da Marketplace, usare il comando `apiapap create` e specificare il nome dell'app per le API da creare mediante il parametro relativo al pacchetto NuGet (`-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

Il valore da usare per il parametro `-u` corrisponde alla sezione intermedia del nome disponibile in Marketplace. Ad esempio, per microsoft\_com.MicrosoftSqlConnector.0.2.2 il comando ha un aspetto analogo al seguente:

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Se sono necessari parametri del modello di app per le API, ad esempio nomi del server e stringhe di connessione per un connettore SQL, i dati necessari verranno richiesti. È possibile usare `--parameters` o `--parameters-file` per passare i valori dei parametri. Per altre informazioni sui file dei parametri, vedere [Eseguire il provisioning di un'app per le API con un nuovo gateway](app-service-api-arm-new-gateway-provision.md).

## Passaggi successivi

Questo articolo ha illustrato come usare singoli comandi dell'interfaccia della riga di comando di Azure CLI per utilizzare le app per le API personalizzate o app per le API create da modelli di Marketplace. Per informazioni su come usare i modelli personalizzati per automatizzare la creazione delle app per le API, vedere le risorse seguenti:

* [Eseguire il provisioning di un'app per le API con un gateway esistente](app-service-api-arm-existing-gateway-provision.md)
* [Eseguire il provisioning di un'app per le API con un nuovo gateway](app-service-api-arm-new-gateway-provision.md)

Per altre informazioni su come usare le utilità della riga di comando di Azure con Gestione risorse di Azure, vedere le risorse seguenti:
 
* [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md).
* [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)
 

<!---HONumber=August15_HO6-->