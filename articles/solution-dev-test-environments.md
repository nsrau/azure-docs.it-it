<properties
   pageTitle="Ambienti di sviluppo e test | Microsoft Azure"
   description="Informazioni su come usare i modelli di Gestione risorse di Azure per creare ed eliminare in modo rapido e coerente ambienti di sviluppo e di test."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="jdial"/>

# Ambienti di sviluppo e test in Microsoft Azure

Le applicazioni personalizzate vengono in genere distribuite in più ambienti di sviluppo e test prima della distribuzione in produzione. Quando gli ambienti vengono creati in locale, le risorse di calcolo vengono prodotte o allocate per ogni ambiente per ogni applicazione. Gli ambienti includono spesso alcune macchine virtuali o fisiche con configurazioni specifiche, distribuite manualmente o con complessi script di automazione. Le distribuzioni richiedono spesso alcune ore e hanno come risultato configurazioni non coerenti nei diversi ambienti.

## Scenario ##

Quando si esegue il provisioning degli ambienti di sviluppo e test in Microsoft Azure, si pagano solo le risorse usate. Questo articolo spiega che è possibile creare, gestire ed eliminare in modo rapido e coerente gli ambienti di sviluppo e test usando i modelli e i file di parametri di Gestione risorse di Azure, come illustrato di seguito.

![Scenario](./media/solution-dev-test-environments/scenario.png)

La figura precedente mostra tre ambienti di sviluppo e test. Ognuno include risorse per l'applicazione Web e il database SQL, specificate in un file modello. I nomi dell'applicazione e del database in ogni ambiente sono diversi e vengono specificati in file di parametri univoci per ogni ambiente.

Se non si ha familiarità con i concetti relativi a Gestione risorse di Azure, è consigliabile leggere l'articolo [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md) prima di questo articolo.

È consigliabile esaminare prima di tutto i passaggi elencati in questo articolo, senza leggere gli articoli a cui si fa riferimento, per acquisire esperienza nell'uso dei modelli di Gestione risorse di Azure. Dopo avere esaminato i passaggi una volta, si potranno ottenere le risposte alla maggior parte delle domande insorte durante la prima analisi provando ad approfondire le procedure e leggendo gli articoli a cui si fa riferimento.

## Pianificare l'uso delle risorse di Azure
Dopo avere creato una struttura generale per l'applicazione, sarà possibile definire gli elementi seguenti:

- Risorse di Azure che verranno incluse nell'applicazione. È possibile compilare l'applicazione e distribuirla come app Web di Azure con un database SQL di Azure oppure compilare l'applicazione in macchine virtuali usando PHP e MySQL o IIS e SQL Server o altri componenti. L'articolo [Confronto tra servizio app, Servizi cloud e Macchine virtuali di Azure](app-service-web/choose-web-site-cloud-service-vm.md) è utile per decidere quali risorse di Azure utilizzare per l'applicazione.
- Requisiti del livello di servizio, ad esempio disponibilità, sicurezza e scalabilità, che verranno soddisfatti dall'applicazione.

## Download di un modello esistente
Un modello di Gestione risorse di Azure definisce tutte le risorse di Azure utilizzate dall'applicazione. Esistono già alcuni modelli che possono essere distribuiti direttamente nel portale di Azure o scaricati, modificati e salvati in un sistema di controllo del codice sorgente con il codice dell'applicazione. Completare la procedura seguente per scaricare un modello esistente.

