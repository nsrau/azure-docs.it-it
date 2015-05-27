<properties 
	pageTitle="App per le API BizTalk Integration nel servizio app di Microsoft Azure | Azure" 
	description="Informazioni su come creare e configurare le app per le API BizTalk Integration; architettura dei microservizi" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# App per le API BizTalk Integration nel servizio app di Microsoft Azure
Il servizio app di Microsoft Azure (o servizio app) include numerose app per le API BizTalk, estremamente importanti per gli ambienti di integrazione. Queste app per le API sono basate sui concetti e gli strumenti usati all'interno di BizTalk Server, ma sono ora disponibili come parte del servizio app di Azure.

Una categoria di queste app per le API sono le app per le API BizTalk Integration, che consentono di aggiungere facilmente regole aziendali, trasformare e convalidare messaggi XML, codificare file flat e dati JSON e molto altro, proprio come si farebbe con un BizTalk Server locale.

Queste app per le API di integrazione offrono funzionalità "Azione". Un'azione è il risultato, ad esempio, dopo la ricezione di un messaggio XML, della convalida del messaggio XML in base a uno schema XML.


## Cosa sono le app per le API Integration
Le app per le API BizTalk Integration sono app per le API esistenti e predefinite che possono elaborare i dati e produrre un output. Ad esempio, alcune di queste app per le API consentono l'interazione tra diversi formati di file e alcuni applicano la logica di business o la logica dell'applicazione. Le app per le API Integration includono:

App per le API | Descrizione
--- | ---
<ul><li>App per le API X12</li><li>AS2 Connector</li><li>App per le API EDIFACT</li><li>App per le API per la gestione dei partner commerciali</li> | Queste app per le API offrono funzioni Business to Business. [Connettori Business to Business](app-service-logic-b2b-connectors.md) contiene altre informazioni sulle app per le API.
BizTalk Flat File Encoder | Un'app per le API **Azione**. Rende interoperabili i dati dei file flat (come Excel, csv) e XML. Può convertire un'istanza di file flat in XML e viceversa.
BizTalk JSON Encoder | Un'app per le API **Azione**. Rende interoperabili i dati JSON ed XML. Può convertire un'istanza JSON in XML e viceversa.
BizTalk Rules | Un'app per le API **Azione**. È possibile implementare e applicare la logica o le "regole" di business per controllare un'operazione aziendale. Le regole sono dinamiche e possono cambiare in qualsiasi momento. Le regole BizTalk consentono agli utenti di abilitare la logica di business senza scrivere alcun codice ed è possibile aggiornarle facilmente senza influire sulle applicazioni.
BizTalk Transform | Un'app per le API **Azione**. Converte i dati da un formato a un altro. È anche possibile usare diverse funzioni predefinite per gestire le stringhe, completare espressioni aritmetiche, formattare data e ora e così via. 
BizTalk XML Validator | Un'app per le API **Azione**. Convalida i dati XML rispetto a schemi XML predefiniti. È possibile usare schemi basati su istanze di file flat, istanze JSON o connettori esistenti. 
BizTalk XPath Extractor | Un'app per le API **Azione**. Cercare ed estrarre dati da contenuti XML basati su uno specifico XPath.
Attesa | Ritardare l'esecuzione per la durata immessa oppure fino a un orario specifico. Quando aggiunto a un'app per la logica, può essere usato per ritardare l'esecuzione dell'intera app.


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

Usando queste app per le API è possibile completare svariate attività di messaggistica o di dati. Ad esempio, se si usa l'app per le API BizTalk Rules, è possibile ricevere un ordine e applicare uno sconto quando viene ordinato uno specifico quantitativo. In alternativa, è possibile addebitare una specifica imposta a seconda del codice di avviamento postale.

È possibile creare il numero desiderato di app per le API in maniera molto facile. È anche possibile riutilizzare le app per le API nell'ambito di più scenari o flussi di lavoro.

Ad esempio, si supponga di avere una politica aziendale che applichi uno sconto del 10% quando un cliente ordina 100 articoli del proprio prodotto. Nell'app, è possibile aggiungere l'app per le API BizTalk Rules che verifica la quantità ordinata e, se supera i 100 articoli, applica lo sconto del 10%.

È anche possibile espandere tale politica aziendale: si supponga che il proprio obiettivo sia aumentare le vendite nella Carolina del Nord. Oltre allo sconto del 10% sugli ordini per 100 o più articoli, viene offerta anche la spedizione gratuita se l'ordine proviene dalla Carolina del Nord. È possibile aggiungere facilmente questa condizione della Carolina del Nord alla regola BizTalk esistente.

A tale scopo, usare queste app per API senza scrivere alcun codice. Di seguito sono riportati i requisiti iniziali.


## Creare le app per le API
Le app per le API Integration possono essere create nel portale di Azure oppure usando le API REST.


