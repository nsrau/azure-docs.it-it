<properties 
	pageTitle="Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse" 
	description="Uso dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure con Gestione risorse" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="rasquill"/>

#Uso dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">Interfaccia della riga di comando multipiattaforma</a></div>

In questo articolo verrà illustrato come usare l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) in modo che funzioni con Gestione risorse. 

>[AZURE.NOTE] Gestione risorse è attualmente in versione di anteprima e non fornisce lo stesso livello delle funzionalità di gestione disponibili con Gestione servizi di Azure. 
>
> Se non è stato ancora installato e configurato xplat-cli, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure ][xplatsetup] per altre informazioni sull'installazione, la configurazione e l'uso di xplat-cli.


##Gestione risorse

Gestione risorse consente di gestire un gruppo di _risorse_ (entità gestite dall'utente quali un server di database, un database o un sito Web) come una singola unità logica o _gruppo di risorse_. Un gruppo di risorse potrebbe ad esempio contenere risorse corrispondenti a un sito Web e a un database SQL.

Per supportare una modalità più dichiarativa per descrivere le modifiche alle risorse all'interno di un gruppo di risorse, Gestione risorse usa  *templates*, che sono documenti JSON. Il linguaggio del modello consente inoltre di descrivere parametri che possono essere compilati inline quando si esegue un comando oppure archiviati in un file JSON a parte. Ciò consente di creare facilmente nuove risorse usando lo stesso modello e semplicemente fornendo parametri diversi. Un modello che crea un sito Web disporrà ad esempio di parametri per il nome del sito, per l'area in cui verrà collocato e per altri parametri comuni.

>[AZURE.NOTE] Le specifiche del linguaggio dei modelli non sono al momento documentate. Quando la documentazione verrà resa disponibile, questo argomento verrà aggiornato con l'aggiunta di un collegamento a tale documentazione di riferimento.
>
> È possibile, tuttavia, utilizzare il comando  `azure group template download` per scaricare dalla raccolta modelli i modelli forniti da Microsoft e dai partner e quindi modificarli.

Quando un modello viene usato per modificare o creare un gruppo, viene creata una _distribuzione_, che viene quindi applicata al gruppo.

##Autenticazione

Attualmente, l'uso di Gestione risorse tramite xplat-cli richiede l'autenticazione a Microsoft Azure con un account di lavoro o studio. L'autenticazione tramite un account Microsoft o un certificato installato mediante un file .publishsettings non è supportata.

Per altre informazioni sull'autenticazione tramite un account aziendale, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure][xplatsetup].

##Individuazione e la configurazione di un modello di gruppo di risorse

1. Poiché la modalità Gestione risorse non è attivata per impostazione predefinita, è necessario utilizzare il comando seguente per abilitare i comandi di gestione risorse di xplat-cli.

		azure config mode arm

	>[AZURE.NOTE] La modalità Gestione risorse e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

2. Quando si lavora con i modelli, è possibile crearne uno personalizzato oppure usarne uno dalla raccolta modelli. In questo caso, verrà utilizzato uno dalla raccolta di modelli. Per ottenere un elenco dei modelli disponibili nella raccolta, usare il comando seguente. (Poiché sono presenti migliaia di modelli, assicurarsi di impaginare i risultati o utilizzare **grep** o **findstr**[in Windows] o il comando preferito di ricerca stringa per individuare modelli interessanti. In alternativa, è possibile utilizzare l'opzione **-- json** e scaricare l'intero elenco in formato JSON per la ricerca semplice. Nell'esempio seguente viene utilizzato il modello denominato **Microsoft.WebSiteSQLDatabase.0.2.6-preview**.)	

		azure group template list

	La risposta conterrà i server di pubblicazione e il nome del modello e avrà un aspetto simile al seguente (ma con molti più elementi).

		data: Publisher Name
		data: ----------------------------------------------------------------------------
		data: Microsoft Microsoft.WebSite.0.1.0-preview1
		data: Microsoft Microsoft.PHPStarterKit.0.1.0-preview1
		data: Microsoft Microsoft.HTML5EmptySite.0.1.0-preview1
		data: Microsoft Microsoft.ASPNETEmptySite.0.1.0-preview1
		data: Microsoft Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Per visualizzare i dettagli di un modello che creerà un sito Web di Azure, usare il comando seguente.

		azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-preview

	Verranno restituite informazioni descrittive sul modello. 

4. Dopo aver selezionato un modello (**zure group template show anteprima Microsoft.WebSiteSQLDatabase.0.2.6**), è possibile scaricare con il comando seguente.

		azure group template download Microsoft.WebSiteSQLDatabase.0.2.6-preview

	Dopo avere scaricato un modello, è possibile personalizzarlo per meglio adattarlo alle proprie esigenze, ad esempio aggiungendovi una nuova risorsa.

	>[AZURE.NOTE] Se si modifica il modello, utilizzare il comando  `azure group template validate` per convalidarlo prima di usarlo per creare o modificare un gruppo di risorse esistente.

5. Per configurare il modello di gruppo di risorse, aprire il file di modello in un editor di testo. Notare raccolta JSON **parameters** nella parte iniziale. Questa contiene un elenco dei parametri previsti da questo modello per poter creare le risorse descritte dal modello stesso. Alcuni parametri, come **sku**, presentano valori predefiniti, mentre altri specificano semplicemente il tipo di valore, ad esempio **siteName**. 
	
	Quando si usa un modello, è possibile specificare parametri insieme ai parametri della riga di comando oppure specificando un file contenente i valori desiderati. In entrambi i casi, i parametri devono essere in formato JSON ed è necessario specificare valori personalizzati per le chiavi che non dispongono di valori predefiniti.

	Ad esempio, per creare un file che contiene i parametri per il modello **Microsoft.WebSiteSQLDatabase.0.2.6-preview**, utilizzare i dati seguenti per creare un file denominato **params**. Sostituire i valori seguenti che iniziano con **_My_** come **_MyWebSite_** con valori personalizzati. **siteLocation** deve specificare un'area di Azure, ad esempio **North Europe** o **South Central US**. In questo esempio viene utilizzata l'area **West US**.

		{
		 "siteName": {
		 "value": "MyWebSite"
		 },
		 "hostingPlanName": {
		 "value": "MyHostingPlan"
		 },
		 "siteLocation": {
		 "value": "West US"
		 },
		 "serverName": {
		 "value": "MySQLServer"
		 },
		 "serverLocation": {
		 "value": "West US"
		 },
		 "administratorLogin": {
		 "value": "MySQLAdmin"
		 },
		 "administratorLoginPassword": {
		 "value": "MySQLAdminPassword"
		 },
		 "databaseName": {
		 "value": "MySQLDB"
		 }
		}


1. Dopo aver salvato il file **params.json**, utilizzare il seguente comando per creare una nuova risorse in base al modello. Il parametro `-e` specifica il file **params.json** creato nel passaggio precedente. Sostituire **MyGroupName** con il nome del gruppo da utilizzare e **MyDataCenter** con il valore di **siteLocation** specificato nel file dei parametri del modello **params.json**.

		azure group create MyGroupName "West US" -f Microsoft.WebSiteSQLDatabase.0.2.6-preview.json -d MyDeployment -e params.json

	>[AZURE.NOTE] Questo comando restituirà OK dopo il caricamento della distribuzione, ma prima che quest'ultima sia stata applicata alle risorse nel gruppo. Per verificare lo stato della distribuzione, usare il comando seguente.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> **ProvisioningState** mostra lo stato della distribuzione.
	> 
	> Se ci si accorge che la configurazione non è corretta ed è necessario arrestare una distribuzione a lunga esecuzione, usare il comando seguente.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> Se non si fornisce un nome di distribuzione, ne verrà creato uno automaticamente in base al nome del file di modello. Verrà restituito come parte dell'output del comando  `azure group create`.

3. Per visualizzare il gruppo, usare il comando seguente:

		azure group show MyGroupName

	Questo comando restituisce informazioni relative alle risorse nel gruppo. Se non presenti più gruppi, è possibile utilizzare il comando  `azure group list` per recuperare un elenco di nomi di gruppo, quindi usare il comando  `azure group show` per visualizzare i dettagli di un gruppo specifico.

##Utilizzo delle risorse

Mentre i modelli consentono di dichiarare modifiche a livello di interi gruppi in ambito di configurazione, è talvolta necessario lavorare su una risorsa specifica. È anche possibile utilizzare i comandi  `azure resource`.

> [AZURE.NOTE] Quando si utilizzano comandi  `azure resource` diversi da  `list`, è necessario specificare la versione dell'API della risorse utilizzata tramite il comando `-o`. In caso di subbio sulla versione dell'API da utilizzare, consultare il file di modello e individuare il campo **apiVersion** relativo alla risorsa.

1. Per elencare tutte le risorse in un gruppo, usare il comando seguente.

		azure resource list MyGroupName

1. Per visualizzare risorse individuali, ad esempio il sito Web, all'interno del gruppo, usare il comando seguente.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	Notare il parametro **Microsoft.Web/sites**. Questo indica il tipo di risorsa per il quale si stanno richiedendo informazioni. Se si scarica il file di modello scaricato in precedenza, si noterà che lo stesso valore viene usato per definire il tipo di risorsa di sito Web descritta nel modello.

	Questo comando restituisce informazioni relative al sito Web. Ad esempio, il campo **hostNames** deve contenere L'URL del sito Web. Usarlo nel browser per verificare che il sito Web sia in esecuzione.

2. Quando si visualizzano i dettagli relativi a una risorsa, è spesso utile usare il parametro `--json`, in quanto quest'ultimo rende l'output più leggibile in presenza di valori quali strutture nidificate o raccolte. Nell'esempio seguente viene illustrata la restituzione dei risultati del comando show sotto forma di documento JSON.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[AZURE.NOTE] È possibile salvare i dati JSON nel file utilizzando il carattere &gt; per inviare l'output al file. Ad esempio:
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. Per eliminare una risorsa esistente, usare il comando seguente.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##Registrazione

Per visualizzare informazioni registrate sulle operazioni eseguite su un gruppo, utilizzare il comando  `azure group log show`. Per impostazione predefinita, questo comando restituisce l'ultima operazione eseguita sul gruppo. Per visualizzare tutte le operazioni, usare il parametro facoltativo `--all`. Per l'ultima distribuzione, usare `--last-deployment`. Per una distribuzione specifica, usare, use `--deployment` e specificare il nome della distribuzione. Nell'esempio seguente viene restituito un log di tutte le operazioni eseguite sul gruppo  'MyGroup'.

	azure group log show mygroup --all

##Passaggi successivi

* Per ulteriori informazioni sull'utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][xplatsetup].
* Per informazioni sull'utilizzo di Gestione risorse con Windows Azure PowerShell, vedere [IGetting Started using Windows PowerShell with Resource Manager][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!--HONumber=47-->
