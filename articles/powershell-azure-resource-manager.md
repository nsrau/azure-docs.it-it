<properties pageTitle="Using Windows PowerShell with Resource Manager" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Use Windows PowerShell to create a resource group" metaCanonical="" services="" documentationCenter="" title="Using Windows PowerShell with Resource Manager" authors="juneb" solutions="" manager="mbaldwin" editor="mollybos" />

Utilizzo di Windows PowerShell con Gestione risorse
===================================================

Con Gestione risorse è stato introdotto un metodo completamente nuovo per gestire le risorse di Azure. Anziché creare e gestire le singole risorse, si inizia immaginando un servizio complesso, ad esempio un blog, una raccolta foto, un portale di SharePoint o un wiki. Si utilizza un modello di risorsa del servizio per creare un gruppo contenente le risorse desiderate per supportare il servizio. A questo punto, è possibile gestire e distribuire il gruppo di risorse come unità logica.

In questa esercitazione viene descritto come utilizzare Windows PowerShell con Gestione risorse per Microsoft Azure. Verrà illustrato il processo di creazione e distribuzione di un gruppo di risorse per un sito Web o un'applicazione Web ospitata in Azure con un database SQL, completo di tutte le risorse necessarie per supportarlo.

**Tempo previsto per il completamento:** 15 minuti

Prerequisiti
------------

Per utilizzare Windows PowerShell con Gestione risorse, è necessario disporre dei programmi seguenti:

-   Windows PowerShell 3.0 o versione successiva. Per trovare la versione di Windows PowerShell in uso, digitare `$PSVersionTable` e verificare che il valore di `PSVersion` sia 3.0 o maggiore. Per installare una versione più recente, vedere [Windows Management Framework 3.0](http://www.microsoft.com/en-us/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855).

-   Azure PowerShell 0.8.0 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, come i moduli, i cmdlet e le sessioni. Per ulteriori informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/en-us/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet utilizzati in questa esercitazione, utilizzare il cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Add-AzureAccount, digitare:

    Get-Help Add-AzureAccount -Detailed

Contenuto dell'esercitazione:
-----------------------------

-   [Informazioni sui moduli di Azure Powershell](#about)
-   [Creazione di un gruppo di risorse](#create)
-   [Gestione di un gruppo di risorse](#manage)
-   [Risoluzione dei problemi relativi a un gruppo di risorse](#troubleshoot)
-   [Passaggi successivi](#next)

Informazioni sui moduli di Azure Powershell
-------------------------------------------

A partire dalla versione 0.8.0, l'installazione di Azure PowerShell include tre moduli di Windows PowerShell:

-   **Azure**: include i cmdlet tradizionali per la gestione delle singole risorse, ad esempio gli account di archiviazione, i siti Web, i database, le macchine virtuali e i servizi multimediali. Per ulteriori informazioni, vedere i [cmdlet di Gestione servizi di Azure](http://msdn.microsoft.com/it-it/library/jj152841.aspx).

-   **AzureResourceManager**: include cmdlet per la creazione, gestione e distribuzione delle risorse di Azure per un gruppo di risorse. Per ulteriori informazioni, vedere i [cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765).

-   **AzureProfile**: include cmdlet comuni a entrambi i moduli, come Add-AzureAccount, Get-AzureSubscription e Switch-AzureMode. Per ulteriori informazioni, vedere i [cmdlet del profilo di Azure](http://go.microsoft.com/fwlink/?LinkID=394766).

> [ WACOM.NOTE] Il modulo AzureResourceManager è al momento in anteprima. Potrebbe non garantire le funzionalità di gestione del modulo AzureServiceManagement.

I moduli Azure e AzureResourceManager non sono stati progettati per essere utilizzati nella stessa sessione di Windows PowerShell. Per facilitare il passaggio da un modulo all'altro, al modulo AzureProfile è stato aggiunto il nuovo cmdlet **Switch-AzureMode**.

Quando si utilizza Azure PowerShell, i cmdlet dei moduli Azure e AzureProfile vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, utilizzare il cmdlet Switch-AzureMode. Il modulo Azure verrà rimosso dalla sessione e i moduli AzureResourceManager (e AzureProfile) verranno importati.

Per passare al modulo AzureResoureManager, digitare:

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManager

Per tornare al modulo Azure, digitare:

    PS C:PS C:\> Switch-AzureMode -Name AzureServiceManagement

Per impostazione predefinita, Switch-AzureMode viene applicato solo alla sessione corrente. Per applicare l'opzione a tutte le sessioni di Windows PowerShell, utilizzare il parametro **Global** di Switch-AzureMode.

Per informazioni sul cmdlet Switch-AzureMode, digitare: `Get-Help Switch-AzureMode` o vedere [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).

Per un elenco dei cmdlet del modulo AzureResourceManager con un riepilogo di supporto, digitare:

    PS C:PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

    Name                                   Synopsis
	----                                   --------
	Get-AzureLocation                      Gets the Azure data center locations and the resource types that they support
    Get-AzureResource                      Gets Azure resources
    Get-AzureResourceGroup                 Gets Azure resource groups
    Get-AzureResourceGroupDeployment       Gets the deployments in a resource group.
    Get-AzureResourceGroupGalleryTemplate  Gets resource group templates in the gallery
    Get-AzureResourceGroupLog              Gets the deployment log for a resource group
    New-AzureResource                      Creates a new resource in a resource group
    New-AzureResourceGroup                 Creates an Azure resource group and its resources
    New-AzureResourceGroupDeployment       Add an Azure deployment to a resource group.
    Remove-AzureResource                   Deletes a resource
    Remove-AzureResourceGroup              Deletes a resource group.
    Save-AzureResourceGroupGalleryTemplate Saves a gallery template to a JSON file
    Set-AzureResource                      Changes the properties of an Azure resource.
    Stop-AzureResourceGroupDeployment      Cancels a resource group deployment
    Test-AzureResourceGroupTemplate        Detects errors in a resource group template or template parameters
    
Per informazioni complete per un cmdlet, digitare un comando con il formato seguente:

    Get-Help <cmdlet-name> -Full

Ad esempio:

    Get-Help Get-AzureLocation -Full

Creazione di un gruppo di risorse per un sito Web e un database
===============================================================

Questa sezione dell'esercitazione descrive il processo di creazione e distribuzione di un gruppo di risorse per un sito Web con un database SQL.

Non è necessario essere esperti di Azure, SQL, dei siti Web o della gestione delle risorse per eseguire questa attività. I modelli consentono di creare il gruppo di risorse con tutte le risorse che potrebbero essere necessarie. Poiché si utilizza Windows PowerShell per automatizzare le attività, è possibile servirsi di questo processo come modello per la creazione di script per attività su vasta scala.

Passaggio 1: Passare a Gestione risorse di Azure
------------------------------------------------

1.  Avviare Windows PowerShell. È possibile utilizzare il programma host desiderato, ad esempio la console di Windows PowerShell o Windows PowerShell ISE.

2.  Utilizzare il cmdlet **Switch-AzureMode** per importare i cmdlet nei moduli AzureResourceManager e AzureProfile.

    `PS C:PS C:\>Switch-AzureMode AzureResourceManager`

3.  Per aggiungere l'account Azure alla sessione di Windows PowerShell, utilizzare il cmdlet **Add-AzureAccount**.

    `PS C:PS C:\> Add-AzureAccount`

Il cmdlet richiede un indirizzo di posta elettronica e una password. Vengono quindi scaricate le impostazioni dell'account in modo che siano disponibili per Windows PowerShell.

Le impostazioni dell'account hanno una scadenza, quindi è necessario aggiornarle regolarmente. Per aggiornare le impostazioni dell'account, eseguire di nuovo **Add-AzureAccount**.

> [ WACOM.NOTE] Il modulo AzureResourceManager richiede Add-AzureAccount. Non è sufficiente un file di impostazioni di pubblicazione.

Passaggio 2: Selezionare un modello di raccolta
-----------------------------------------------

Esistono modi diversi per creare un gruppo di risorse con le relative risorse, ma la soluzione più semplice consiste nell'utilizzare un modello di gruppo di risorse. Un *modello di gruppo di risorse* è una stringa JSON che definisce le risorse di un gruppo. La stringa include segnaposto chiamati "parametri" per i valori definiti dall'utente, come i nomi e le dimensioni.

Azure ospita una raccolta di modelli di gruppi di risorse ed è possibile creare modelli personalizzati da zero o modificando un modello della raccolta. In questa esercitazione verrà utilizzato un modello della raccolta.

Per cercare un modello nella raccolta di modelli di gruppo di risorse di Azure, utilizzare il cmdlet **Get-AzureResourceGroupGalleryTemplate**.

Al prompt di Windows Powershell digitare:

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate

Il cmdlet restituisce un elenco dei modelli di raccolta con le proprietà Publisher e Identity. La proprietà **Identity** viene utilizzata per identificare il modello nei comandi.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

SUGGERIMENTO: per richiamare l'ultimo comando, premere la freccia SU.

Il modello Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json sembra interessante. Per ulteriori informazioni su un modello della raccolta, utilizzare il parametro **Identity**. Il valore del parametro Identity è l'identità del modello.

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json

Il cmdlet restituisce un oggetto con maggiori informazioni sul modello, inclusa una descrizione.

    <p>Windows Azure Web Sites offers secure and flexible development, 
    deployment and scaling options for any sized web application. Leverage 
    your existing tools to create and deploy applications without the hassle 
    of managing infrastructure.</p>

Questo modello sembra adatto per lo scopo dell'esercitazione. Salvarlo su disco ed esaminarlo più attentamente.

Passaggio 3: Esaminare il modello
---------------------------------

Salvare il modello in un file JSON su disco. Questo passaggio non è obbligatorio, ma facilita la visualizzazione del modello. Per salvare il modello, utilizzare il cmdlet **Save-AzureResourceGroupGalleryTemplate**. Utilizzare il relativo parametro **Identity** per specificare il modello e il parametro **Path** per specificare un percorso su disco.

Save-AzureResourceGroupGalleryTemplate salva il modello e restituisce il percorso e il nome del file del modello JSON.

    PS C:PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json -Path D:\Azure\Templates

    Path
	----
	D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json
    
È possibile visualizzare il file del modello in un editor di testo, come il Blocco note. Ogni modello contiene una sezione **resources** e una sezione **parameters**.

Nella sezione **resources** del modello sono elencate le risorse create dal modello. Questo modello crea un server di database SQL e un database SQL, una server farm e un sito Web, nonché numerose impostazioni di gestione.

La definizione di ogni risorsa ne include le proprietà, quali il nome, il tipo e la posizione, e i parametri per i valori definiti dall'utente. Questa sezione del modello definisce ad esempio il database SQL. Include i parametri per il nome del database ([parameters('databaseName')]), la posizione del server di database [parameters('serverLocation')] e la proprietà collation [parameters('collation')].

     {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

La sezione **parameters** del modello è un insieme dei parametri definiti in tutte le risorse. Include le proprietà databaseName, serverLocation e collation.

    "parameters": {
      ...    
      
      "serverLocation": {
        "type": "string"
      }, 
      ...
      
      "databaseName": {
        "type": "string"
      },
      "collation": {
        "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
      }

Alcuni parametri hanno un valore predefinito. Quando si utilizza un modello, non è necessario fornire i valori per questi parametri. Se non si specifica un valore, viene utilizzato il valore predefinito.

    "collation": {
          "type": "string",
          "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
        }

Quando i parametri hanno valori enumerati, i valori validi sono elencati con il parametro. Il parametro **sku** ad esempio accetta i valori Free, Shared, Basic e Standard. Se non si specifica un valore per il parametro **sku**, viene utilizzato il valore predefinito Free.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },

Si noti che il parametro **administratorLoginPassword** utilizza una stringa sicura, non testo normale. Quando si specifica un valore per una stringa sicura, il valore viene oscurato.

    "administratorLoginPassword": {
      "type": "securestring"
    },

A questo punto, è quasi possibile utilizzare il modello, ma prima occorre trovare una posizione per ciascuna delle risorse.

Passaggio 4: Recuperare la posizione dei tipi di risorsa
--------------------------------------------------------

Per la maggior parte dei modelli è necessario specificare una posizione per ognuna delle risorse di un gruppo di risorse. Tutte le risorse si trovano in un data center di Azure, ma non tutti i data center di Azure supportano qualsiasi tipo di risorsa.

Selezionare una posizione che supporti il tipo di risorsa. Non è necessario che le risorse di un gruppo di risorse si trovino nella stessa posizione e che si trovino nella stessa posizione del gruppo di risorse o della sottoscrizione.

Per recuperare le posizioni che supportano ciascun tipo di risorsa, utilizzare il cmdlet **Get-AzureLocation**. Di seguito è riportata una parte dell'output (l'output ottenuto potrebbe essere diverso, in quanto i dettagli possono variare con il tempo).

    Name                                 Locations
	----                                 ---------
	ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US, 
										 South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
                                         East, Japan West, North Central US, North Europe, South Central US,
                                         Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

Ora sono disponibili le informazioni necessarie per creare il gruppo di risorse.

Passaggio 5: Creare un gruppo di risorse
----------------------------------------

In questo passaggio il modello di gruppo di risorse verrà utilizzato per creare il gruppo di risorse. Aprire come riferimento il file Microsoft.WebSiteSQLDatabase.0.1.0-preview1 JSON su disco e seguire la procedura.

Per creare un gruppo di risorse, utilizzare il cmdlet **New-AzureResourceGroup**.

Il comando utilizza il parametro **Name** per specificare un nome per il gruppo di risorse e il parametro **Location** per specificarne la posizione. Utilizzare l'output di **Get-AzureLocation** per selezionare una posizione per il gruppo di risorse. Il parametro **GalleryTemplateIdentity** viene utilizzato per specificare il modello della raccolta.

    PS C:PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

Quando si digita il nome del modello, New-AzureResourceGroup lo recupera, lo analizza e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, Windows PowerShell richiede il valore.

**Parametri del modello dinamici**

Per ottenere i parametri, digitare un segno meno (-) per indicare un nome di parametro e quindi premere TAB. In alternativa, digitare le prime lettere di un nome di parametro, ad esempio siteName, quindi premere TAB.

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>

Il nome del parametro viene completato automaticamente. Per spostarsi tra i nomi dei parametri, premere ripetutamente TAB.

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 

Immettere un nome per il sito Web e ripetere il processo con TAB per ognuno dei parametri. I parametri con un valore predefinito sono facoltativi. Per accettare un valore predefinito, omettere il parametro dal comando.

Quando un parametro di un modello include valori enumerati, ad esempio il parametro sku di questo modello, premere TAB per passare da un valore all'altro.

	    PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>
	
		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Free<TAB>
		
		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Basic<TAB>

Di seguito è riportato un esempio di comando New-AzureResourceGroup che specifica solo i parametri obbligatori e il parametro comune **Verbose**. Si noti che **administratorLoginPassword** viene omesso (il carattere di apice inverso (\`) è il carattere di continuazione della riga di Windows PowerShell).

    PS C:PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose
    
Quando si inserisce il comando, viene richiesto il parametro obbligatorio mancante **administratorLoginPassword**. Inoltre, quando si digita la password, il valore della stringa sicura viene oscurato. Questa strategia elimina il rischio di fornire la password in testo normale.

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !
     for Help.)
    administratorLoginPassword: **********

**New-AzureResourcGroup** restituisce il gruppo di risorse creato e distribuito. Di seguito è riportato l'output del comando, incluso l'output di verbose.

    VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
    VERBOSE: 3:47:30 PM - Template is valid.
    VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
    using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
    VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
    VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
    succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
    provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
    'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
    VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
    status is succeeded
    VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded


    ResourceGroupName : TestRG
    Location          : eastasia
    ProvisioningState : Succeeded
    Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

In pochi passaggi sono stati create e distribuite le risorse necessarie per un sito Web complesso. Il modello della raccolta ha fornito quasi tutte le informazioni necessarie per questa attività. Inoltre, l'attività è stata automatizzata facilmente.

Gestione di un gruppo di risorse
================================

Dopo aver creato un gruppo di risorse, è possibile utilizzare i cmdlet del modulo AzureResourceManager per gestirlo, modificarlo, aggiungervi risorse o rimuoverlo.

-   Per ottenere i gruppi di risorse della propria sottoscrizione, utilizzare il cmdlet **Get-AzureResourceGroup**:

          PS C:>Get-AzureResourceGroup

          ResourceGroupName : TestRG
          Location          : eastasia
          ProvisioningState : Succeeded
          Resources         :
                          Name                   Type                                  Location
                          =====================  ====================================  =========
                          ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                          TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                          TestSite               microsoft.insights/components         centralus
                          testserver             Microsoft.Sql/servers                 westus
                          TestDB                 Microsoft.Sql/servers/databases       westus
                          TestPlan               Microsoft.Web/serverFarms             westus
                          TestSite               Microsoft.Web/sites                   westus

-   Per ottenere le risorse del gruppo di risorse, utilizzare il cmdlet **GetAzureResource** e il relativo parametro ResourceGroupName. Senza parametri, Get-AzureResource recupera tutte le risorse della sottoscrizione di Azure.

          PS C:\> Get-AzureResource -ResourceGroupName TestRG
            
          Name                   ResourceType                          Location
		  ----                   ------------                          --------
		  ServerErrors-TestSite  microsoft.insights/alertrules         eastus
          TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
          TestSite               microsoft.insights/components         centralus
          testserver             Microsoft.Sql/servers                 westus
          TestDB                 Microsoft.Sql/servers/databases       westus
          TestPlan               Microsoft.Web/serverFarms             westus
          TestSite               Microsoft.Web/sites                   westus

-   Per aggiungere una risorsa al gruppo di risorse, utilizzare il cmdlet **New-AzureResource**. Questo comando aggiunge un nuovo sito Web al gruppo di risorse TestRG. Questo comando è leggermente più complesso, in quanto non utilizza un modello.

          PS C:\>New-AzureResource -Name TestSite2 `
          -Location "North Europe" `
          -ResourceGroupName TestRG `
          -ResourceType "Microsoft.Web/sites" `
          -ApiVersion 2004-04-01 `
          -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   Per aggiungere una nuova distribuzione basata su un modello al gruppo di risorse, utilizzare il comando **New-AzureResourceGroupDeployment**.

          PS C:\>New-AzureResourceGroupDeployment ` 
          -ResourceGroupName TestRG `
          -GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
          -siteName TestWeb2 `
          -hostingPlanName TestDeploy2 `
          -siteMode Limited `
          -computeMode Dedicated `
          -siteLocation "North Europe" 
          -subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
          -resourceGroup TestRG

-   Per eliminare una risorsa dal gruppo di risorse, utilizzare il cmdlet **Remove-AzureResource**. Questo cmdlet elimina la risorsa, ma non il gruppo di risorse.

    Il comando rimuove il sito Web TestSite2 dal gruppo di risorse TestRG.

          Remove-AzureResource -Name TestSite2 `
              -Location "North Europe" `
              -ResourceGroupName TestRG `
              -ResourceType "Microsoft.Web/sites" `
              -ApiVersion 2004-04-01

-   Per eliminare un gruppo di risorse, utilizzare il cmdlet **Remove-AzureResourceGroup**. Questo cmdlet elimina il gruppo di risorse e le relative risorse.

          PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
            
          Confirm
          Are you sure you want to remove resource group 'TestRG'
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

Risoluzione dei problemi relativi a un gruppo di risorse
========================================================

Quando si sperimentano i cmdlet dei moduli AzureResourceManager, è possibile che si verifichino errori. Utilizzare i suggerimenti di questa sezione per risolverli.

Evitare gli errori
------------------

Il modulo AzureResourceManager include cmdlet che aiutano a evitare gli errori.

-   **Get-AzureLocation**: questo cmdlet ottiene le posizioni che supportano ciascun tipo di risorsa. Prima di specificare una posizione per una risorsa, utilizzare questo cmdlet per verificare che la posizione supporti il tipo di risorsa.

-   **Test-AzureResourceGroupTemplate**: testare il modello e il relativo parametro prima di utilizzarli. Immettere un modello personalizzato o di una raccolta e i valori dei parametri del modello che si intende utilizzare. Questo cmdlet verifica se il modello è coerente al suo interno e se il valore impostato per il parametro corrisponde al modello.

Correggere gli errori
---------------------

-   **Get-AzureResourceGroupLog**: questo cmdlet recupera le voci del log per ogni distribuzione del gruppo di risorse. Se si verificano problemi, esaminare innanzitutto i log di distribuzione.

-   **Verbose e Debug**: i cmdlet del modulo AzureResourceManager chiamano le API REST che eseguono le operazioni effettive. Per visualizzare i messaggi restituiti dalle API, impostare la variabile \$DebugPreference su "Continue" e utilizzare il parametro comune Verbose nei propri comandi. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

-   **Le credenziali di Azure non sono state configurate o sono scadute**: per aggiornare le credenziali nella sessione di Windows PowerShell, utilizzare il cmdlet Add-AzureAccount. Le credenziali di un file di impostazioni di pubblicazione non sono sufficienti per i cmdlet del modulo AzureResourceManager.

Passaggi successivi
===================

Per ulteriori informazioni sull'utilizzo di Windows PowerShell con Gestione risorse:

-   [Cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): informazioni sull'utilizzo dei cmdlet nei moduli AzureResourceManager.
-   [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
-   [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove funzionalità di Windows PowerShell.
-   [Blog "Hey, Scripting Guy!":](http://blogs.technet.com/b/heyscriptingguy/) suggerimenti e consigli basati sull'esperienza dei membri della community.
-   [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse](http://www.windowsazure.com/it-it/documentation/articles/xplat-cli-azure-resource-manager/): informazioni sulle soluzioni alternative per automatizzare le operazioni di Gestione risorse.

