<properties 
	pageTitle="Azure PowerShell con Gestione risorse | Microsoft Azure" 
	description="Introduzione all'uso di Azure PowerShell per distribuire più risorse come gruppo di risorse in Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Uso di Azure PowerShell con Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Con Gestione risorse di Azure è stato introdotto un metodo completamente nuovo per gestire le risorse di Azure. Invece di creare e gestire le singole risorse, si inizia immaginando un'intera soluzione, ad esempio un blog, una raccolta foto, un portale di SharePoint o un wiki. Si usa un modello (una rappresentazione dichiarativa della soluzione) per creare un gruppo di risorse contenente tutte le risorse necessarie per supportare la soluzione. A questo punto, si gestisce e distribuisce il gruppo di risorse come unità logica.

In questa esercitazione viene descritto come usare Azure PowerShell con Gestione risorse di Azure. Verrà illustrato il processo di creazione e distribuzione di un gruppo di risorse per un'app Web ospitata in Azure con un database SQL, completo di tutte le risorse necessarie per supportarlo.

## Prerequisiti

Per completare questa esercitazione, sono necessari:

- Un account Azure
  + È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.
  
  + È possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
- Azure PowerShell 1.0. Per informazioni su questa versione e su come installarla, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, quali moduli, cmdlet e sessioni.

## Elementi distribuiti

In questa esercitazione si userà Azure PowerShell per distribuire un'app Web e un database SQL. Questa soluzione con l'app Web e il database SQL, tuttavia, è costituita da diversi tipi di risorsa integrati. Le risorse effettive che verranno distribuite sono:

- Server SQL: per ospitare il database
- Database SQL: per archiviare i dati
- Regole del firewall: per consentire all'app Web di connettersi al database
- Piano di servizio app: per definire le funzionalità e il costo dell'app Web
- Sito Web: per eseguire l'app Web
- Configurazione Web: per archiviare la stringa di connessione al database 

## Ottenere la guida per i cmdlet

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Get-AzureRmResource, digitare:

	Get-Help Get-AzureRmResource -Detailed

Per un elenco dei cmdlet del modulo Resources con un riepilogo di supporto, digitare:

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

L'output sarà simile al seguente:

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Per informazioni complete per un cmdlet, digitare un comando con il formato seguente:

	Get-Help <cmdlet-name> -Full
  
## Accedere al proprio account Azure

Prima di usare la soluzione, è necessario accedere al proprio account.

Per accedere al proprio account Azure, usare il cmdlet **Login-AzureRmAccount**.

    PS C:\> Login-AzureRmAccount

Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

Le impostazioni dell'account hanno una scadenza, quindi è necessario aggiornarle regolarmente. Per aggiornare le impostazioni dell'account, eseguire di nuovo **Login-AzureRmAccount**.

>[AZURE.NOTE] I moduli di Gestione risorse richiedono Login-AzureRmAccount. Non è sufficiente un file di impostazioni di pubblicazione.

## Recuperare la posizione dei tipi di risorsa

Quando si distribuisce una risorsa, è necessario specificare dove ospitarla. Non tutte le aree supportano ogni tipo di risorsa. Prima di distribuire l'app Web e il database SQL, è necessario sapere quali aree supportano tali tipi. Anche se un gruppo di risorse può contenere risorse che si trovano in aree diverse, quando è possibile, è consigliabile creare le risorse nella stessa posizione per ottimizzare le prestazioni. In particolare, è consigliabile assicurarsi che il database si trovi nello stesso percorso dell'applicazione che esegue l'accesso.

Per recuperare le posizioni che supportano ciascun tipo di risorsa, sarà necessario usare il cmdlet **Get-AzureRmResourceProvider**. Prima di tutto, ecco che cosa restituisce questo comando:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace rappresenta una raccolta di tipi di risorsa correlati. Questi spazi dei nomi in genere sono perfetti per i servizi che si vuole creare in Azure. Per usare un provider di risorse elencato come **Unregistered**, è possibile registrarlo eseguendo il cmdlet **Register-AzureRmResourceProvider** e specificando lo spazio dei nomi del provider da registrare. Molto probabilmente, il provider di risorse da usare in questa esercitazione sarà già registrato per la sottoscrizione.

