<properties 
	pageTitle="Convertire un'API esistente in un'app per le API" 
	description="Informazioni su come configurare un progetto di Visual Studio API Web per la distribuzione come app per le API in un servizio app di Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Convertire un'API esistente in un'app per le API

## Informazioni generali

In questa esercitazione si creerà un progetto API Web ASP.NET in Visual Studio e quindi si aggiungeranno i pacchetti NuGet e i metadati del progetto che consentono di pubblicare e distribuire il progetto come app per le API nel servizio app di Azure. L'esercitazione spiega anche come personalizzare i metadati dell'app per le API.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creare un progetto API Web

1.  Aprire la finestra di dialogo **Nuovo progetto** in Visual Studio.

2.  Selezionare il nodo **Cloud** nel riquadro **Modelli installati** e quindi selezionare il modello **Applicazione Web ASP.NET**.

3.  Assegnare al progetto il nome *ContactsList*, quindi fare clic su **OK**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **Vuoto**, fare clic sulla casella di controllo **API Web**, deselezionare la casella di controllo **Host nel cloud** e quindi fare clic su **OK**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio crea i file di progetto per un progetto API Web vuoto.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Models** e quindi scegliere **Aggiungi > Classe** dal menu contestuale. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Assegnare al nuovo file il nome *Contact.cs* e quindi fare clic su **Aggiungi**.

5. Sostituire il contenuto del nuovo file con il codice seguente. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Fare clic con il pulsante destro del mouse sulla cartella **Controllers** e quindi scegliere **Aggiungi > Controller** dal menu contestuale. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. Nella finestra di dialogo **Aggiungi scaffolding** selezionare l'opzione **Controller API Web 2 - Vuoto** e quindi fare clic su **Aggiungi**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Assegnare al controller il nome **ContactsController** e quindi fare clic su **Aggiungi**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Sostituire il codice nel nuovo file del controller con il codice seguente. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


È stato creato un progetto API Web funzionante. Il modo più semplice per verificare che funzioni è chiamare il metodo Get da un browser.

6. Premere CTRL+F5 per eseguire il progetto.

	Il browser visualizza un errore HTTP 403 perché si apre senza un URL completo che punti al metodo Get.

7. Nel barra degli indirizzi del browser aggiungere "api/contacts/get/" all'URL e quindi premere INVIO.  L'URL finale sarà simile all'esempio seguente:

		http://localhost:25735/api/contacts/get/

	Ogni browser risponde in modo diverso a una chiamata API. Se si usa Internet Explorer, si vedrà un messaggio di download nella parte inferiore della finestra del browser:

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Configurare l'applicazione per la distribuzione come app per le API

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione) e quindi scegliere **Aggiungi > SDK app per le API di Azure**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. Nella finestra di dialogo **Scegli origine metadati app per le API** fare clic su **Generazione metadati automatica**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Questa scelta abilita l'interfaccia utente di Swagger dinamica, che verrà illustrata più avanti nell'esercitazione. In alternativa, è possibile specificare un file di definizione API di Swagger statico. A questo scopo, si selezionerà **Specifica un file JSON statico contenente metadati Swagger**. Visual Studio chiederà di caricare un file. Il file caricato viene salvato come *apiDefinition.Swagger.json* nella cartella *Metadata*.

3. Fare clic su **OK**.

	Visual Studio aggiunge un file *apiapp.json* e una cartella *Metadata*.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio aggiunge anche i pacchetti NuGet Swashbuckle. Nei passaggi seguenti si proverà l'interfaccia utente di definizione API dinamica fornita da Swagger. 

6. Premere CTRL+F5 per eseguire il progetto.

	Come prima, il browser visualizza un errore HTTP 403.

7. Nella barra degli indirizzi del browser aggiungere "swagger/" all'URL e quindi premere INVIO.  L'URL finale sarà simile all'esempio seguente:

		http://localhost:25735/swagger/

8. Nella pagina di Swagger fare clic su **Contacts** per visualizzare i metodi disponibili.
 
	Viene mostrato solo `Get` perché è l'unico metodo creato nel controller Contacts. La pagina mostra il codice JSON di risposta di esempio.

9. Fare clic sul metodo Get per visualizzare una risposta JSON di esempio e un pulsante **Try it out**.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Fare clic su **Try it out**.

	Viene restituita la risposta codificata in ContactsController.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Il progetto API Web ora è pronto per la distribuzione come app per le API nel servizio app di Azure. Le sezioni seguenti di questa esercitazione forniscono informazioni sui metadati che è possibile modificare per personalizzare l'app per le API. 

## Verificare apiapp.json

Le impostazioni contenute nel file *apiapp.json* determinano come l'app per le API viene identificata e presentata in Azure Marketplace. In questa versione di anteprima, Visual Studio non consente di pubblicare le app per le API nel Marketplace e quindi molte impostazioni non hanno effetto.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

