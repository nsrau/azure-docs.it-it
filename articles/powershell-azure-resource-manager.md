<properties 
	pageTitle="Azure PowerShell con Gestione risorse | Microsoft Azure" 
	description="Introduzione all'uso di Azure PowerShell per distribuire più risorse come gruppo di risorse in Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/11/2016" 
	ms.author="tomfitz"/>

# Uso di Azure PowerShell con Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)

Con Gestione risorse di Azure è stato introdotto un metodo completamente nuovo per gestire le risorse di Azure. Invece di creare e gestire le singole risorse, si inizia immaginando un'intera soluzione, ad esempio un blog, una raccolta foto, un portale di SharePoint o un wiki. Si usa un modello (una rappresentazione dichiarativa della soluzione) per creare un gruppo di risorse contenente tutte le risorse necessarie per supportare la soluzione. A questo punto, si gestisce e distribuisce il gruppo di risorse come unità logica.

In questa esercitazione viene descritto come usare Azure PowerShell con Gestione risorse di Azure. Illustra in modo dettagliato il processo di distribuzione di una soluzione e di utilizzo della soluzione. Per la distribuzione verranno usati Azure PowerShell e un modello di Azure Resource Manager:

- Server SQL: per ospitare il database
- Database SQL: per archiviare i dati
- Regole del firewall: per consentire all'app Web di connettersi al database
- Piano di servizio app: per definire le funzionalità e il costo dell'app Web
- Sito Web: per eseguire l'app Web
- Configurazione Web: per archiviare la stringa di connessione al database 
- Regole di avviso: per il monitoraggio delle prestazioni e degli errori
- App Insights: per le impostazioni del ridimensionamento automatico

## Prerequisiti

Per completare questa esercitazione, sono necessari:

- Un account Azure
  + È possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.
  
  + È possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
- Azure PowerShell 1.0. Per informazioni su questa versione e su come installarla, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, quali moduli, cmdlet e sessioni.

## Ottenere la guida per i cmdlet

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Get-AzureRmResource, digitare:

    Get-Help Get-AzureRmResource -Detailed

Per un elenco dei cmdlet del modulo Resources con un riepilogo di supporto, digitare:

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

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

Per accedere al proprio account Azure, usare il cmdlet **Add-AzureRmAccount**.

    Add-AzureRmAccount

Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

Le impostazioni dell'account hanno una scadenza, quindi è necessario aggiornarle regolarmente. Per aggiornare le impostazioni dell'account, eseguire di nuovo **Add-AzureRmAccount**.

>[AZURE.NOTE] I moduli di Azure Resource Manager richiedono Add-AzureRmAccount. Non è sufficiente un file di impostazioni di pubblicazione.

Se sono disponibili più sottoscrizioni, specificare l'ID sottoscrizione da usare per la distribuzione con il cmdlet **Set-AzureRmContext**.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## Creare un gruppo di risorse

Prima di distribuire risorse nella sottoscrizione, è necessario creare un gruppo di risorse che conterrà le risorse.

Per creare un gruppo di risorse, usare il cmdlet **New-AzureRmResourceGroup**.

Il comando utilizza il parametro **Name** per specificare un nome per il gruppo di risorse e il parametro **Location** per specificarne la posizione. In base a quanto stabilito nella sezione precedente, verrà usata la posizione "West US".

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
L'output sarà analogo al seguente:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Il gruppo di risorse è stato creato.

## Distribuire la soluzione