Per ottenere altri dettagli su un provider, specificare tale spazio dei nomi:

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Per limitare l'output alle posizioni supportate per un tipo specifico di risorsa, ad esempio i siti Web, usare:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
L'output sarà analogo al seguente:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Le posizioni visualizzate potrebbero essere leggermente diverse dai risultati precedenti. I risultati potrebbero essere diversi perché un amministratore dell'organizzazione ha creato un criterio che limita le aree che possono essere usate nella sottoscrizione o perché possono esistere restrizioni correlate alle politiche fiscali della propria sede contrattuale.

Provare a eseguire lo stesso comando per il database:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Sembra che queste risorse siano disponibili in più aree. Per questo argomento verrà usata l'area **West US**, ma è possibile specificare una qualsiasi delle aree supportate.

## Creare un gruppo di risorse

Questa sezione dell'esercitazione descrive il processo di creazione di un gruppo di risorse. Il gruppo di risorse verrà usato come contenitore per tutte le risorse della soluzione che condividono lo stesso ciclo di vita. Più avanti nell'esercitazione, si distribuiranno l'app Web e il database SQL in questo gruppo di risorse.

Per creare un gruppo di risorse, usare il cmdlet **New-AzureRmResourceGroup**.

Il comando utilizza il parametro **Name** per specificare un nome per il gruppo di risorse e il parametro **Location** per specificarne la posizione. In base a quanto stabilito nella sezione precedente, verrà usata la posizione "West US".

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Il gruppo di risorse è stato creato.


## Ottenere le versioni dell'API disponibili per le risorse

Quando si distribuisce un modello, è necessario specificare una versione dell'API da usare per la creazione della risorsa. Le versioni dell'API disponibili corrispondono alle versioni delle operazioni API REST che vengono rilasciate dal provider di risorse. Quando i provider di risorse abilitano nuove funzionalità, rilasciano le nuove versioni dell'API REST. Di conseguenza, la versione dell'API specificata nel modello influisce sulle proprietà disponibili quando si crea il modello. In generale, è opportuno selezionare la versione più recente dell'API durante la creazione di nuovi modelli. Per i modelli esistenti, è possibile decidere se continuare a usare una versione dell'API che certamente non modificherà la distribuzione o se aggiornare il modello alla versione più recente per sfruttare i vantaggi delle nuove funzionalità.

Questo passaggio può sembrare poco chiaro, ma individuare le versioni dell'API disponibili per la risorsa non è difficile. Si userà ancora una volta il comando **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Come si può notare, questa API è stata aggiornata spesso. In genere, per tutte le risorse di un provider di risorse saranno disponibili gli stessi numeri di versione dell'API. L'unica eccezione si verifica se, a un certo punto, una risorsa viene aggiunta o rimossa. Si presuppone che per la risorsa serverFarms siano disponibili le stesse versioni dell'API, ma è possibile verificare se sono presenti risorse che si ritiene possano avere un elenco diverso di versioni dell'API disponibili.

Per il database, verrà visualizzato:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Creare il modello

Questo argomento non spiega come creare il modello e non ne illustra la struttura. Per queste informazioni, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md). Il modello che si distribuirà è riportato di seguito. Si noti che il modello usa le versioni dell'API recuperate nella sezione precedente. Per assicurarsi che tutte le risorse si trovino nella stessa area, viene usata l'espressione del modello **resourceGroup().location** per usare la posizione del gruppo di risorse.

Si noti anche la sezione dei parametri. Questa sezione definisce i valori che è possibile specificare quando si distribuiscono le risorse. Questi valori verranno usati più avanti nell'esercitazione.

È possibile copiare il modello e salvarlo in locale come file con estensione json. In questa esercitazione si presume che sia stato salvato in c:\\Azure\\Templates\\azuredeploy.json, ma è possibile salvarlo in qualsiasi percorso e con il nome che si preferisce.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Distribuire il modello

Dopo avere creato il gruppo di risorse e il modello, si è pronti per distribuire nel gruppo di risorse l'infrastruttura definita nel modello. Le risorse vengono distribuite con il cmdlet **New-AzureRmResourceGroupDeployment**. La sintassi di base è simile alla seguente:

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Vengono specificati il gruppo di risorse e il percorso del modello. Se il modello non è locale, è possibile usare il parametro **-TemplateUri** e specificare un URI per il modello. È possibile impostare il parametro **-Mode** su **Incremental** o **Complete**. Per impostazione predefinita, Gestione risorse esegue un aggiornamento incrementale durante la distribuzione. Non è pertanto fondamentale impostare il parametro **-Mode** se si vuole la modalità **Incremental**. Per comprendere le differenze tra le modalità di distribuzione, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

