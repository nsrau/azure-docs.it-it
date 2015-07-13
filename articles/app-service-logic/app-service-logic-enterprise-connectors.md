<properties 
	pageTitle="Connettori aziendali nel servizio app di Microsoft Azure | Azure" 
	description="Informazioni su come creare e configurare un connettore aziendale; architettura dei microservizi" 
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

# Connettori aziendali nel servizio app di Microsoft Azure
Il Servizio app di Microsoft Azure (o Servizio app in breve) include diversi connettori che è possibile usare con i sistemi aziendali, come SAP e Marketo. L'utilizzo di questi connettori consente di connettersi facilmente a un sistema aziendale e completare diverse attività.

I connettori aziendali offrono funzionalità "Trigger" o "Azione". Una funzionalità Trigger avvia una nuova istanza basata su un evento specifico, ad esempio l'aggiornamento di una voce in MongoDB. Una funzionalità Azione rappresenta il risultato, ad esempio dopo l'aggiornamento di una voce in MongoDB e il successivo ottenimento di una voce da MongoDB.


## Cos'è un connettore aziendale
I connettori aziendali sono "App per le API" esistenti che è possibile connettere a sistemi aziendali basati sul cloud o locali, inclusi:

Connettore | Descrizione
--- | ---
Marketo | Un connettore **Azione**. Connettersi a Marketo per eseguire diverse azioni, come creare/aggiornare clienti potenziali, ottenere modifiche dei clienti potenziali, pianificare una campagna, richiedere una campagna, ottenere clienti potenziali, ottenere informazioni su campagne/elenco, aggiungere clienti potenziali all'elenco e rimuoverli dall'elenco.
MongoDB | Un connettore **Trigger** e **Azione**. Connettersi al database server Mondo oppure al server Mongo locale. In una raccolta MongoDB, eseguire diverse azioni, come la creazione, l'aggiornamento, il recupero e l'eliminazione di voci. 
QuickBooks | Un connettore **Azione**. Connettersi a QuickBooks e usare le applicazioni per creare, aggiornare, leggere, eliminare e inviare query a diverse entità di Intuit QuickBooks, ad esempio clienti, articoli, fatture e così via.
Salesforce |Un connettore **Azione**. Connettersi a SalesForce per gestire diverse entità come account, clienti potenziali, opportunità, casi e così via nel proprio account SalesForce. È possibile eseguire diverse azioni come creare, aggiornare, eseguire upsert, query ed eliminare le diverse entità. 
SugarCRM | Un connettore **Azione**. Connettersi a SugarCRM online e usare le applicazioni per creare, aggiornare, leggere ed eliminare i diversi tipi di moduli, come gli account, i contatti, i prodotti e così via.
SAP | Un connettore **Azione**. Connettersi a un server SAP locale. Richiamare RFC, BAPI e TRFC e inviare anche IDOC.


L'utilizzo di questi connettori consente di completare diverse attività sul sistema aziendale. Ad esempio, usando il connettore QuickBooks è possibile eseguire attività tipiche in QuickBooks, ad esempio aggiungere i un nuovo cliente, aggiornare una fattura oppure eliminare un articolo.

È possibile creare il numero desiderato di connettori, in maniera molto facile. È anche possibile riutilizzare un singolo connettore nell'ambito di più scenari o flussi di lavoro.

Ad esempio, si supponga di prendere parte a una conferenza e incontrare un cliente che vorrebbe acquistare 10 pezzi del proprio prodotto. Sul proprio dispositivo mobile, è possibile aprire l'app mobile che usa il connettore QuickBooks appena creato, aggiungere le informazioni sul cliente e creare una fattura.

Mentre si presenzia alla conferenza, un cliente presso la sede della propria azienda vuole saldare una fattura esistente. Il dipendente apre un'applicazione che usa lo stesso connettore QuickBooks appena creato, cerca le informazioni sul cliente e aggiorna la fattura in modo che rifletta il nuovo saldo.