### Creare le app per le API usando le API REST
Vedere le [API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


### Creare le app per le API Integration nel portale di Azure
Nel portale di Azure è possibile creare app per le API BizTalk Integration durante la creazione di app per la logica, app Web o app per dispositivi mobili. In alternativa, è possibile crearne una usando il relativo pannello. Entrambe le modalità sono facili, perciò tutto dipende dalle proprie necessità o preferenze. Alcuni utenti preferiscono creare prima tutte le app per le API Integration con le loro specifiche proprietà, quindi creare le app per la logica, Web o mobili e infine aggiungere le app per le API Integration create.

La procedura seguente consente di creare le app per le API BizTalk Integration tramite il pannello App per le API:

1. Nella schermata iniziale del portale di Azure (la home page) selezionare **Marketplace**. **App per le API** elenca tutte le app per le API e i connettori esistenti. È anche possibile **cercare** una specifica app per le API BizTalk.
2. Selezionare l'app per le API. Nel nuovo pannello selezionare **Crea**. 
3. Immettere le proprietà: 

	Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per l'app per le API, ad esempio *RulesDiscountTaxCode* o *APIAppValidateXML*.
Piano di servizio app | Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.
Piano tariffario | Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure. 
Gruppo di risorse | Creare un nuovo gruppo di risorse o usare un gruppo esistente. Tutte le app per le API e i connettori per app per la logica, app Web e app per dispositivi mobili devono trovarsi nello stesso gruppo di risorse. L'articolo <br/><br/>[Uso dei gruppi di risorse](resource-group-overview.md) illustra questa proprietà. 
Sottoscrizione | Proprietà di sola lettura che elenca la sottoscrizione corrente.
Location | La località geografica che ospita il servizio di Azure. 
Aggiungi a schermata iniziale | Selezionare questa opzione per aggiungere le app per le API Integration alla schermata iniziale (home page).


## Configurare le app per le API BizTalk
Aprire l'app per le API BizTalk nel portale di gestione di Azure. Nella sezione **Componenti** è possibile aggiungere gli ulteriori componenti necessari per completare l'app per le API:

	API App | Tasks
--- | ---
BizTalk Flat File Encoder | Immettere un file flat, come Excel o un file csv che si vogliono convertire in formato XML. In alternativa, immettere un file XML che si vuole convertire in un file flat.
BizTalk JSON Encoder | Immettere un file JSON che si vuole convertire in formato XML. In alternativa, immettere un file XML che si vuole convertire in formato JSON. 
BizTalk Rules | Aggiungere i propri vocabolari e creare le proprie regole IF THEN. Vedere [Usare BizTalk Rules](app-service-logic-use-biztalk-rules.md). 
BizTalk Transform | Aggiungere un'opzione Map, quindi immettere uno schema XML di input e uno schema XML di output. Usare le funzioni predefinite per gestire il messaggio o i dati in entrata affinché corrispondano allo schema XML di output. Vedere [Trasformare i documenti XML](app-service-logic-transform-xml-documents.md). 
BizTalk XML Validator | Immettere il file XML da convalidare rispetto a uno schema XML predefinito. È possibile usare schemi basati su istanze di file flat, istanze JSON o connettori esistenti. 
BizTalk XPath Extractor | Cercare ed estrarre dati da contenuti XML basati su uno specifico XPath.
Attesa | Immettere una durata o un orario specifico per l'esecuzione delle app Web, app per dispositivi mobili o app per la logica.


## Monitorare le app per le API
Aprire l'app per le API BizTalk nel portale di gestione di Azure. Nella sezione **Operazioni** è possibile visualizzare le varie operazioni di gestione. Ad esempio, è possibile:

- Visualizzare eventi informativi e di errore
- Visualizzare l'utilizzo della memoria e il conteggio dei thread del processo di lavoro (w3wp)
- Visualizzare i log dell'applicazione e del server Web\\

Per altre informazioni, vedere [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md).


## Aggiungere le app per le API BizTalk all'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare queste app per le API Integration. È possibile creare una nuova app per le API BizTalk o aggiungere quelle esistenti alle app per la logica, mobili o Web.

All'interno dell'app, selezionare semplicemente le app per le API BizTalk dalla raccolta per aggiungerle automaticamente all'app.

La procedura seguente consente di aggiungere le app per le API BizTalk ad app per la logica, mobili o Web:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare le proprie app per la logica, per dispositivi mobili o Web. 

	Se si sta creando una nuova app, cercare app per la logica, app per dispositivi mobili o app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.

3. In **Raccolta** selezionare l'app per le API BizTalk, che verrà aggiunta automaticamente all'app.

4. Selezionare **OK** per salvare le modifiche.


## Altre risorse delle app per le API Integration
[Creare un'app per la logica EAI con VETR](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [Trasformare i documenti XML](app-service-logic-transform-xml-documents.md)<br/> [Usare BizTalk Rules](app-service-logic-use-biztalk-rules.md)<br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)<br/> [Siti Web e app Web in Azure App Service](app-service-web-app-azure-portal.md)


## Altri connettori
[Connettori Enterprise](app-service-logic-enterprise-connectors.md)<br/> [Connettori Business-to-Business](app-service-logic-b2b-connectors.md)<br/> [Connettori per social network](app-service-logic-social-connectors.md)<br/> [Connettori per protocolli](app-service-logic-protocol-connectors.md)<br/> [Connettori per i servizi app e dati](app-service-logic-data-connectors.md)<br/> [Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)
<!--HONumber=54-->