I contenuti iniziali del file creato quando si sceglie la voce di menu **SDK app per le API di Azure** sono simili a quelli dell'esempio seguente:

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

La tabella seguente illustra il formato e l'utilizzo dei campi inclusi nel file e degli altri campi facoltativi che è possibile aggiungere. 

**Nota:** come indicato in precedenza, per questa versione di anteprima molti di questi campi non hanno effetto perché determinano come l'app per le API viene presentata in Azure Marketplace, ma Visual Studio non consente ancora di pubblicare le app per le API nel Marketplace.

| Nome (grassetto=obbligatorio) | Tipo | Commenti |
|:-----------|:------------|:------------|
|**id**           |stringa|ID di questo pacchetto. Deve essere univoco in uno spazio dei nomi e non deve contenere punti, / o caratteri @. Quando si distribuisce il progetto, Visual Studio verifica che l'ID sia univoco e consente di modificarlo. 
|**namespace**    |stringa|Spazio dei nomi che, con la proprietà **id**, identifica in modo univoco l'app per le API.  La proprietà è obbligatoria, ma il valore può essere una stringa vuota. <br/><br/>Questa proprietà consente di specificare un dominio, ad esempio contoso.com.  Se l'ID pacchetto che si vuole usare non è disponibile, è possibile aggiungere un dominio per poter usare tale ID pacchetto. Ad esempio, se ContactsList esiste già nella raccolta di app per le API senza uno spazio dei nomi, è possibile aggiungere un pacchetto ContactsList con lo spazio dei nomi contoso.com. <br/><br/>È possibile specificare un dominio anche per aggiungere un pacchetto alla raccolta di app per le API a cui possono accedere solo i membri dell'organizzazione. <br/><br/>Lo spazio dei nomi verrà usato come nome dell'editore nel Marketplace, dopo aver convertito i punti in trattini e i trattini in due trattini (--).<br/><br/>Lo spazio dei nomi è "microsoft.com" per le app per le API fornite da Microsoft.  
|**version**      |stringa|Formato [Semver](http://docs.nuget.org/Create/Versioning), ad esempio 1.0.1, 1.1.0-alpha.
|**gateway**      |stringa|Versione del gateway, espressa come data, ad esempio: 2015-01-14. Un *gateway* è una speciale app Web attraverso la quale vengono instradate tutte le richieste alle app per le API in un gruppo di risorse. Una delle sue funzioni principali è gestire l'autenticazione. Attualmente l'unica versione del gateway è 2015-01-14. In futuro, quando verranno rilasciate nuove versioni del gateway, questa proprietà consentirà di evitare modifiche a posteriori e di continuare a usare l'API del gateway precedente. 
|**title**        |stringa|Nome visualizzato dell'app per le API.
|**summary**      |stringa|Breve riepilogo dell'app per le API di non più di 100 caratteri.
|description      |stringa|Descrizione completa dell'app per le API. Può contenere codice HTML, non più di 1500 caratteri.
|**author**       |stringa|Autore/i dell'app per le API di non più di 256 caratteri.
|homepage         |URI|Home page dell'app per le API.
|endpoints        |oggetto[]|Matrice di un elemento che può contenere un endpoint di definizione API. 
|>>endpoints.apiDefinition|stringa|URI relativo dell'interfaccia utente di definizione API di Swagger dinamica (ad esempio, "/swagger/docs/v1") o di un file di definizione API di Swagger statico. Per l'API Web ASP.NET, l'interfaccia utente di Swagger generata dinamicamente è in genere la scelta migliore, ma, se non funziona con l'API in uso o se non si sta usando l'API Web ASP.NET, è possibile specificare un file di definizione statico. Per fornire un file di definizione statico, è possibile specificare qui l'URI relativo che punta al file o lasciare questa proprietà vuota e includere il file di definizione API statico nella cartella Metadata come [apiDefinition.swagger.json](#apidef). 
|>>endpoints.status|URI|Riservato per utilizzi futuri.
|categories       |stringa[]|Determina dove verrà mostrato il pacchetto in Azure Marketplace. I valori validi sono: social, enterprise, integration, protocol, app-datasvc, other. Valore predefinito: other.
|license          |oggetto|Licenza dell'app per le API.
|>>**license.type**|stringa|Identificatore licenza SPDX, ad esempio MIT.
|>>license.url    |URL|URL assoluto che punta al testo completo della licenza.
|>>license.requireAcceptance|Booleano|Indica se una licenza deve essere approvata prima dell'installazione. Valore predefinito: false.
|links            |oggetto[]|Matrice di collegamenti da aggiungere alla pagina del Marketplace.
|>>**links.text** |stringa|Testo del collegamento.
|>>**links.url**  |URL|URL del collegamento.
|authentication|oggetto[]|Matrice indicante il tipo di autenticazione necessaria all'app per le API per eseguire chiamate API in uscita.  Un connettore DropBox, ad esempio, deve eseguire l'autenticazione a DropBox e un connettore Salesforce deve eseguire l'autenticazione a Salesforce.
|>>authentication.type|stringa|I valori supportati sono i seguenti (senza distinzione tra maiuscole e minuscole): Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer. In base a questo valore, il portale è a conoscenza dei valori di configurazione, ad esempio l'ID client e il segreto client, obbligatori. 
|>>authentication.scopes|stringa[]|Matrice di ambiti specifici del tipo di autenticazione.
|copyright        |stringa|Informazioni sul copyright dell'app per le API.
|brandColor       |stringa|Colore facoltativo del marchio per migliorare l'esperienza con l'interfaccia utente, in qualsiasi formato compatibile con CSS, ad esempio #abc, red.
|tags             |stringa[]|Elenco di tag correlati al pacchetto.

<!--todo add when ready to document dependencies
|dependencies    |oggetto[]|Matrice di dipendenze del pacchetto.
|>>dependencies.id|stringa|ID del pacchetto di dipendenze.
|>>dependencies.domain|stringa|Dominio del pacchetto di dipendenze.
|>>dependencies.version|stringa|Versione del pacchetto di dipendenze.
-->

<!--todo add when ready to document status URI
URI del metodo Get di un servizio Web che restituisce un valore indicante lo stato corrente dell'app per le API. Se si fornisce questo URI, il portale mostrerà lo stato operativo corrente dell'app per le API con le altre informazioni sull'app per le API, ad esempio:  running, nearing quota, SSL certificate expiring e così via. Il formato della risposta JSON che si prevede verrà ricevuta dal portale è mostrato più avanti, dopo questa tabella. Se non si specifica un URI endpoints.status, il portale mostra lo stato della piattaforma Azure come stato dell'app per le API.
Ecco un esempio che mostra il formato previsto della risposta JSON dal metodo Get a cui punta `endpoints.status`:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
La proprietà `name` è una breve descrizione dello stato, `message` è una descrizione più lunga e `level` può essere "Error", "Warning" o "Info" per il normale stato.
-->

## Verificare la cartella Metadata

La cartella Metadata può contenere icone e schermate per la raccolta di app per le API, un file Swagger di documentazione dell'API e la configurazione dell'interfaccia utente per il portale di Azure. Tutte queste informazioni sono facoltative.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Nota:** come segnalato in precedenza per *apiapps.json*, i metadati relativi alla presentazione dell'app per le API in Azure Marketplace non hanno effetto per questa versione di anteprima, perché Visual Studio non consente di pubblicare le app per le API nel Marketplace.

### Cartella Metadata/icons

È possibile fornire le icone da visualizzare nella raccolta. 
Se non si forniscono icone personalizzate, verranno usate icone di app per le API generiche. I file delle icone devono essere in formato PNG e seguire queste convenzioni per i nomi e le dimensioni:

<!--todo: also used in the workflow designer--> 

|Nome file|Dimensioni
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Cartella Metadata/screenshots

È possibile fornire fino a 5 schermate da visualizzare nella raccolta. I file di immagine devono essere in formato PNG, 533x324.

### <a id="apidef"></a>File Metadata/apiDefinition.swagger.json

È possibile usare un file di formato [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) per descrivere la definizione API dell'app per le API. Ciò consente di esporre le definizioni API in modo statico nel pacchetto. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: find out if there is something to replace this -- could be deploymentTemplates subfolder.
### File resourceTemplate.delta.json
È possibile specificare un modello di Gestione risorse di Azure eseguito durante la distribuzione dell'app per le API. Le risorse specificate in questo file delta vengono aggiunte alle risorse create per impostazione predefinita per un'app per le API. Ad esempio, se l'app per le API richiede un'istanza di database SQL, è possibile usare questo file per eseguire il provisioning automatico del database  e impostare la stringa di connessione nelle impostazioni di configurazione quando l'app per le API viene distribuita.
-->  

### File Metadata/UIDefinition.json

È possibile fornire informazioni sull'interfaccia utente, ad esempio hint e una convalida nel [formato di Azure
Marketplace](https://auxdocs.azurewebsites.net/it-it/documentation/articles/gallery-items).

### Cartella Metadata/deploymentTemplates

Visual Studio crea questa cartella quando si sceglie la voce di menu **SDK app per le API di Azure**, ma per questa versione di anteprima non è usata.

## Passaggi successivi

Il progetto API Web ora è pronto per essere distribuito come app per le API. A questo scopo, è possibile seguire l'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

Per altre informazioni, vedere [Cosa sono le app per le API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->