Questo argomento non spiega come creare il modello e non ne illustra la struttura. Per informazioni, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md) e [Procedura dettagliata per un modello di Azure Resource Manager](resource-manager-template-walkthrough.md). Il modello predefinito per il [provisioning di un'app Web con un database SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) verrà distribuito da [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).

Dopo avere creato il gruppo di risorse e il modello, si è pronti per distribuire nel gruppo di risorse l'infrastruttura definita nel modello. Le risorse vengono distribuite con il cmdlet **New-AzureRmResourceGroupDeployment**. Il modello specifica molti valori predefiniti, che verranno usati per evitare che sia necessario fornire valori per questi parametri. La sintassi di base è simile alla seguente:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Vengono specificati il gruppo di risorse e il percorso del modello. Se il modello è un file locale, usare il parametro **-TemplateFile** e specificare il percorso del modello. È possibile impostare il parametro **-Mode** su **Incremental** o **Complete**. Per impostazione predefinita, Gestione risorse esegue un aggiornamento incrementale durante la distribuzione. Non è pertanto fondamentale impostare il parametro **-Mode** se si vuole la modalità **Incremental**. Per comprendere le differenze tra le modalità di distribuzione, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

###Parametri del modello dinamici

Se si ha familiarità con PowerShell, è possibile cercare un cmdlet tra i parametri disponibili digitando un segno meno (-) e quindi premendo TAB. Questa stessa funzionalità può essere usata anche con i parametri definiti nel modello. Quando si digita il nome del modello, il cmdlet recupera il modello, lo analizza e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello.

Quando si inserisce il comando, viene richiesto il parametro obbligatorio mancante **administratorLoginPassword**. Inoltre, quando si digita la password, il valore della stringa sicura viene oscurato. Questa strategia elimina il rischio di fornire la password in testo normale.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se il modello include un parametro con un nome corrispondente a uno dei parametri nel comando per la distribuzione del modello, ad esempio un parametro denominato **ResourceGroupName** nel modello che corrisponde al parametro **ResourceGroupName** nel cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx), verrà richiesto di specificare un valore per un parametro con il suffisso **FromTemplate**, ad esempio **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione non attribuendo ai parametri lo stesso nome dei parametri usati per operazioni di distribuzione.

Il comando viene eseguito e restituisce dei messaggi quando le risorse vengono create. Infine viene visualizzato il risultato della distribuzione.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

In pochi passaggi, sono state create e distribuite le risorse necessarie per un sito Web complesso.

### Registrare le informazioni di debug

Quando si distribuisce un modello, è possibile registrare informazioni aggiuntive sulla richiesta e sulla risposta, specificando il parametro **-DeploymentDebugLogLevel** quando si esegue **New-AzureRmResourceGroupDeployment**. Queste informazioni possono essere utili per risolvere gli errori di distribuzione. Il valore predefinito è **None**, ovvero non vengono registrati contenuti relativi alla richiesta o alla risposta. È possibile specificare la registrazione del contenuto dalla richiesta, dalla risposta o da entrambi. Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni e sulla registrazione delle informazioni di debug, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). L'esempio seguente registra il contenuto di richieste e risposte per la distribuzione.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Quando si imposta il parametro DeploymentDebugLogLevel, occorre valutare attentamente il tipo di informazioni passate durante la distribuzione. La registrazione di informazioni sulla richiesta o sulla risposta può esporre dati riservati, che vengono recuperati tramite le operazioni di distribuzione.


## Ottenere informazioni sui gruppi di risorse

Dopo avere creato un gruppo di risorse, è possibile usare i cmdlet del modulo di Gestione risorse per gestire i gruppi di risorse.

- Per ottenere un gruppo di risorse nella sottoscrizione, usare il cmdlet **Get-AzureRmResourceGroup**:

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	Che restituisce le informazioni seguenti:

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	Se non si specifica alcun nome di gruppo di risorse, il cmdlet restituisce tutti i gruppi di risorse della sottoscrizione.

- Per ottenere le risorse nel gruppo di risorse, usare il cmdlet **Find-AzureRmResource** e il relativo parametro **ResourceGroupNameContains**. Senza parametri, Find-AzureRmResource recupera tutte le risorse nella sottoscrizione di Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     Viene restituito un elenco di risorse con formato analogo al seguente:
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- È possibile usare i tag per organizzare in modo logico le risorse della sottoscrizione, quindi recuperare le risorse con i cmdlet **Find-AzureRmResource** e **Find-AzureRmResourceGroup**.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      I tag consentono di eseguire numerose operazioni. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## Aggiungere un gruppo di risorse