###Parametri del modello dinamici

Se si ha familiarità con PowerShell, è possibile cercare un cmdlet tra i parametri disponibili digitando un segno meno (-) e quindi premendo TAB. Questa stessa funzionalità può essere usata anche con i parametri definiti nel modello. Quando si digita il nome del modello, il cmdlet recupera il modello, lo analizza e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, PowerShell richiede il valore.

Di seguito viene riportato il comando completo con i parametri inclusi. È possibile specificare i propri valori per i nomi delle risorse.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

Quando si inserisce il comando, viene richiesto il parametro obbligatorio mancante **administratorLoginPassword**. Inoltre, quando si digita la password, il valore della stringa sicura viene oscurato. Questa strategia elimina il rischio di fornire la password in testo normale.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se il modello include un parametro con un nome corrispondente a uno dei parametri nel comando per la distribuzione del modello, ad esempio un parametro denominato **ResourceGroupName** nel modello che corrisponde al parametro **ResourceGroupName** nel cmdlet [New AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx), verrà richiesto di specificare un valore per un parametro con il suffisso **FromTemplate**, ad esempio **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

Il comando viene eseguito e restituisce dei messaggi quando le risorse vengono create. Infine viene visualizzato il risultato della distribuzione.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

In pochi passaggi, sono state create e distribuite le risorse necessarie per un sito Web complesso.

## Ottenere informazioni sui gruppi di risorse

Dopo avere creato un gruppo di risorse, è possibile usare i cmdlet del modulo di Gestione risorse per gestire i gruppi di risorse.

- Per ottenere tutti i gruppi di risorse della sottoscrizione, usare il cmdlet **Get-AzureRmResourceGroup**:

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      Se si vuole ottenere un determinato gruppo di risorse, fornire il parametro **Nome**.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- Per ottenere le risorse nel gruppo di risorse, usare il cmdlet **Find-AzureRmResource** e il relativo parametro **ResourceGroupNameContains**. Senza parametri, Find-AzureRmResource recupera tutte le risorse nella sottoscrizione di Azure.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- Il modello precedente include un tag di una risorsa. È possibile usare i tag per organizzare logicamente tutte le risorse nella sottoscrizione. Usare i comandi **Find-AzureRmResource** e **Find-AzureRmResourceGroup** per eseguire una query delle risorse in base ai tag.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      I tag consentono di eseguire numerose operazioni. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## Aggiungere un gruppo di risorse

Per aggiungere una risorsa al gruppo di risorse, è possibile usare il cmdlet **New-AzureRmResource**. Aggiungere una risorsa in questo modo, tuttavia, potrebbe creare confusione in futuro perché la nuova risorsa non esiste nel modello. Se si ridistribuisse il vecchio modello, si distribuirebbe una soluzione incompleta. Se si prevede di eseguire spesso la distribuzione, risulterà più semplice e affidabile aggiungere la nuova risorsa al modello e ridistribuirlo.

## Spostare una risorsa

È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## Eliminare un gruppo di risorse

- Per eliminare una risorsa dal gruppo di risorse, usare il cmdlet **Remove-AzureRmResource**. Questo cmdlet elimina la risorsa, ma non il gruppo di risorse.

	Il comando rimuove il sito Web TestSite dal gruppo di risorse TestRG.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Per eliminare un gruppo di risorse, usare il cmdlet **Remove-AzureRmResourceGroup**. Questo cmdlet elimina il gruppo di risorse e le relative risorse.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Passaggi successivi

- Per informazioni sulla creazione di modelli di Gestione risorse, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).
- Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](./resource-group-template-deploy.md).
- Per un esempio dettagliato della distribuzione di un progetto, vedere [Distribuire microservizi in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Per informazioni sulla risoluzione dei problemi relativi a una distribuzione non riuscita, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./virtual-machines/resource-group-deploy-debug.md).

<!---HONumber=AcomDC_0218_2016-->