1. Per un elenco di modelli disponibili, vedere i [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/) nel repository GitHub. L'elenco include una cartella "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Poiché molte applicazioni personalizzate includono un'applicazione Web e un database SQL, questo modello viene usato come esempio per il resto dell'articolo, per semplificare la comprensione dell'uso dei modelli. La spiegazione completa di tutti gli elementi creati e configurati da questo modello non rientra nell'ambito dell'articolo, ma se si prevede di usarlo per creare ambienti effettivi nell'organizzazione, è consigliabile ottenere informazioni dettagliate leggendo l'articolo [Eseguire il provisioning di un'app Web con un database SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md). Nota: questo articolo è stato scritto per la versione di dicembre 2015 del modello [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database). I collegamenti seguenti che puntano ai file del modello e dei parametri fanno riferimento a tale versione del modello.
2. Fare clic sul file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) nella cartella 201-web-app-sql-database per visualizzarne il contenuto. Questo è il file modello di Gestione risorse di Azure.
3. Nella modalità di visualizzazione fare clic sul pulsante "[Non elaborato](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)".
4. Con il mouse selezionare il contenuto del file e salvarlo nel computer come file con nome "TestApp1-Template.json".
5. Esaminare il contenuto del modello e tenere presente quanto segue:
 - Sezione **Resources**: definisce i tipi di risorse di Azure create da questo modello. Tra gli altri tipi di risorse, questo modello crea risorse di tipo [App Web di Azure](app-service-web/app-service-web-overview.md) e [Database SQL di Azure](sql-database/sql-database-technical-overview.md). Se si preferisce eseguire e gestire server Web e SQL in macchine virtuali, è possibile usare il modello "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" o "[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", ma le istruzioni riportate in questo articolo si basano sul modello [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database).
 - Sezione **Parameters**: definisce i parametri con cui è possibile configurare ogni risorsa. Alcuni parametri specificati nel modello hanno proprietà "defaultValue", altri no. Quando si distribuiscono risorse di Azure con un modello, è necessario fornire valori per tutti i parametri privi delle proprietà defaultValue specificate nel modello. Se non si forniscono valori per i parametri con proprietà defaultValue, verrà usato il valore specificato per il parametro defaultValue nel modello.

Un modello definisce le risorse di Azure create e i parametri con cui è possibile configurare ogni risorsa. Per altre informazioni sui modelli e su come progettare modelli personalizzati, leggere l'articolo [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](best-practices-resource-manager-design-templates.md).

## Scaricare e personalizzare un file di parametri esistente

È probabile che si vogliano creare le *stesse* risorse di Azure in ogni ambiente, ma con una configurazione delle risorse *diversa* per ogni ambiente. I file di parametri servono a questo. Per creare file di parametri che contengono valori univoci in ogni ambiente, completare i passaggi seguenti.

1. Visualizzare il contenuto del file [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) nella cartella 201-web-app-sql-database. Questo è il file di parametri per il file modello salvato nella sezione precedente.
2. Nella modalità di visualizzazione fare clic sul pulsante "[Non elaborato](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)".
3. Con il mouse selezionare il contenuto del file e salvarlo nel computer in tre file separati con i nomi seguenti:
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. Con un editor di testo o JSON modificare il file di parametri per l'ambiente di sviluppo creato nel passaggio 3, sostituendo i valori elencati a destra dei valori dei parametri nel file con i *valori* elencati a destra dei **parametri** seguenti:
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Stati Uniti centrali*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *Stati Uniti centrali*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *sostituire con la propria password*
 - **databaseName**: *testapp1devdb*

4. Con un editor di testo o JSON modificare il file di parametri per l'ambiente di test creato nel passaggio 3, sostituendo i valori elencati a destra dei valori dei parametri nel file con i *valori* elencati a destra dei **parametri** seguenti:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPla*n
 - **siteLocation**: *Stati Uniti centrali*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *Stati Uniti centrali*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *sostituire con la propria password*
 - **databaseName**: *testapp1testdb*

5. Con un editor di testo o JSON modificare il file di parametri per l'ambiente di pre-produzione creato nel passaggio 3. Sostituire l'intero contenuto del file con il codice seguente:

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
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

