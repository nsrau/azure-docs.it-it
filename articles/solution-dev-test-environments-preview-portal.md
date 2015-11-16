<properties
   pageTitle="Ambienti di sviluppo e test | Microsoft Azure"
   description="Informazioni su come usare i modelli di Gestione risorse di Azure per creare ed eliminare in modo rapido e coerente ambienti di sviluppo e di test."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Ambienti di sviluppo e test in Microsoft Azure

Le applicazioni personalizzate vengono in genere distribuite in più ambienti di sviluppo e test prima della distribuzione in produzione. Quando gli ambienti vengono creati in locale, le risorse di calcolo vengono prodotte o allocate per ogni ambiente per ogni applicazione. Gli ambienti includono spesso alcune macchine virtuali o fisiche con configurazioni specifiche, distribuite manualmente o con complessi script di automazione. Le distribuzioni richiedono spesso alcune ore e hanno come risultato configurazioni non coerenti nei diversi ambienti.

## Scenario ##

Quando si esegue il provisioning degli ambienti di sviluppo e test in Microsoft Azure, si pagano solo le risorse usate. Questo articolo spiega che è possibile creare, gestire ed eliminare in modo rapido e coerente gli ambienti di sviluppo e test usando i modelli e i file di parametri di Gestione risorse di Azure, come illustrato di seguito.

![Scenario](./media/solution-dev-test-environments-preview-portal/scenario.png)

La figura precedente mostra tre ambienti di sviluppo e test. Ogni ambiente include un'applicazione Web e un database SQL. I nomi dell'applicazione e del database in ogni ambiente sono diversi. Questo articolo illustra come usare un modello per distribuire la stessa risorsa in più ambienti e come usare file di parametri univoci per specificare diverse configurazioni per le risorse nei diversi ambienti.

Se non si ha familiarità con i concetti relativi a Gestione risorse di Azure, è consigliabile leggere l'articolo [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md) prima di questo articolo.

È consigliabile esaminare prima di tutto i passaggi elencati in questo articolo, senza leggere gli articoli a cui si fa riferimento, per acquisire esperienza nell'uso dei modelli di Gestione risorse di Azure. L'esame di questi passaggi susciterà sicuramente molte domande. Dopo avere esaminato i passaggi una volta, sarà possibile ottenere le risposte per la maggior parte delle domande provando ad approfondire le procedure e leggendo gli articoli a cui si fa riferimento.

## Pianificare l'uso delle risorse di Azure
Dopo avere creato una struttura generale per l'applicazione, sarà possibile definire gli elementi seguenti:

- Risorse di Azure che verranno incluse nell'applicazione. È possibile compilare l'applicazione e distribuirla come app Web di Azure con un database SQL di Azure oppure compilare l'applicazione in macchine virtuali usando PHP e MySQL o IIS e SQL Server o altri componenti. L'articolo [Confronto tra servizio app, Servizi cloud e Macchine virtuali di Azure](app-service-web/choose-web-site-cloud-service-vm.md) è utile per decidere quali risorse di Azure utilizzare per l'applicazione.
- Requisiti del livello di servizio, ad esempio disponibilità, sicurezza e scalabilità, che verranno soddisfatti dall'applicazione.

## Uso dei modelli di Gestione risorse di Azure
Un modello di Gestione risorse di Azure definisce tutte le risorse di Azure utilizzate dall'applicazione. Esistono già alcuni modelli che possono essere distribuiti direttamente nel portale di anteprima di Azure o scaricati, modificati e salvati in un sistema di controllo del codice sorgente con il codice dell'applicazione. È probabile che esista già un modello che include le risorse da usare con l'applicazione. Per un elenco di modelli disponibili, vedere il repository dei [modelli della guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates/) in GitHub.

