<properties 
	pageTitle="Uso di Azure PowerShell con Gestione risorse di Azure" 
	description="Utilizzare Azure PowerShell per distribuire più risorse come gruppo di risorse in Azure." 
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
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Uso di Azure PowerShell con Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Con Gestione risorse di Azure è stato introdotto un metodo completamente nuovo per gestire le risorse di Azure. Anziché creare e gestire le singole risorse, si inizia immaginando un servizio complesso, ad esempio un blog, una raccolta foto, un portale di SharePoint o un wiki. Si utilizza un modello di risorsa del servizio per creare un gruppo contenente le risorse desiderate per supportare il servizio. A questo punto, è possibile gestire e distribuire il gruppo di risorse come unità logica.

In questa esercitazione viene descritto come usare Windows Azure PowerShell con Gestione risorse per Microsoft Azure. Verrà illustrato il processo di creazione e distribuzione di un gruppo di risorse per un'app Web ospitata in Azure con un database SQL, completo di tutte le risorse necessarie per supportarlo.

## Prerequisiti

Per completare questa esercitazione, è necessario disporre di Azure PowerShell 0.8.0 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, quali moduli, cmdlet e sessioni. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Add-AzureAccount, digitare:

	Get-Help Add-AzureAccount -Detailed

## Informazioni sui moduli di Azure Powershell
A partire dalla versione 0.8.0, l'installazione di Azure PowerShell include più di un modulo di PowerShell. È necessario decidere in modo esplicito se utilizzare i comandi disponibili nel modulo Azure o il modulo di Gestione risorse di Azure. Per facilitare il passaggio da un modulo all'altro, al modulo AzureProfile è stato aggiunto il nuovo cmdlet **Switch-AzureMode**.

Quando si usa Azure PowerShell, i cmdlet inclusi nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, usare il cmdlet Switch-AzureMode. Il modulo Azure verrà rimosso dalla sessione e i moduli AzureResourceManager e AzureProfile verranno importati.

Per passare al modulo AzureResoureManager, digitare:

    PS C:> Switch-AzureMode -Name AzureResourceManager

Per tornare al modulo Azure, digitare:

    PS C:> Switch-AzureMode -Name AzureServiceManagement

Per impostazione predefinita, Switch-AzureMode viene applicato solo alla sessione corrente. Per applicare l'opzione a tutte le sessioni di PowerShell, usare il parametro **Global** di Switch-AzureMode.