Il motivo per cui nell'ambiente di pre-produzione vengono usati valori non predefiniti per questi parametri consiste nel testare i valori eventualmente preferibili per questi parametri nell'ambiente di produzione e consentirne la verifica. Questi parametri sono tutti correlati ai valori di [piani di hosting delle app Web](https://azure.microsoft.com/pricing/details/app-service/) o **sku** e [Database SQL](https://azure.microsoft.com/pricing/details/sql-database/) di Azure oppure **requestedServiceObjectiveName** usati dall'applicazione. Diversi SKU e nomi di oggetti del servizio hanno costi e funzionalità diverse e supportano diverse metriche relative ai livelli di servizio.

La tabella seguente elenca i valori predefiniti per i parametri specificati nel modello e i valori usati invece dei valori predefiniti nel file di parametri pre-produzione.

| Parametro | Valore predefinito | Valore del file di parametri |
|---|---|---|
| **sku** | Gratuito | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Creare ambienti
Tutte le risorse di Azure devono essere create entro un [Gruppo di risorse di Azure](resource-group-overview.md). I gruppi di risorse consentono di raggruppare le risorse di Azure, in modo che sia possibile gestirle insieme. È possibile assegnare [autorizzazioni](./active-directory/role-based-access-built-in-roles.md) ai gruppi di risorse, in modo che persone specifiche dell'organizzazione possano creare, modificare, eliminare o visualizzare i gruppi e le risorse disponibili nei gruppi. Gli avvisi e le informazioni di fatturazione per le risorse incluse nel gruppo di risorse possono essere visualizzati nel [portale di Azure](https://portal.azure.com). I gruppi di risorse vengono creati in un'[area](https://azure.microsoft.com/regions/) di Azure. In questo articolo tutte le risorse vengono create nell'area Central US. Quando si inizia a creare ambienti effettivi, si sceglierà l'area più adatta alle proprie esigenze.

Creare gruppi di risorse per ogni ambiente usando uno dei metodi seguenti. Tutti i metodi avranno lo stesso risultato.

###Interfaccia della riga di comando di Azure

Assicurarsi che l'interfaccia della riga di comando sia [installata](xplat-cli-install.md) in un computer Windows, OS X o Linux e di avere [connesso](xplat-cli-connect.md) l'[account Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md), detto anche account aziendale o dell'istituto di istruzione, alla sottoscrizione di Azure. Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo.

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

###PowerShell

Assicurarsi che Azure PowerShell 1.01 o versione successiva sia installato in un computer Windows e di avere connesso l'[account Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md), detto anche account aziendale o dell'istituto di istruzione, alla propria sottoscrizione, come illustrato in dettaglio nell'articolo [Come installare e configurare Azure PowerShell](powershell-install-configure.md). Da un prompt dei comandi di PowerShell digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Se avrà esito positivo, il comando restituirà quanto segue:

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Per creare il gruppo di risorse per l'ambiente di test, immettere il comando seguente:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Per creare il gruppo di risorse per l'ambiente di pre-produzione, immettere il comando seguente:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md), detto anche account aziendale o dell'istituto di istruzione. Fare clic su Nuovo--> Gestione--> Gruppo di risorse e immettere "TestApp1-Development" nella casella Nome gruppo di risorse, selezionare la sottoscrizione e "Stati Uniti centrali" nella casella Località del gruppo di risorse, come illustrato nell'immagine seguente. ![di Microsoft Azure](./media/solution-dev-test-environments/rgcreate.png)
2. Fare clic sul pulsante Crea per creare il gruppo di risorse.
3. Fare clic su Sfoglia, scorrere l'elenco verso il basso fino a Gruppi di risorse e selezionare questa voce, come illustrato di seguito. ![di Microsoft Azure](./media/solution-dev-test-environments/rgbrowse.png)
4. Dopo aver fatto clic su Gruppi di risorse, verrà visualizzato il pannello Gruppi di risorse con il nuovo gruppo di risorse. ![di Microsoft Azure](./media/solution-dev-test-environments/rgview.png)
5. Creare i gruppi di risorse TestApp1-Test e TestApp1-Pre-Production seguendo la stessa procedura usata per creare il gruppo di risorse TestApp1-Development precedente.

##Distribuire risorse negli ambienti

Distribuire le risorse di Azure nei gruppi di risorse per ogni ambiente usando il file modello per l'applicazione e i file di parametri per ogni ambiente con uno dei metodi seguenti. Entrambi i metodi avranno lo stesso risultato.

###Interfaccia della riga di comando di Azure

Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di sviluppo, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Dopo la visualizzazione per alcuni minuti di un messaggio di attesa del completamento della distribuzione, se il comando riesce restituirà quanto segue:

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
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

Se il comando ha esito negativo, risolvere eventuali messaggi di errore e riprovare. I problemi comuni consistono nell'uso di valori di parametri non conformi ai vincoli di denominazione delle risorse di Azure. Per altri suggerimenti sulla risoluzione dei problemi, vedere l'articolo [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-manager-troubleshoot-deployments-cli.md).

Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di test, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Dalla riga di comando dell'interfaccia della riga di comando digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di pre-produzione, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

Al prompt dei comandi di Azure PowerShell, versione 1.01 o successiva, digitare il comando seguente per distribuire le risorse nel gruppo di risorse creato per l'ambiente di sviluppo, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Dopo la visualizzazione di un cursore intermittente per alcuni minuti, se il comando riesce restituirà quanto segue:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Se il comando ha esito negativo, risolvere eventuali messaggi di errore e riprovare. I problemi comuni consistono nell'uso di valori di parametri non conformi ai vincoli di denominazione delle risorse di Azure. Per altri suggerimenti sulla risoluzione dei problemi, vedere l'articolo [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-manager-troubleshoot-deployments-powershell.md).

  Da un prompt dei comandi di PowerShell digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di test, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Da un prompt dei comandi di PowerShell digitare il comando seguente per distribuire risorse nel gruppo di risorse creato dall'ambiente di pre-produzione, sostituendo [path] con il percorso nei file salvati nei passaggi precedenti.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

I file modello e di parametri possono essere sottoposti al controllo delle versioni e gestiti con il codice dell'applicazione in un sistema di controllo del codice sorgente. È anche possibile salvare i comandi precedenti in file di script e salvarli nel codice stesso.

> [AZURE.NOTE] È possibile distribuire il modello direttamente in Azure facendo clic sul pulsante "Distribuisci in Azure" nell'articolo [Eseguire il provisioning di un'app Web con un database SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Questo approccio può risultare utile per approfondire la propria conoscenza dei modelli, ma ciò non consente di modificare, sottoporre al controllo delle versioni e salvare il modello e i valori dei parametri con il codice dell'applicazione, quindi in questo articolo non vengono forniti altri dettagli relativi a questo metodo.

## Gestire gli ambienti
Durante lo sviluppo, la configurazione delle risorse di Azure nei diversi ambienti può essere resa incoerente in modo intenzionale o accidentale. Ciò può provocare attività superflue di risoluzione dei problemi durante il ciclo di sviluppo dell'applicazione.

1. Modificare gli ambienti aprendo il [portale di Azure](https://portal.azure.com).
2. Accedere al portale con lo stesso account usato per completare i passaggi precedenti.
3. Come illustrato nella figura seguente, fare clic su Esplora--> Gruppi di risorse. Potrebbe essere necessario scorrere verso il basso per visualizzare Gruppi di risorse. ![di Microsoft Azure](./media/solution-dev-test-environments/rgbrowse.png)
4. Dopo aver fatto clic su Gruppi di risorse nell'immagine precedente, verrà visualizzato il pannello Gruppi di risorse e i tre gruppi di risorse creati in un passaggio precedente, come illustrato nell'immagine seguente. Fare clic sul gruppo di risorse TestApp1-Development. Verrà visualizzato il pannello con l'elenco delle risorse create dal modello nella distribuzione del gruppo di risorse TestApp1-Development completata in un passaggio precedente. Eliminare la risorsa app Web TestApp1DevApp facendo clic su TestApp1DevApp nel pannello del gruppo di risorse TestApp1-Development e quindi facendo clic su Elimina nel pannello dell'app Web TestApp1DevApp. ![di Microsoft Azure](./media/solution-dev-test-environments/portal2.png)
5. Fare clic su "Sì" quando il portale richiede di confermare l'eliminazione della risorsa. Chiudendo il pannello del gruppo di risorse TestApp1-Development e riaprendolo, l'app Web appena eliminata non sarà più visibile. I contenuti del gruppo di risorse sono ora diversi da quanto previsto. È possibile eseguire altre prove eliminando più risorse da più gruppi di risorse o addirittura modificando le impostazioni di configurazione per alcune risorse. Invece di usare il portale di Azure per eliminare una risorsa da un gruppo di risorse, è possibile usare il comando di PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) o il comando "azure resource delete" dall'interfaccia della riga di comando per eseguire la stessa attività.
6. Per ripristinare lo stato in cui devono essere tutte le risorse e la configurazione incluse nei gruppi di risorse, distribuire di nuovo gli ambienti nei gruppi di risorse con gli stessi comandi usati nella sezione [Distribuire risorse negli ambienti](#deploy-resources-to-environments), sostituendo "Deployment1" con "Deployment2".
7.  Come illustrato nella sezione di riepilogo del pannello TestApp1-Development nell'immagine illustrata nel passaggio 4, si noterà che l'app Web eliminata nel portale nel passaggio precedente è ancora presente, così come le altre risorse che si è scelto di eliminare. Se è stata modificata la configurazione di una delle risorse, è possibile verificare che siano stati ripristinati i relativi valori anche nei file di parametri. Uno dei vantaggi della distribuzione degli ambienti con i modelli di Gestione risorse di Azure consiste nel fatto che è possibile ridistribuire facilmente gli ambienti per ripristinarne uno stato noto in qualsiasi momento.
8. Se si fa clic sul testo sotto "Ultima distribuzione" nella figura seguente, verrà visualizzato un pannello che mostra la cronologia delle distribuzioni per il gruppo di risorse. Poiché è stato usato il nome "Deployment1" per la prima distribuzione e "Deployment2" per la seconda distribuzione, saranno presenti due voci. Se si fa clic su una distribuzione, verrà visualizzato un pannello che mostra i risultati per ogni distribuzione. ![di Microsoft Azure](./media/solution-dev-test-environments/portal3.png)



## Eliminare gli ambienti
Quando non è più necessario usare un ambiente, è consigliabile eliminarlo, in modo da evitare addebiti per l'utilizzo di risorse di Azure che non vengono più usate. L'eliminazione di ambienti è addirittura più semplice della creazione. Nei passaggi precedenti sono stati creati singoli gruppi di risorse di Azure per ogni ambiente e quindi sono state distribuite risorse nei gruppi di risorse.

Eliminare gli ambienti usando uno dei metodi seguenti. Tutti i metodi avranno lo stesso risultato.

### Interfaccia della riga di comando di Azure

Da un prompt dell'interfaccia della riga di comando, digitare quanto segue:

	azure group delete "TestApp1-Development"

Quando richiesto, immettere y e quindi premere INVIO per rimuovere l'ambiente di sviluppo e tutte le relative risorse. Dopo alcuni minuti il comando restituirà quanto segue:

	info:    group delete command OK

Da un prompt dell'interfaccia della riga di comando, digitare quanto segue per eliminare gli ambienti rimanenti:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

Al prompt dei comandi di Azure PowerShell, versione 1.01 o successiva, digitare il comando seguente per eliminare il gruppo di risorse e tutto il relativo contenuto.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Quando richiesto, se si è certi di che voler rimuovere il gruppo di risorse, immettere y e premere INVIO.

Digitare quanto segue per eliminare gli ambienti rimanenti:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Portale di Azure

1. Nel portale di Azure passare a Gruppi di risorse come è stato fatto nei passaggi precedenti.
2. Selezionare il gruppo di risorse TestApp1-Development e quindi fare clic su Elimina nel pannello del gruppo di risorse TestApp1-Development. Verrà visualizzato un nuovo pannello. Immettere il nome del gruppo di risorse e fare clic sul pulsante Elimina. ![di Microsoft Azure](./media/solution-dev-test-environments/rgdelete.png)
3. Eliminare i gruppi di risorse TestApp1-Test e TestApp1-Pre-Production seguendo la stessa procedura usata per eliminare il gruppo di risorse TestApp1-Development.

Indipendentemente dal metodo usato, i gruppi di risorse e tutte le risorse che contengono non esisteranno più e non verranno più fatturati costi per tali risorse.

Per ridurre al minimo le spese di utilizzo per le risorse di Azure sostenute durante lo sviluppo dell'applicazione, è possibile usare [Automazione di Azure](automation/automation-intro.md) per pianificare processi per:

- Arrestare le macchine virtuali alla fine di ogni giornata e riavviarle all'inizio di ogni giornata.
- Eliminare interi ambienti alla fine di ogni giornata e ricrearli all'inizio di ogni giornata.
 
Dopo avere constatato la semplicità dei processi di creazione, gestione ed eliminazione di ambienti di sviluppo e di test, è possibile ottenere altre informazioni eseguendo altri esperimenti con i passaggi precedenti e leggendo i riferimenti inclusi in questo articolo.

## Passaggi successivi

- [Delegare il controllo amministrativo](./active-directory/role-based-access-control-configure.md) a risorse diverse in ogni ambiente, assegnando gruppi o utenti di Microsoft Azure AD a ruoli specifici, in grado di eseguire un sottoinsieme di operazioni sulle risorse di Azure.
- [Assegnare tag](resource-group-using-tags.md) ai gruppi di risorse per ogni ambiente e/o per le singole risorse. È possibile aggiungere un tag "Environment" ai gruppi di risorse e impostarne il valore in modo che corrisponda ai nomi di ambiente. I tag possono essere particolarmente utili quando è necessario organizzare le risorse per la fatturazione o la gestione.
- Monitorare gli avvisi e la fatturazione per le risorse del gruppo di risorse nel [portale di Azure](https://portal.azure.com).

<!---HONumber=AcomDC_0824_2016-->