L'elenco include una cartella "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Poiché molte applicazioni personalizzate includono un'applicazione Web e un database SQL, questo modello viene usato come esempio per il resto dell'articolo, per semplificare la comprensione dell'uso dei modelli. La spiegazione completa di tutti gli elementi creati e configurati da questo modello non rientra nell'ambito dell'articolo, ma se si prevede di usarlo per creare ambienti effettivi nell'organizzazione, è consigliabile ottenere informazioni dettagliate leggendo l'articolo [Eseguire il provisioning di un'app Web con un database SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).

> [AZURE.NOTE]È possibile distribuire il modello direttamente in Azure facendo clic sul pulsante "Distribuisci in Azure" nell'articolo [Eseguire il provisioning di un'app Web con un database SQL](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Ciò può risultare utile per approfondire le proprie conoscenze sui modelli, ma non consente di modificare, sottoporre al controllo delle versioni e salvare il modello e i valori dei parametri con il codice dell'applicazione. I passaggi di questo articolo illustrano come salvare e sottoporre al controllo delle versioni il modello e i valori dei parametri con il codice dell'applicazione.

  **Passaggio 1:** Visualizzare i contenuti del file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) nella cartella 201-web-app-sql-database. Questo è il file modello di Gestione risorse di Azure. Nella modalità di visualizzazione fare clic sul pulsante "[Non elaborato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)". Con il mouse selezionare l'intero contenuto del file e salvarlo nel computer come file con nome "TestApp1-Template.json".

Nel file del modello verrà visualizzata una sezione "resources", che definisce le risorse di Azure create dal modello. Tra gli altri tipi di risorse, questo modello crea risorse di tipo [App Web di Azure](app-service-web/app-service-web-overview.md) e [Database SQL di Azure](sql-database/sql-database-technical-overview.md).

È anche disponibile una sezione "parameters", che definisce i parametri con cui è possibile configurare ogni risorsa. Alcuni parametri specificati nel modello hanno proprietà "defaultValue", altri no. Quando si distribuiscono risorse di Azure con un modello, è necessario fornire valori per tutti i parametri privi delle proprietà defaultValue. Se non si forniscono valori per i parametri con proprietà defaultValue, verrà usato il valore specificato per il parametro defaultValue nel modello.

Un modello definisce le risorse di Azure create e i parametri con cui è possibile configurare ogni risorsa. È in genere consigliabile creare le stesse risorse in ogni ambiente di sviluppo e di test. Per altre informazioni sui modelli e su come progettare modelli personalizzati, leggere l'articolo [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](best-practices-resource-manager-design-templates.md).

## Creare il file di parametri

È probabile che si voglia creare le stesse risorse di Azure in ogni ambiente, ma con diverse configurazioni per ogni ambiente. I file di parametri servono a questo.

  **Passaggio 2:** Visualizzare i contenuti del file [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) nella cartella 201-web-app-sql-database. Questo è il file di parametri per il file modello salvato nel Passaggio 1. Nella modalità di visualizzazione fare clic sul pulsante "[Non elaborato](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json)". Con il mouse selezionare l'intero contenuto del file e salvarlo in tre file separati nel computer con i nomi seguenti:

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Passaggio 3:** Usando un editor di testo o JSON, modificare i file di parametri degli ambienti di sviluppo e test creati nel Passaggio 2, sostituendo i valori esistenti nei file con i valori seguenti.

  --TestApp1-Parameters-Development.json--

| Parametro | Descrizione |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | Stati Uniti centrali |
| **serverName** | testapp1dev |
| **serverLocation** | Stati Uniti centrali |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Parametro | Descrizione |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | Stati Uniti centrali |
| **serverName** | testapp1test |
| **serverLocation** | Stati Uniti centrali |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

Nel Passaggio 6 questi file di parametri verranno usati per creare configurazioni univoche per le risorse dell'app Web di Azure e del database SQL di Azure negli ambienti di sviluppo e di test.

 **Passaggio 4:** Modificare il file di parametri TestApp1-Parameters-Pre-Production.json creato nel Passaggio 2. Sostituire l'intero contenuto del file con il codice seguente.

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

Nel file di parametri pre-produzione precedente i parametri **sku** e **requestedServiceObjectiveName** erano *added*, mentre non sono stati aggiunti nei file di parametri di sviluppo e di test. Ciò è dovuto al fatto che sono presenti valori predefiniti specificati per questi parametri nel modello. Negli ambienti di sviluppo e di test vengono usati i valori predefiniti, ma nell'ambiente di pre-produzione vengono usati valori non predefiniti per questi parametri.