Per informazioni sul cmdlet Switch-AzureMode, digitare: `Get-Help Switch-AzureMode` oppure vedere [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Per un elenco dei cmdlet del modulo AzureResourceManager con un riepilogo di supporto, digitare:

    PS C:> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

L'output sarà simile al seguente:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Per informazioni complete per un cmdlet, digitare un comando con il formato seguente:

	Get-Help <cmdlet-name> -Full

Ad esempio,

	Get-Help Get-AzureLocation -Full

Per l’insieme completo di comandi di Gestione risorse di Azure, vedere [Cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Creare un gruppo di risorse

Questa sezione dell'esercitazione descrive il processo di creazione e distribuzione di un gruppo di risorse per un'app Web con un database SQL.

Non è necessario essere esperti di Azure, SQL, app Web o della gestione delle risorse per eseguire questa attività. I modelli consentono di creare il gruppo di risorse con tutte le risorse che potrebbero essere necessarie. Poiché si utilizza Windows PowerShell per automatizzare le attività, è possibile servirsi di questo processo come modello per la creazione di script per attività su vasta scala.

### Passaggio 1: Passare a Gestione risorse di Azure 
1. Avviare PowerShell. È possibile usare il programma host desiderato, ad esempio la console di Azure PowerShell o Windows PowerShell ISE.

2. Usare il cmdlet **Switch-AzureMode** per importare i cmdlet nei moduli AzureResourceManager e AzureProfile.

        PS C:> Switch-AzureMode AzureResourceManager

3. Per aggiungere l'account Azure alla sessione di Windows PowerShell, usare il cmdlet **Add-AzureAccount**.

        PS C:> Add-AzureAccount

Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l’accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Windows PowerShell.

Le impostazioni dell'account hanno una scadenza, quindi è necessario aggiornarle regolarmente. Per aggiornare le impostazioni dell'account, eseguire di nuovo **Add-AzureAccount**.

>[AZURE.NOTE]Il modulo AzureResourceManager richiede Add-AzureAccount. Non è sufficiente un file di impostazioni di pubblicazione.

### Passaggio 2: Selezionare un modello di raccolta

Esistono modi diversi per creare un gruppo di risorse con le relative risorse, ma la soluzione più semplice consiste nell'usare un modello di gruppo di risorse. Un *modello di gruppo di risorse* è una stringa JSON che definisce le risorse di un gruppo. La stringa include segnaposto chiamati "parametri" per i valori definiti dall'utente, come i nomi e le dimensioni.

Azure ospita una raccolta di modelli di gruppi di risorse ed è possibile creare modelli personalizzati da zero o modificando un modello della raccolta. In questa esercitazione verrà usato un modello della raccolta.

Per visualizzare tutti i modelli nella raccolta di modelli del gruppo di risorse di Azure, utilizzare il cmdlet **Get-AzureResourceGroupGalleryTemplate**. Questo comando, però, restituisce un numero elevato di modelli. Per visualizzare un numero di modelli più gestibile, specificare un parametro dell’autore di pubblicazione.

Al prompt di Azure PowerShell digitare:
    
    PS C:> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

Il cmdlet restituisce un elenco dei modelli di raccolta con Microsoft come autore. La proprietà **Identity** viene utilizzata per identificare il modello nei comandi.

Il modello Microsoft.WebSiteSQLDatabase.0.2.6-preview sembra interessante. Quando si esegue il comando, la versione del modello potrebbe essere leggermente diversa perché è stata rilasciata una nuova versione. Usare la versione più recente del modello. Per ulteriori informazioni su un modello della raccolta, utilizzare il parametro **Identity**. Il valore del parametro Identity è l'identità del modello.

    PS C:> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

Il cmdlet restituisce un oggetto con maggiori informazioni sul modello, inclusi un riepilogo e una descrizione.

Questo modello sembra adatto per lo scopo dell'esercitazione. Salvarlo su disco ed esaminarlo più attentamente.

### Passaggio 3: Esaminare il modello

Salvare il modello in un file JSON su disco. Questo passaggio non è obbligatorio, ma facilita la visualizzazione del modello. Per salvare il modello, usare il cmdlet **Save-AzureResourceGroupGalleryTemplate**. Usare il relativo parametro **Identity** per specificare il modello e il parametro **Path** per specificare un percorso su disco.

Save-AzureResourceGroupGalleryTemplate salva il modello e restituisce il percorso e il nome del file del modello JSON.

	PS C:> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


È possibile visualizzare il file del modello in un editor di testo, come il Blocco note. Ogni modello presenta una sezione **parameters** e una sezione **resources**.

La sezione **parameters** del modello è un insieme dei parametri definiti in tutte le risorse. Include i valori delle proprietà che è possibile fornire quando si imposta il gruppo di risorse.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Alcuni parametri hanno un valore predefinito. Quando si utilizza un modello, non è necessario fornire i valori per questi parametri. Se non si specifica un valore, viene usato il valore predefinito.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

Quando i parametri hanno valori enumerati, i valori validi sono elencati con il parametro. Il parametro **sku** ad esempio accetta i valori Free, Shared, Basic e Standard. Se non si specifica un valore per il parametro **sku**, viene usato il valore predefinito Free.

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
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


A questo punto, è quasi possibile usare il modello, ma prima occorre trovare una posizione per ciascuna delle risorse.

### Passaggio 4: Ottenere le posizioni dei tipi di risorsa

Per la maggior parte dei modelli è necessario specificare una posizione per ognuna delle risorse di un gruppo di risorse. Tutte le risorse si trovano in un data center di Azure, ma non tutti i data center di Azure supportano qualsiasi tipo di risorsa.

Selezionare una posizione che supporti il tipo di risorsa. Non è necessario creare tutte le risorse in un gruppo di risorse nello stesso percorso. Tuttavia, quando è possibile, è consigliabile creare risorse nello stesso percorso per ottimizzare le prestazioni. In particolare, è possibile assicurarsi che il database si trovi nello stesso percorso dell'applicazione che effettua l'accesso.

Per recuperare le posizioni che supportano ciascun tipo di risorsa, usare il cmdlet **Get-AzureLocation**. Per limitare l'output a un tipo specifico di risorsa, ad esempio ResourceGroup, utilizzare:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

L'output sarà simile a:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Ora sono disponibili le informazioni necessarie per creare il gruppo di risorse.

### Passaggio 5: Creare un gruppo di risorse
 
In questo passaggio il modello di gruppo di risorse verrà usato per creare il gruppo di risorse. Per riferimento, aprire il file New_WebSite_And_Database.json su disco e proseguire. Il file di modello può essere molto utile per determinare i valori di parametro da passare, ad esempio il valore di ApiVersion corretto per una risorsa.

Per creare un gruppo di risorse, usare il cmdlet **New-AzureResourceGroup**.

Il comando utilizza il parametro **Name** per specificare un nome per il gruppo di risorse e il parametro **Location** per specificarne la posizione. Usare l'output di **Get-AzureLocation** per selezionare una posizione per il gruppo di risorse. Il parametro **GalleryTemplateIdentity** viene utilizzato per specificare il modello della raccolta.

	PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

Quando si digita il nome del modello, New-AzureResourceGroup lo recupera, lo analizza e aggiunge i relativi parametri al comando in modo dinamico. In questo modo è molto semplice specificare i valori dei parametri del modello. Inoltre, se si dimentica un valore per un parametro obbligatorio, Windows PowerShell richiede il valore.

**Parametri dinamici del modello**

Per ottenere i parametri, digitare un segno meno (-) per indicare un nome di parametro e quindi premere TAB. In alternativa, digitare le prime lettere di un nome di parametro, ad esempio siteName, quindi premere TAB.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

Il nome del parametro viene completato automaticamente. Per spostarsi tra i nomi dei parametri, premere ripetutamente TAB.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Immettere un nome per il sito Web e ripetere il processo con TAB per ognuno dei parametri. I parametri con un valore predefinito sono facoltativi. Per accettare un valore predefinito, omettere il parametro dal comando.

Quando un parametro di un modello include valori enumerati, ad esempio il parametro sku di questo modello, premere TAB per passare da un valore all'altro.

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Di seguito è riportato un esempio di comando New-AzureResourceGroup che specifica solo i parametri obbligatori e il parametro comune **Verbose**. Si noti che **administratorLoginPassword** viene omesso

	PS C:> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

Quando si inserisce il comando, viene richiesto il parametro obbligatorio mancante **administratorLoginPassword**. Inoltre, quando si digita la password, il valore della stringa sicura viene oscurato. Questa strategia elimina il rischio di fornire la password in testo normale.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup** restituisce il gruppo di risorse creato e distribuito.

In pochi passaggi, sono state create e distribuite le risorse necessarie per un sito Web complesso. Il modello della raccolta ha fornito quasi tutte le informazioni necessarie per questa attività. Inoltre, l'attività è stata automatizzata facilmente.

## Ottenere informazioni sui gruppi di risorse

Dopo aver creato un gruppo di risorse, è possibile utilizzare i cmdlet del modulo AzureResourceManager per gestire i gruppi di risorse.

- Per ottenere i gruppi di risorse della propria sottoscrizione, usare il cmdlet **Get-AzureResourceGroup**:

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Per ottenere le risorse del gruppo di risorse, usare il cmdlet **GetAzureResource** e il relativo parametro ResourceGroupName. Senza parametri, Get-AzureResource recupera tutte le risorse della sottoscrizione di Azure.

		PS C:> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Aggiungere un gruppo di risorse

- Per aggiungere una risorsa al gruppo di risorse, usare il cmdlet **New-AzureResource**. Questo comando aggiunge un nuovo sito Web al gruppo di risorse TestRG. Questo comando è leggermente più complesso, in quanto non utilizza un modello. 

        PS C:>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Per aggiungere una nuova distribuzione basata su un modello al gruppo di risorse, usare il comando **New-AzureResourceGroupDeployment**.

		PS C:>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Spostare una risorsa

È possibile spostare le risorse esistenti in un nuovo gruppo di risorse. Per esempi, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## Eliminare un gruppo di risorse

- Per eliminare una risorsa dal gruppo di risorse, utilizzare il cmdlet **Remove-AzureResource**. Questo cmdlet elimina la risorsa, ma non il gruppo di risorse.

	Il comando rimuove il sito Web TestSite2 dal gruppo di risorse TestRG.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Per eliminare un gruppo di risorse, usare il cmdlet **Remove-AzureResourceGroup**. Questo cmdlet elimina il gruppo di risorse e le relative risorse.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Risoluzione dei problemi relativi a un gruppo di risorse
Quando si sperimentano i cmdlet dei moduli AzureResourceManager, è possibile che si verifichino errori. Usare i suggerimenti di questa sezione per risolverli.

### Evitare gli errori

Il modulo AzureResourceManager include cmdlet che aiutano a evitare gli errori.


- **Get-AzureLocation**: questo cmdlet ottiene i percorsi che supportano ogni tipo di risorsa. Prima di specificare una posizione per una risorsa, usare questo cmdlet per verificare che la posizione supporti il tipo di risorsa.


- **Test-AzureResourceGroupTemplate**: testare il modello e il parametro di modello prima di usarli. Immettere un modello personalizzato o di una raccolta e i valori dei parametri del modello che si intende usare. Questo cmdlet verifica se il modello è coerente al suo interno e se il valore impostato per il parametro corrisponde al modello.



### Correggere gli errori

- **Get-AzureResourceGroupLog**: questo cmdlet ottiene le voci del log per ogni distribuzione del gruppo di risorse. Se si verificano problemi, esaminare innanzitutto i log di distribuzione. 

- **Verbose e Debug**: i cmdlet nel modulo AzureResourceManager chiamano le API REST che eseguono effettivamente le operazioni. Per visualizzare i messaggi restituiti dalle API, impostare la variabile $DebugPreference su "Continue" e usare il parametro comune Verbose nei propri comandi. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

- **Le credenziali di Azure non sono state configurate o sono scadute**: per aggiornare le credenziali nella sessione di Windows PowerShell, usare il cmdlet Add-AzureAccount. Le credenziali di un file di impostazioni di pubblicazione non sono sufficienti per i cmdlet del modulo AzureResourceManager.


## Passaggi successivi
Introduzione

- [Panoramica di Gestione risorse di Microsoft Azure](./resource-group-overview.md)
- [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](./xplat-cli-azure-resource-manager.md)
- [Uso del portale di Azure per gestire le risorse di Azure](./resource-group-portal.md)

Creazione e distribuzione delle applicazioni

- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](./resource-group-template-deploy.md)
- [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-group-deploy-debug.md)
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)
- [Operazioni avanzate con i modelli](./resource-group-advanced-template.md)

Organizzazione delle risorse

- [Uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md)

Gestione e controllo dell'accesso

- [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)
- [Autenticazione di un'entità servizio con Gestione risorse di Azure](./resource-group-authenticate-service-principal.md)
- [Creare una nuova entità servizio di Azure usando il portale classico di Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO3-->