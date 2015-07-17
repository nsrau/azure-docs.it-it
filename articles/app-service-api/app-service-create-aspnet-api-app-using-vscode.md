<properties
   pageTitle="Creare un'app per le API ASP.NET 5 in Visual Studio Code"
   description="Questa esercitazione illustra come creare un'app per le API ASP.NET 5 usando Visual Studio Code."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Creare un'app per le API ASP.NET 5 in Visual Studio Code

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Panoramica

Questa esercitazione illustra come creare un'app per le API ASP.NET 5 usando [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode). ASP.NET 5 è una riprogettazione significativa di ASP.NET. ASP.NET 5 è un nuovo framework open source e multipiattaforma per la compilazione tramite .NET di moderne applicazioni Web basate sul cloud. Per altre informazioni, vedere [Introduzione ad ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Per informazioni sulle app per le API, vedere [Informazioni sulle app per le API](app-service-api-apps-why-best-platform.md).

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/) oppure [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/). È anche possibile provare gratuitamente gli [esempi di app del servizio app](http://tryappservice.azure.com).

## Prerequisiti  

* Installare e configurare [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Installare [Node.js](http://nodejs.org/download/).<br\> 
	[Node.js](http://nodejs.org/) è una piattaforma per la creazione di applicazioni server veloci e scalabili tramite JavaScript. Node è il runtime (nodo), mentre [npm](http://www.npmjs.com/) è lo strumento di gestione pacchetti per i moduli Node. In questa esercitazione si userà npm per eseguire lo scaffolding di un'app per le API ASP.NET 5.

## Installare ASP.NET 5 e DNX
ASP.NET 5/DNX è uno stack .NET snello per la creazione di un cloud moderno e di app Web in esecuzione su OS X, Linux e Windows. È stato completamente riprogettato per fornire un framework di sviluppo ottimizzato per le app che vengono distribuite nel cloud o eseguite in locale. È costituito da componenti modulari con un overhead minimo, in modo da garantire la massima flessibilità durante la creazione di soluzioni.

> [AZURE.NOTE]ASP.NET 5 e DNX (l'ambiente di esecuzione .NET) sono ancora in versione Beta/Anteprima per OS X e Linux.

Questa esercitazione è stata realizzata per consentire la creazione di applicazioni con le più recenti versioni di sviluppo di ASP.NET 5 e DNX. Per un'esperienza più stabile e rilasciata, visitare la pagina [http://www.asp.net/vnext](http://www.asp.net/vnext). Le istruzioni seguenti sono specifiche di Windows. Per istruzioni di installazione più dettagliate per OS X, Linux e Windows, vedere l'articolo relativo all'[installazione di ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Per installare .NET Version Manager (DNVM) in Windows, eseguire il comando seguente nella finestra di comando:

	<pre class="prettyprint">
	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
	</pre>
	Lo script DNVM verrà scaricato e inserito nel profilo utente.

2. Potrebbe essere necessario disconnettersi dopo aver immesso il comando precedente per rendere effettiva la modifica della variabile di ambiente PATH.
3. Controllare la posizione di DNVM eseguendo quanto segue nella finestra di comando: 

	<pre class="prettyprint">
	where dnvm
	</pre>
	La finestra di comando visualizzerà un percorso analogo al seguente:

	![Percorso dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Dopo aver scaricato DNVM, è necessario usarlo per scaricare DNX per eseguire le applicazioni. Eseguire il comando seguente nella finestra di comando:

	<pre class="prettyprint">
	dnvm upgrade
	</pre>

5. Verificare DNVM e visualizzare il runtime attivo immettendo quanto segue nella finestra di comando.

	<pre class="prettyprint">
	dnvm list
	</pre>
	La finestra di comando visualizzerà i dettagli del runtime attivo:

	![Percorso dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Per istruzioni di installazione più dettagliate per OS X, Linux e Windows, vedere l'articolo relativo all'[installazione di ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Creare l'app per le API 

Questa sezione illustra come eseguire lo scaffolding di una nuova app per le API ASP.NET. Per installare [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) e [Bower](http://bower.io/) si userà node package manager (npm).

1. Dopo l'installazione di Visual Studio Code e Node.js, aprire un prompt dei comandi con diritti di amministratore e passare al percorso in cui si prevede di inserire tutti i progetti ASP.NET tramite VSCode.
2. Digitare il comando seguente nella finestra di comando per installare Yeoman e gli strumenti di supporto:

	<pre class="prettyprint">
	npm install -g yo grunt-cli generator-aspnet bower
	</pre>

3. Immettere quanto segue nella finestra di comando per creare la cartella di progetto ed eseguire lo scaffolding dell'app:

	<pre class="prettyprint">
	yo aspnet
	</pre>

4. Seguire le istruzioni fornite dal generatore scorrendo e selezionando il tipo **Web API Application**.

	![Yeoman - Generatore di ASP.NET 5](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Assegnare all'app per le API ASP.NET il nome **ContactsList**. Questo nome verrà usato nel codice fornito in seguito nell'esercitazione. <br> Yeoman crea una nuova cartella denominata **ContactsList** e i file necessari per la nuova app.
6. Aprire **Visual Studio Code**.<br> 
	È possibile aprire VSCode dalla finestra di comando immettendo **code**.
7. Scegliere **Open Folder** dal menu **File** e selezionare la cartella in cui si trova l'app per le API ASP.NET.

	![Finestra di dialogo Select Folder](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode caricherà il progetto e lo visualizzerà nella finestra**Explore**.

	![VSCode che visualizza il progetto Contact](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. In **VSCode** scegliere **Command Palette** dal menu **View**.
9. In **Command Palette** immettere i comandi seguenti:

	<pre class="prettyprint">
	dnx:dnu restore - (ContactsList)
	</pre>
	Quando si inizia a digitare, nell'elenco verrà visualizzata l'intera riga di comando.

	![Comando Restore](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Il comando Restore installa i pacchetti NuGet necessari per eseguire l'applicazione. Al termine, la finestra di comando visualizzerà **Restore complete**.

## Modificare l'app per le API

A questo punto si modificherà l'app **ContactsList** aggiungendo una classe **Contact** e una classe **ContactsController**.

1. Posizionare il cursore sul nome del progetto **ContactsList** e aggiungere una nuova cartella denominata *Models* usando l'icona di una nuova cartella.

	![Nuova cartella Models](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Fare clic con il pulsante destro del mouse sulla cartella **Models** per aggiungere un nuovo file di classe denominato *Contact.cs* con il codice seguente:

	<pre class="prettyprint">
	namespace ContactsList.Models
	{
	    public class Contact
	    {
	        public int Id { get; set; }
	        public string Name { get; set; }
	        public string EmailAddress { get; set; }
	    }
	}
	</pre>

3. Fare clic con il pulsante destro del mouse sulla cartella **Controllers** e aggiungere un file *ContactsController.cs* in modo che venga visualizzato come segue:

	<pre class="prettyprint">
	using System.Collections.Generic;
	using Microsoft.AspNet.Mvc;
	using ContactsList.Models;
	
	namespace ContactsList.Controllers
	{
	    [Route("api/[controller]")]
	    public class ContactsController : Controller
	    {
	        // GET: api/Contacts
	        [HttpGet]
	        public IEnumerable&lt;Contact&gt; Get()
	        {
	            return new Contact[]{
	                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
	                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}
	</pre>

4. Assicurarsi che tutti i file vengano salvati selezionando **File** > **Save All**.
5. In **Command Palette** immettere quanto segue per eseguire l'app in locale:

	<pre class="prettyprint">
	dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
	</pre>

	Nella finestra di comando verrà visualizzato *Started*. Se nella finestra di comando non compare*Started*, verificare che nell'angolo inferiore sinistro della finestra di VSCode non siano presenti errori relativi al progetto.

5. Aprire un browser e passare all'URL seguente:

	**http://localhost:5001/api/Contacts**

	È possibile visualizzare il contenuto di *Contacts.json*. Quando si visualizza il file, il contenuto sarà il seguente:

	![Contenuto json restituito](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Modificare i metadati dell'app per le API
I metadati che permettono la distribuzione del progetto API ASP.NET come app per le API devono essere inclusi in un file *apiapp.json* nella radice del progetto.

1. In VSCode fare clic con il pulsante destro del mouse sulla cartella*wwwroot* e scegliere l'opzione **New File**.
2. Assegnare al nuovo file il nome *apiapp.json*.<br\> 
	Accertarsi che *apiapp.json* sia nella cartella *wwwroot*.
3. Aggiungere quanto riportato di seguito al file *apiapp.json*:

	<pre class="prettyprint">
	{
	    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
	    "id": "ContactsList",
	    "namespace": "microsoft.com",
	    "gateway": "2015-01-14",
	    "version": "1.0.0",
	    "title": "ContactsList",
	    "summary": "",
	    "author": "",
	    "endpoints": null
	}
	</pre>

Nel file *apiapp.json* è possibile specificare un endpoint per la definizione JSON dell'API Swagger dinamica, ma per questa esercitazione si userà un file di definizione dell'API statica. Per un esempio che usa la generazione Swagger dinamica, vedere [Configurare un progetto API Web come un'app per le API](app-service-dotnet-create-api-app-visual-studio.md).

## Aggiungere la definizione dell'API Swagger statica
Per fornire un file di definizione dell'API Swagger 2.0 statica, è necessario creare una cartella nella radice Web e inserirvi il file di definizione dell'API.

1. In VSCode creare una cartella denominata *metadata* nella cartella *wwwroot*.
2. Fare clic con il pulsante destro del mouse sulla nuova cartella *metadata* e aggiungere un nuovo file denominato *apiDefinition.swagger.json*. 
3. Aggiungere al nuovo file la sintassi json seguente:

	<pre class="prettyprint">
	{
	  "swagger": "2.0",
	  "info": {
	    "version": "v1",
	    "title": "ContactsList"
	  },
	  "host": "MUST REPLACE THIS WITH YOUR HOST URL",
	  "schemes": [
	    "https"
	  ],
	  "paths": {
	    "/api/Contacts": {
	      "get": {
	        "tags": [
	          "Contacts"
	        ],
	        "operationId": "Contacts_Get",
	        "consumes": [],
	        "produces": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml"
	        ],
	        "responses": {
	          "200": {
	            "description": "OK",
	            "schema": {
	              "type": "array",
	              "items": {
	                "$ref": "#/definitions/Contact"
	              }
	            }
	          }
	        },
	        "deprecated": false
	      },
	      "post": {
	        "tags": [
	          "Contacts"
	        ],
	        "operationId": "Contacts_Post",
	        "consumes": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml",
	          "application/x-www-form-urlencoded"
	        ],
	        "produces": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml"
	        ],
	        "parameters": [
	          {
	            "name": "contact",
	            "in": "body",
	            "required": true,
	            "schema": {
	              "$ref": "#/definitions/Contact"
	            }
	          }
	        ],
	        "responses": {
	          "200": {
	            "description": "OK",
	            "schema": {
	              "$ref": "#/definitions/Object"
	            }
	          }
	        },
	        "deprecated": false
	      }
	    }
	  },
	  "definitions": {
	    "Contact": {
	      "type": "object",
	      "properties": {
	        "Id": {
	          "format": "int32",
	          "type": "integer"
	        },
	        "Name": {
	          "type": "string"
	        },
	        "EmailAddress": {
	          "type": "string"
	        }
	      }
	    },
	    "Object": {
	      "type": "object",
	      "properties": {}
	    }
	  }
	}
	</pre>

Più avanti in questa esercitazione si sostituirà la stringa del segnaposto URL host precedente con l'URL host di Azure che verrà creato e copiato in un secondo momento.

## Creare un'app per le API nel portale di anteprima di Azure

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/) oppure [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/). È anche possibile provare gratuitamente gli [esempi di app del servizio app](http://tryappservice.azure.com).

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO** nella parte inferiore sinistra del portale.

3. Fare clic su **Web e dispositivi mobili > App per le API**.

	![Nuova app per le API in Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Immettere un valore in **Nome**, ad esempio ContactsList.

5. Selezionare un piano di Servizi app da usare o crearne uno nuovo. Se si crea un nuovo piano, selezionare i piano tariffario, la posizione e altre opzioni.

	![Pannello Nuova app per le API in Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Fare clic su **Crea**.

	![Pannello App per le API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Se quando è stata creata l'app si è lasciata selezionata la casella di controllo **Aggiungi alla Schermata iniziale**, è possibile individuare facilmente l'app facendo clic su **Home** o **Sfoglia**. Se la casella di controllo è stata deselezionata, fare clic su **Notifiche** a sinistra per visualizzare lo stato della creazione dell'app per le API, quindi fare clic sulla notifica per passare al pannello relativo alla nuova app per le API.

7. Fare clic su **Impostazioni > Impostazioni applicazione**.

8. Impostare il livello di accesso su **Pubblico (anonimo)**.

9. Fare clic su **Salva**.

	![Impostazioni app di Azure](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Abilitare la pubblicazione Git dalla nuova app per le API

Git è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Il codice scritto per l'app per le API verrà archiviato in un repository Git locale e verrà distribuito in Azure tramite il push in un repository remoto. Questo metodo di distribuzione è una funzionalità delle app Web del servizio app che è possibile usare in un'app per le API, in quanto queste app sono basate sulle app Web: un'app per le API nel servizio app di Azure non è altro che un'app Web con funzionalità aggiuntive per l'hosting di servizi Web.

Nel portale è possibile gestire le funzionalità specifiche delle app per le API nel pannello **App per le API** e le funzionalità condivise con le app Web nel pannello **Host app per le API**. Passare quindi al pannello **Host app per le API** per configurare la funzionalità di distribuzione Git.

1. Nel pannello App per le API fare clic su **Host app per le API**.

	![Host app per le API di Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Individuare la sezione **Distribuzione** del pannello **App per le API** e fare clic su **Configura distribuzione continua**. Potrebbe essere necessario scorrere verso per visualizzare questa parte del pannello.

	![Host app per le API di Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Fare clic su **Scegliere l'origine > Repository Git locale**.

5. Fare clic su **OK**.

	![Repository Git locale di Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Se le credenziali di distribuzione per la pubblicazione di un'app per le API o di un'altra app del servizio app non sono state configurate in precedenza, è possibile farlo ora:

	* Fare clic su **Imposta credenziali di distribuzione**.

	* Creare un nome utente e una password. La password sarà necessaria più avanti durante la configurazione di Git.

	* Fare clic su **Salva**.

	![Credenziali distribuzione di Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. Nel pannello **Host app per le API** fare clic su **Impostazioni > Proprietà**. L'URL del repository Git remoto in cui verrà effettuata la distribuzione è visualizzato in "URL GIT".

8. Copiare l'**URL GIT** che verrà usato più avanti nell'esercitazione.

	![URL Git di Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Nel pannello **APP PER LE API** copiare l'**URL** che verrà usato per aggiornare il valore "host" nel file *apiDefinition.swagger.json*.

	![URL di Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. In VSCode aprire il file *apiDefinition.swagger.json* e sostituire il valore host "MUST REPLACE THIS WITH YOUR HOST URL" con l'**URL** copiato nel passaggio precedente.
11. Rimuovere anche i caratteri "https://" dall'inizio del valore host.
12. Salvare le modifiche apportate al file *apiDefinition.swagger.json*.

## Pubblicare l'app per le API nel servizio app di Azure

In questa sezione verrà creato un repository Git locale e verrà eseguito il push da questo repository in Azure per distribuire l'applicazione di esempio nell'app per le API in esecuzione nel servizio app di Azure.

1. Nella barra di spostamento a sinistra di VSCode selezionare l'opzione Git.
2. Se Git non è già installato, installarlo scegliendo uno dei collegamenti forniti ([Chocolatey](https://chocolatey.org/packages/git) o [git-scm.com](http://git-scm.com/downloads)). Se non si ha familiarità con Git, scegliere **git-scm.com** e selezionare l'opzione per usare Git con GitBash dal prompt dei comandi di Windows. 
3. Dopo l'installazione di Git, riavviare VSCode e selezionare l'opzione Git nella barra a sinistra.
4. In VSCode Selezionare **Initialize git repository** per accertarsi che l'area di lavoro sia sotto il controllo del codice sorgente di Git. 

	![Initialize Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. Aggiungere un messaggio per il commit e selezionare il segno di spunta per **Commit All**.

	![Git Commit All](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Trovare e aprire **GitBash**. In alternativa, è possibile usare il prompt dei comandi di Windows.
7. In **GitBash** passare alla cartella del progetto VSCode. Ad esempio:

	<pre class="prettyprint">
	cd c:\VSCodeProjects\ContactsList
	</pre>

7. Creare un riferimento remoto per effettuare il push degli aggiornamenti all'app Web (host dell'app per le API) creata in precedenza, usando l'URL Git (che termina con ".git") copiato in uno dei passaggi precedenti:

	<pre class="prettyprint">
	git remote add azure [URL dell'archivio remoto]
	</pre>

8. Effettuare il push delle modifiche in Azure usando il comando seguente:

	<pre class="prettyprint">
	git push azure master
	</pre>

	Verrà richiesto di specificare la password creata in precedenza. **Nota: La password non sarà visibile.**

	L'output da questo comando termina con un messaggio che indica che la distribuzione è riuscita:

	<pre class="prettyprint">
	remote: Deployment successful.
	To https://user@testsite.scm.azurewebsites.net/testsite.git
	[new branch]      master -> master
	</pre>

> [AZURE.NOTE]Se si apportano modifiche all'app, è possibile ripubblicarla selezionando l'opzione **Commit All** in VSCode e immettendo il comando **git push azure master** in **GitBash**.

## Visualizzare la definizione dell'API nel portale di anteprima di Azure
Dopo aver distribuito un'API nell'app per le API, è possibile visualizzare la definizione dell'API nel portale di anteprima di Azure. Per iniziare, è necessario riavviare il gateway per consentire ad Azure di riconoscere che la definizione dell'API dell'app per le API è stata modificata. Il gateway è un'app Web che gestisce l'autorizzazione e l'amministrazione dell'API per le app per le API in un gruppo di risorse.

1. Nel portale di anteprima di Azure passare al pannello **APP PER LE API** relativo all'app per le API creata in precedenza e quindi fare clic sul collegamento **Gateway**.
2. Nel pannello **GATEWAY** fare clic su **Riavvia**. È ora possibile chiudere il pannello.
3. Nel pannello **APP PER LE API** fare clic su **Riavvia**. 
4. Nel pannello **APP PER LE API** fare clic su **Definizione API**.<br>
	 Nel pannello Definizione API sono visualizzati due metodi. Se i metodi GET e POST non sono visualizzati immediatamente, attendere alcuni secondi che Azure aggiorni l'app. Fare quindi clic su **Definizione API** nel pannello **APP PER LE API**.

## Eseguire l'app in Azure
Nel portale di anteprima di Azure passare al pannello **HOST APP PER LE API** relativo all'app per le API e fare clic su **Sfoglia**. Aggiungere quindi **api/Contacts** alla fine dell'URL per visualizzare i dettagli del contatto.


## Conclusioni
In questa esercitazione si è appreso come creare un'app per le API in Visual Studio Code. Per altre informazioni su Visual Studio Code, vedere [Visual Studio Code](https://code.visualstudio.com/Docs/). Per informazioni sulle app per le API, vedere [Informazioni sulle app per le API](app-service-api-apps-why-best-platform.md).
 

<!---HONumber=62-->