Nell'ambiente di pre-produzione vengono usati valori non predefiniti per questi parametri per testare valori che potrebbero essere preferibili per questi parametri nell'ambiente di produzione. Questi parametri sono tutti correlati ai valori di [piani di hosting delle app Web](http://azure.microsoft.com/pricing/details/app-service/) o **sku** e [Database SQL](http://azure.microsoft.com/pricing/details/sql-database/) di Azure oppure **requestedServiceObjectiveName** usati dall'applicazione. Diversi SKU e nomi di oggetti del servizio hanno costi e funzionalità diverse e supportano diverse metriche relative ai livelli di servizio.

La tabella seguente elenca i valori predefiniti per i parametri specificati nel modello e i valori usati invece dei valori predefiniti nel file di parametri pre-produzione.

| Parametro | Valore predefinito | Valore del file di parametri |
|---|---|---|
| **sku** | Gratuito | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Creare ambienti
Tutte le risorse di Azure devono essere create entro un [Gruppo di risorse di Azure](azure-portal/resource-group-portal.md). I gruppi di risorse consentono di raggruppare le risorse di Azure, in modo che sia possibile gestirle insieme. È possibile assegnare [autorizzazioni](resource-group-rbac.md) ai gruppi di risorse, in modo che persone specifiche dell'organizzazione possano creare, modificare, eliminare o visualizzare i gruppi e le risorse disponibili nei gruppi. Gli avvisi e le informazioni di fatturazione per le risorse nel gruppo di risorse possono essere visualizzati nel [portale di anteprima di Azure](https://portal.azure.com). I gruppi di risorse vengono creati in una [posizione](http://azure.microsoft.com/regions/) di Azure. In questo articolo tutte le risorse vengono create nella posizione Stati Uniti centrali. Quando si inizia a creare ambienti effettivi, si sceglierà la posizione più adatta alle proprie esigenze.

  **Passaggio 5:** Creare gruppi di risorse per gli ambienti di sviluppo e di test usando uno dei metodi seguenti. Entrambi i metodi avranno esattamente lo stesso risultato.

  **Metodo 1:** interfaccia della riga di comando di Azure (CLI)

  Assicurarsi che l'interfaccia della riga di comando sia [installata](xplat-cli-install.md) in un computer Windows, OS X o Linux e che l'ID dell'organizzazione sia [connesso](xplat-cli-connect.md) alla sottoscrizione di Azure. Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo.

	azure group create "TestApp1-Development" "Central US"

  Se avrà esito positivo, il comando restituirà quanto segue:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Per creare il gruppo di risorse per l'ambiente di test, immettere il comando seguente:

	azure group create "TestApp1-Test" "Central US"

  Per creare il gruppo di risorse per l'ambiente di pre-produzione, immettere il comando seguente:

	azure group create "TestApp1-Pre-Production" "Central US"

  **Metodo 2:** PowerShell

  Assicurarsi che PowerShell sia installato in un computer Windows e connesso alla sottoscrizione, come illustrato in modo dettagliato nell'articolo [Come installare e configurare Azure PowerShell](powershell-install-configure.md). Da un prompt dei comandi di PowerShell digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo. Se si usa la versione di anteprima di Azure PowerShell 1.0, il comando è **New-AzureRmResourceGroup**, come illustrato di seguito. Se si usa una versione di Azure PowerShell precedente all'anteprima 1.0, il comando è **New-AzureResourceGroup**.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  Se avrà esito positivo, il comando restituirà quanto segue:

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Per creare il gruppo di risorse per l'ambiente di test, immettere il comando seguente:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  Per creare il gruppo di risorse per l'ambiente di pre-produzione, immettere il comando seguente:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Passaggio 6:** distribuire le risorse di Azure per i gruppi di risorse per ogni ambiente usando il file modello per l'applicazione e i file di parametri per ogni ambiente usando uno dei metodi seguenti. Entrambi i metodi avranno esattamente lo stesso risultato.

  **Metodo 1:** interfaccia della riga di comando di Azure (CLI)

  Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di sviluppo, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  Se avrà esito positivo, il comando restituirà quanto segue:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  Se il comando ha esito negativo, risolvere eventuali messaggi di errore e riprovare. I problemi comuni consistono nell'uso di valori di parametri non conformi ai vincoli di denominazione delle risorse di Azure. Per altri suggerimenti sulla risoluzione dei problemi, vedere l'articolo [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](virtual-machines/resource-group-deploy-debug.md).

  Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di test, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di pre-produzione, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Metodo 2:** PowerShell

  Da un prompt dei comandi di PowerShell digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di sviluppo, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti. Se si usa la versione di anteprima di Azure PowerShell 1.0, il comando è **New-AzureRmResourceGroupDeployment**, come illustrato di seguito. Se si usa una versione di Azure PowerShell precedente all'anteprima 1.0, il comando è **New-AzureResourceGroupDeployment**.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  Se avrà esito positivo, il comando restituirà quanto segue:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Se il comando ha esito negativo, risolvere eventuali messaggi di errore e riprovare. I problemi comuni consistono nell'uso di valori di parametri non conformi ai vincoli di denominazione delle risorse di Azure. Per altri suggerimenti sulla risoluzione dei problemi, vedere l'articolo [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](virtual-machines/resource-group-deploy-debug.md).

  Da un prompt dei comandi di PowerShell digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di test, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Da un prompt dei comandi di PowerShell digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di pre-produzione, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

I file modello e di parametri possono essere sottoposti al controllo delle versioni e gestiti con il codice dell'applicazione in un sistema di controllo del codice sorgente. È anche possibile salvare i comandi precedenti in file di script e salvarli nel codice stesso.

## Gestire gli ambienti
Durante lo sviluppo, la configurazione delle risorse di Azure nei diversi ambienti può essere resa incoerente in modo intenzionale o accidentale. Ciò può provocare attività superflue di risoluzione dei problemi durante il ciclo di sviluppo dell'applicazione.

  **Passaggio 7:** cambiare gli ambienti. Aprire il [portale di anteprima di Azure](https://portal.azure.com) e accedere al portale con lo stesso account usato per completare i passaggi precedenti. Come illustrato nella figura seguente, fare clic su Esplora tutto-->Gruppi di risorse. Potrebbe essere necessario scorrere verso il basso nel pannello Sfoglia per visualizzare i gruppi di risorse. Verranno visualizzati tutti e tre i gruppi di risorse creati usando uno dei metodi dei passaggi precedenti. Fare clic sul gruppo di risorse TestApp1-Development, come illustrato di seguito.

  ![Portale](./media/solution-dev-test-environments-preview-portal/portal1.png)

  Dopo la selezione del gruppo di risorse TestApp1-Development, verrà visualizzato il pannello che elenca le risorse create dal modello nella distribuzione del gruppo di risorse completato in un passaggio precedente. Eliminare la risorsa dell'app Web TestApp1Dev facendo clic su TestApp1Dev--> Elimina, come illustrato di seguito.

  ![Portale](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Fare clic su "Sì" quando il portale richiede di confermare l'eliminazione della risorsa. I contenuti del gruppo di risorse sono ora diversi da quanto previsto. È possibile eseguire altre prove eliminando più risorse da più gruppi di risorse o addirittura modificando le impostazioni di configurazione per alcune risorse.

> [AZURE.NOTE]Invece di usare il portale di anteprima di Azure per eliminare una risorsa da un gruppo di risorse, è possibile usare il comando [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) di PowerShell o il comando "azure resource delete" dall'interfaccia della riga di comando per eseguire la stessa attività.

  **Passaggio 8:** ridistribuire gli ambienti nei gruppi di risorse usando gli stessi comandi del Passaggio 6, ma sostituire "Deployment1" con "Deployment2". Come illustrato nella sezione Riepilogo della figura seguente, tutte le risorse del modello sono di nuovo presenti nel gruppo di risorse TestApp1-Development. Uno dei vantaggi della distribuzione degli ambienti con i modelli di Gestione risorse di Azure consiste nel fatto che è possibile ridistribuire facilmente gli ambienti per ripristinarne uno stato noto in qualsiasi momento.

  ![Portale](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Se si fa clic sul testo sotto "Ultima distribuzione" nella figura, verrà visualizzato un pannello che mostra la cronologia delle distribuzioni per il gruppo di risorse. Poiché è stato usato il nome "Deployment1" per la prima distribuzione e "Deployment2" per la seconda distribuzione, saranno presenti due voci. Se si fa clic su una distribuzione, verrà visualizzato un pannello che mostra i risultati per ogni distribuzione.

## Eliminare gli ambienti
Quando un ambiente non è più necessario, è consigliabile eliminarlo, in modo che non vengano effettuati addebiti per risorse di Azure che non vengono più usate. L'eliminazione di ambienti è addirittura più semplice della creazione. Nei passaggi precedenti sono stati creati singoli gruppi di risorse di Azure per ogni ambiente. Quando si elimina un gruppo di risorse, verranno eliminate anche tutte le risorse contenute nel gruppo. Tenendo presente questo, usare uno dei metodi seguenti per eliminare gli ambienti (gruppi di risorse), oltre a tutte le risorse di Azure incluse negli ambienti e distribuite in precedenza.

  **Passaggio 9:** eliminare gli ambienti usando uno dei metodi seguenti. Entrambi i metodi avranno esattamente lo stesso risultato.

  **Metodo 1: interfaccia della riga di comando di Azure**

  Da un prompt dell'interfaccia della riga di comando, digitare quanto segue:

	azure group delete "TestApp1-Development"

  Questo comando restituirà quanto segue, se si immette "y" quando richiesto:

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  Da un prompt dell'interfaccia della riga di comando, digitare quanto segue per eliminare gli ambienti rimanenti:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Metodo 2:** PowerShell

  Se si usa la versione di anteprima di Azure PowerShell 1.0, il comando per eliminare il gruppo di risorse è **Remove-AzureRmResourceGroup**, come illustrato di seguito. Se si usa una versione di Azure PowerShell precedente all'anteprima 1.0, il comando è **Remove-AzureResourceGroup**. Da un prompt di PowerShell, digitare quanto segue:

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  Questo comando restituirà quanto segue, se si immette "y" quando richiesto:

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  Da un prompt di PowerShell, digitare quanto segue per eliminare gli ambienti rimanenti:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

Indipendentemente dal metodo usato, quando l'esecuzione del comando viene completata, i gruppi di risorse e tutte le risorse in essi contenuti non esisteranno più e non verranno più sostenute spese di fatturazione per tali risorse.

Per ridurre al minimo le spese di utilizzo per le risorse di Azure sostenute durante lo sviluppo dell'applicazione, è possibile usare [Automazione di Azure](automation/automation-intro.md) per pianificare processi per:

- Arrestare le macchine virtuali alla fine di ogni giornata e riavviarle all'inizio di ogni giornata.
- Eliminare interi ambienti alla fine di ogni giornata e ricrearli all'inizio di ogni giornata.
 
Dopo avere constatato la semplicità dei processi di creazione, gestione ed eliminazione di ambienti di sviluppo e di test, è possibile ottenere altre informazioni eseguendo altri esperimenti con i passaggi precedenti e leggendo i riferimenti inclusi in questo articolo.

## Passaggi successivi

- [Delegare il controllo amministrativo](role-based-access-control-configure.md) a risorse diverse in ogni ambiente, assegnando gruppi o utenti di Microsoft Azure AD a ruoli specifici, in grado di eseguire un sottoinsieme di operazioni sulle risorse di Azure.
- [Assegnare tag](resource-group-using-tags.md) ai gruppi di risorse per ogni ambiente e/o per le singole risorse. È possibile aggiungere un tag "Environment" ai gruppi di risorse e impostarne il valore in modo che corrisponda ai nomi di ambiente. I tag possono essere particolarmente utili quando occorre organizzare le risorse per la fatturazione o la gestione.
- Monitorare gli avvisi e la fatturazione per le risorse del gruppo di risorse nel [portale di anteprima di Azure](https://portal.azure.com).

## Risorse aggiuntive

- [Creare e distribuire i modelli di Gestione risorse di Azure in Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) con Azure SDK 2.6 installato.
- Creare l'applicazione usando [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) o [Web Matrix](http://www.microsoft.com/web/webmatrix/).
- [Distribuire un'app Web](app-service-web/web-sites-deploy.md) negli ambienti creati.
- Usare [Release Management per Visual Studio](http://msdn.microsoft.com/Library/vs/alm/Release/overview) per creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente.
- Richiedere un invito per l'anteprima del [Lab di sviluppo/test di Azure](http://azure.microsoft.com/campaigns/devtest-lab/). Consente di gestire ambienti lab di sviluppo e test usando i modelli e di configurare le quote e i criteri da usare nell'organizzazione.

<!---HONumber=Nov15_HO2-->