Vi sono flussi differenti che usano lo stesso connettore QuickBooks. È possibile creare e usare questi connettori senza scrivere alcun codice. Di seguito sono riportati i requisiti iniziali.


## Requisiti iniziali
Quando si crea un connettore aziendale sono necessarie alcune risorse. L'utente deve creare questi articoli prima che sia possibile usarli nel connettore. Tra i requisiti sono inclusi i seguenti:

Requisito | Descrizione
--- | ---
Spazio dei nomi del bus di servizio e i relativi valori chiave | Quando si usa SAP o MongoDB in locale, sono necessari uno spazio dei nomi del bus di servizio e i relativi valori chiave. Se non si esegue la connessione a un sistema locale, non è necessario uno spazio dei nomi del bus di servizio.<br/><br/>[Creare uno spazio dei nomi del bus di servizio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Gestione connessioni ibride locale | Quando ci si connette a un sistema locale, si installa Gestione connessioni ibride nel sistema locale. Quando si crea il connettore locale, il download è disponibile nelle proprietà o nelle impostazioni del connettore. 
Proprietà specifiche del sistema | Quando si usano sistemi aziendali, sono necessarie alcune proprietà specifiche del sistema (come nome utente e password). 


## Creare un connettore
Un connettore può essere creato usando il portale di Azure.

### Creare un connettore nel portale di Azure
Nel portale di Azure è possibile creare un connettore aziendale durante la creazione di app per la logica, app Web o app per dispositivi mobili. In alternativa, è possibile crearne una usando il relativo pannello. Entrambe le modalità sono facili, perciò tutto dipende dalle proprie necessità o preferenze. Alcuni utenti preferiscono creare prima tutti i connettori con le loro specifiche proprietà, quindi creare le app per la logica, Web o mobili e infine aggiungere il connettore creato.

I passaggi seguenti consentono di creare un connettore aziendale usando il pannello del connettore:

1. Nella schermata iniziale del portale di Azure (la home page) selezionare **Marketplace**. **App per le API** elenca tutti i connettori esistenti. È anche possibile eseguire la **Ricerca** per uno specifico connettore.
2. Selezionare il connettore. Nel nuovo pannello selezionare **Crea**. 
3. Immettere le proprietà: 

	Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per il connettore, Ad esempio, è possibile denominarlo *SAPConnector*, *SalesForceGetAccounts* o *QuickBooksGetItems*.
Impostazioni pacchetto | Immettere le impostazioni di sistema dell'organizzazione, ad esempio *nome utente SAP* o *URL del server di SugarCRM*. Vedere [Proprietà specifiche del sistema aziendale](#AddProperties) in questo argomento. 
Piano di servizio app | Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.
Piano tariffario | Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure. 
Gruppo di risorse | Creare un nuovo gruppo di risorse o usare un gruppo esistente. Tutte le app per le API e i connettori per app per la logica, app Web e app per dispositivi mobili devono trovarsi nello stesso gruppo di risorse. L'articolo <br/><br/>[Uso dei gruppi di risorse](../resource-group-overview.md) illustra questa proprietà. 
Sottoscrizione | Proprietà di sola lettura che elenca la sottoscrizione corrente.
Location | La località geografica che ospita il servizio di Azure. 
Aggiungi a schermata iniziale | Selezionare questa opzione per aggiungere il connettore alla schermata iniziale (home page).


	**<a name="AddProperties"></a>Proprietà specifiche del sistema aziendale**

> [AZURE.IMPORTANT]Ogni connettore ha proprietà specifiche di un particolare sistema aziendale. Quando ci si connette a SAP, si immettono proprietà specifiche di SAP. Quando ci si connette a SalesForce, si immettono proprietà specifiche di SalesForce e così via. La tabella seguente elenca le proprietà del sistema aziendale richieste.
	
	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>Endpoint</li><li>Nome provider</li></ul>
MongoDB| <ul><li>Stringa di connessione</li><li>Host</li><li>Porta</li><li>Nome utente</li><li>Password</li><li>Database</li><li>Usare la crittografia SSL</li><li>Locale: immettere False se è basato su cloud. Se il sistema MongoDb è locale, immettere True e immettere le seguenti proprietà:<ul><li>Nome chiave di accesso condivisa</li><li>Spazio dei nomi del bus di servizio</li><li>Percorso relè</li><li>Chiave di invio</li></ul></li></ul>
QuickBooks | <ul><li>ID società</li><li>Nome provider</li></ul>
SAP | <ul><li>Nome host</li><li>Lingua</li><li>Nome utente</li><li>Password</li><li>Numero sistema</li><li>Stringa di connessione bus di servizio</li><li>Nomi RFC</li><li>Nomi TRFC</li><li>Nomi BAPI</li><li>Nome IDOC</li></ul>
Salesforce | <ul><li>Nome provider</li><li>Istanza</li><li>Versione</li><li>Entità (valori separati da virgola)</li></ul>
SugarCRM | <ul><li>URL del server</li><li>Nome provider</li><li>Nomi modulo</li></ul>

4. Selezionare **Crea**.


## Installare la Gestione connessioni ibride locale
Dopo aver creato il connettore aziendale che si connette a un sistema locale, come SAP, installare la Gestione connessioni ibride:

1. Nel sistema aziendale locale, aprire il portale di gestione di Azure e selezionare il connettore aziendale. 
2. Nel pannello, selezionare **Connessione ibrida**. 
3. Copiare il valore in **Stringa di configurazione gateway primario**. 
4. Selezionare **Scarica e configura**. Durante l'installazione, incollare la stringa di configurazione gateway copiata e continuare con l'installazione. 
5. Per confermare la connettività, aprire il pannello del connettore aziendale. Lo stato deve indicare **Connesso**. 

[Integrazione con un server SAP locale fornisce un esempio](app-service-logic-integrate-with-an-on-premise-SAP-server.md).


## Monitorare le app per le API
Aprire l'app per le API aziendale nel portale di gestione di Azure. Nella sezione **Operazioni** è possibile visualizzare le varie operazioni di gestione. Ad esempio, è possibile:

- Visualizzare eventi informativi e di errore
- Visualizzare l'utilizzo della memoria e il conteggio dei thread del processo di lavoro (w3wp)
- Visualizzare i log dell'applicazione e del server Web\

Per altre informazioni, vedere [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md).


## Aggiungere il connettore all'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare questi connettori. È possibile creare un nuovo connettore o aggiungere quelli esistenti alle app per la logica, app per dispositivi mobili o app Web.

All'interno dell'app, selezionare semplicemente il connettore dalla raccolta per aggiungerlo automaticamente all'app.

La procedura seguente consente di aggiungere un connettore aziendale app per la logica, mobili o Web:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare le proprie app per la logica, per dispositivi mobili o Web.

	Se si sta creando una nuova app, cercare app per la logica, app per dispositivi mobili o app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.

3. In **Raccolta** aggiungere i connettori creati, che verranno aggiunti automaticamente all'app.
4. Selezionare **OK** per salvare le modifiche. 


## Sicurezza
Alcuni connettori aziendali usano la protezione OAuth. Quando si aggiunge il connettore all'app, si **autorizza** il connettore tramite la connessione al sistema aziendale con l'account di accesso e si accettano le condizioni. Così facendo, l'app e il connettore usano l'account di accesso per autenticarsi nel sistema.


### Accedere a un connettore con le API REST
[Vedere la documentazione sull'uso delle API REST del connettore.](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Altre risorse del connettore aziendale
[Integra con un server SAP locale](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/> [quali sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)<br/> [Siti Web e app Web in Azure App Service](../app-service-web/app-service-web-overview.md)



## Altri connettori
[Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md)<br/> [Connettori Business-to-Business](app-service-logic-b2b-connectors.md)<br/> [Connettori per social network](app-service-logic-social-connectors.md)<br/> [Connettori per protocolli](app-service-logic-protocol-connectors.md)<br/> [Connettori per i servizi app e dati](app-service-logic-data-connectors.md)<br/> [Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->