Per aggiungere una risorsa al gruppo di risorse, è possibile usare il cmdlet **New-AzureRmResource**. Aggiungere una risorsa in questo modo, tuttavia, potrebbe creare confusione in futuro perché la nuova risorsa non esiste nel modello. Se si ridistribuisse il vecchio modello, si distribuirebbe una soluzione incompleta. Se si prevede di eseguire spesso la distribuzione, risulterà più semplice e affidabile aggiungere la nuova risorsa al modello e ridistribuirlo.

## Spostare una risorsa

È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## Esportare il modello

Per un gruppo di risorse esistente, distribuito mediante PowerShell o uno degli altri metodi quali il portale, è possibile visualizzare il modello di Azure Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.

2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

PowerShell consente di generare un modello che rappresenta lo stato corrente del gruppo di risorse oppure di recuperare il modello usato per una distribuzione specifica.

L'esportazione del modello per un gruppo di risorse risulta utile quando si apportano modifiche a un gruppo di risorse ed è necessario recuperare la rappresentazione JSON del rispettivo stato corrente. Il modello generato, tuttavia, contiene solo un numero minimo di parametri e nessuna variabile. La maggior parte dei valori del modello è hardcoded. Prima di distribuire il modello generato, è possibile che si voglia convertire altri valori in parametri, per potere personalizzare la distribuzione per diversi ambienti.

L'esportazione del modello per una distribuzione specifica è utile quando è necessario visualizzare il modello effettivo usato per distribuire le risorse. Il modello includerà tutte le variabili e tutti i parametri definiti per la distribuzione originale. Se, tuttavia, un utente dell'organizzazione ha modificato il gruppo di risorse in modo diverso da quanto definito nel modello, questo modello non rappresenterà lo stato corrente del gruppo di risorse.

> [AZURE.NOTE] La funzionalità di esportazione del modello è disponibile in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si prova a esportare un modello, è possibile che venga visualizzato un errore che indica che alcune risorse non sono state esportate. Se necessario, è possibile definire manualmente queste risorse nel modello dopo averlo scaricato.

###Esportare il modello da un gruppo di risorse

Per visualizzare il modello per un gruppo di risorse, eseguire il cmdlet **Export-AzureRmResourceGroup**.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###Scaricare il modello dalla distribuzione

Per scaricare il modello usato per una distribuzione specifica, eseguire il cmdlet **Save-AzureRmResourceGroupDeploymentTemplate**.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## Eliminare le risorse o il gruppo di risorse

- Per eliminare una risorsa dal gruppo di risorse, usare il cmdlet **Remove-AzureRmResource**. Questo cmdlet elimina la risorsa, ma non il gruppo di risorse.

	Il comando rimuove il sito Web TestSite dal gruppo di risorse TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Per eliminare un gruppo di risorse, usare il cmdlet **Remove-AzureRmResourceGroup**. Questo cmdlet elimina il gruppo di risorse e le relative risorse.

		Remove-AzureRmResourceGroup -Name TestRG1
		
	È necessario confermare l'eliminazione.
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## Script di distribuzione

Gli esempi di distribuzione precedenti di questo argomento hanno illustrato solo i singoli cmdlet necessari per distribuire risorse in Azure. L'esempio seguente mostra uno script di distribuzione che crea il gruppo di risorse e quindi distribuisce le risorse.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## Passaggi successivi

- Per informazioni sulla creazione di modelli di Gestione risorse, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).
- Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](./resource-group-template-deploy.md).
- Per un esempio dettagliato della distribuzione di un progetto, vedere [Distribuire microservizi in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Per informazioni sulla risoluzione dei problemi relativi a una distribuzione non riuscita, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0525_2016-->