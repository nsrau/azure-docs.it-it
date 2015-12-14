<properties 
	pageTitle="Distribuire e configurare un'app per le API del connettore SaaS" 
	description="Informazioni su come configurare un connettore SaaS installato nella sottoscrizione di Azure da Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# Distribuire e configurare un'app per le API del connettore SaaS nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Panoramica

Questa esercitazione illustra come installare, configurare e testare un [connettore SaaS (Software-as-a-Service)](../app-service-logic-what-are-bizTalk-api-apps.md) nel [servizio app di Azure](/documentation/services/app-service/) in modo da chiamarlo a livello di codice, ad esempio da un'app mobile. Un connettore SaaS è un'[app per le API](app-service-api-apps-why-best-platform.md) che semplifica l'integrazione con una piattaforma SaaS, ad esempio Office 365, Salesforce, Facebook e Dropbox. Se, invece di usare un connettore predefinito, si vuole creare un'app per le API .NET personalizzata, vedere [Connettersi a una piattaforma SaaS da un'app per le API ASP.NET](app-service-api-dotnet-connect-to-saas.md).

Ad esempio, il processo di autenticazione per l'uso diretto di Dropbox per codificare le richieste HTTP in modo che leggano e scrivano file nell'account Dropbox risulta complicato. Un connettore Dropbox gestisce automaticamente la complessità dell'autenticazione, permettendo agli sviluppatori di concentrarsi sulla scrittura di codice specifico per l'azienda.

> [AZURE.NOTE]Queste istruzioni non sono necessarie se si vuole usare un connettore SaaS da un'app per la logica. Per informazioni su come utilizzare i connettori SaaS all'interno di applicazioni per la logica, vedere [Creare una nuova app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) e [mediante l’applicazione OAUTH personalizzata in connettori](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurelogicapps&announcementId=4af1e4c5-d220-4457-97d8-d08e427ae6c1).
 
Questa esercitazione usa un connettore DropBox come esempio e illustra in modo dettagliato i passaggi seguenti:

* Installare il connettore Dropbox in un [gruppo di risorse](../resource-group-overview.md) nella sottoscrizione di Azure. 
* Configurare il connettore Dropbox in modo che possa connettersi al servizio Dropbox. Per completare questo passaggio sarà necessario un account Dropbox.
* Configurare il gruppo di risorse in modo che solo gli utenti autenticati possano accedere alle app per le API incluse nel gruppo di risorse.
* Eseguire i test per verificare il funzionamento corretto dell'autenticazione utente e dell'autenticazione Dropbox.

Per altre informazioni sull'autenticazione nel servizio app, vedere [Autenticazione per app per le API e app per dispositivi mobili](../app-service/app-service-authentication-overview.md).

## Installare il connettore Dropbox

1. Passare alla home page del [portale di anteprima di Azure] e fare clic su **Marketplace**.

	![Marketplace nel portale di anteprima di Azure](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. Cercare Dropbox e quindi fare clic sull'icona **Connettore Dropbox**.

	![Fare clic su Connettore Dropbox](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)
 
3. Fare clic su **Crea**.

	![Fare clic su Crea](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)
 
5. Nel pannello **Connettore Dropbox** in **Piano di servizio app** fare clic su **Crea nuovo** e quindi immettere DropBoxPlan nella casella **Crea nuovo piano di servizio app**.

	Per altre informazioni sui piani di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. In **Gruppo di risorse** fare clic su **Crea nuovo** e infine immettere DropboxRG nella casella **Crea nuovo gruppo di risorse**.

	Per altre informazioni sui gruppi di risorse, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](../resource-group-overview.md).

7. Selezionare il **Piano tariffario** Gratuito. Se non è visualizzato nell'elenco, fare clic su **Visualizza tutto**. Dopo avere selezionato **F1 Gratuito** fare clic sul pulsante **Seleziona**.

	È possibile usare un piano tariffario a pagamento, ma non è necessario per questa esercitazione.
 
11. Scegliere una **Località** vicina.

9. <a id="gateway"></a>Mantenere il valore predefinito "DropboxConnector" come **Nome** del connettore e quindi fare clic su **Crea**.

	![Fare clic su Crea](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

	Il servizio app di Azure crea un gruppo di risorse e quindi crea un'app per le API del connettore Dropbox e un'app Web *gateway* in tale gruppo. La funzione del gateway consiste nel gestire l'accesso a tutte le app per le API nel gruppo di risorse.

	È possibile verificare lo stato della creazione di risorse facendo clic su **Notifiche** nella home page del portale di anteprima di Azure.

3. Quando Azure completa la creazione del connettore, fare clic su **Sfoglia > Gruppi di risorse > DropboxRG**.
 
	Il pannello **Gruppo di risorse** per DropboxRG mostra il connettore e il gateway nel gruppo di risorse.

	![Diagramma Gruppo di risorse](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## Configurare l'account Dropbox e il connettore Dropbox

Per abilitare l'accesso API all'account Dropbox, sarà necessario creare un'app Dropbox nel sito per sviluppatori Dropbox. Copiare quindi i valori relativi a ID client e segreto client dall'app Dropbox al connettore Dropbox e impostare il connettore in modo che accetti solo richieste autenticate.

### <a id="createdbapp"></a>Creare un'app Dropbox

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### <a id="copysettings"></a>Copiare le impostazioni dell'app Dropbox nel connettore Dropbox per Azure e viceversa 

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

### Configurare il connettore Dropbox in modo da richiedere l'accesso autenticato

Per impostazione predefinita, il **Livello di accesso** del connettore viene impostato su **Interno**, ovvero può essere chiamato solo da altre app per le API e app Web nello stesso gruppo di risorse. Dropbox consente tuttavia solo agli utenti autenticati di accedere all'account Dropbox. Sarà quindi necessario modificare l'impostazione relativa al livello di accesso, in modo da richiedere l'autenticazione utente.

1. Tornare al pannello **Impostazioni** e fare clic su **Impostazioni applicazione**.

2. Nel pannello **Impostazioni applicazione** impostare **Livello di accesso** su **Pubblico (autenticato)** e quindi fare clic su **Salva**.
	
	![Impostare su Pubblico (autenticato)](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

Il connettore Dropbox è stato configurato in modo che le chiamate in uscita siano in grado di accedere all'account Dropbox e che le chiamate in arrivo provengano da utenti autenticati. Nella sezione successiva verrà specificato il provider di autenticazione da usare per autenticare gli utenti.

## Configurare il gateway

Come illustrato [in precedenza](#gateway), il gateway è un'app Web speciale che gestisce l'accesso a tutte le app per le API in un gruppo di risorse. Per configurare il gateway per l'autenticazione degli utenti, scegliere un provider di autenticazione quale Azure Active Directory, Google o Twitter. Gli utenti dovranno quindi eseguire l'autenticazione con il provider selezionato prima di potere chiamare correttamente il connettore Dropbox.

- Per eseguire questo passaggio, passare alla sezione [Configurare il gateway](app-service-api-dotnet-add-authentication.md#configure-the-gateway) dell'esercitazione [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md) e seguire le istruzioni per configurare il gateway nel gruppo di risorse DropboxRG.

## Eseguire test per verificare l'autenticazione utente e Dropbox

Dopo la configurazione del gateway nel gruppo di risorse DropboxRG per l'uso di un provider di autenticazione, è possibile testare il connettore Dropbox.

Nella maggior parte dei casi si userà un connettore chiamandolo dal codice e saranno presto disponibili esercitazioni che illustrano questa procedura. È tuttavia possibile che in alcuni casi si voglia verificare il funzionamento del connettore prima del collegamento di altre parti di un'app. Questa esercitazione illustra come usare un browser e un semplice strumento del client REST per verificare che sia possibile interagire con il servizio Dropbox tramite il connettore Dropbox appena installato e configurato.

Le istruzioni seguenti illustrano come eseguire questi passaggi usando gli strumenti per sviluppatori del browser Chrome e gli strumenti del client REST Postman. Si tratta semplicemente di un esempio ed è possibile eseguire le stesse procedure con altri browser e strumenti. È anche possibile usare il componente aggiuntivo "Advanced REST Client" di Chrome.

### Accedere come utente finale

Eseguire i passaggi seguenti in una nuova finestra del browser. In base al provider di autenticazione usato, è possibile che si debba usare una finestra privata o in incognito.

2. Passare all'URL di accesso per il gateway e il provider di autenticazione configurati. L'URL usa il modello seguente: 

    	http://[gatewayurl]/login/[providername]

	È possibile ottenere l'URL del gateway dal pannello **Gateway** nel [portale di anteprima di Azure]. Per visualizzare il pannello **Gateway** fare clic sul gateway nel diagramma mostrato nel pannello **Gruppo di risorse**.

	![URL del gateway](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

	Il valore [providername] è "facebook" per Facebook, "twitter" per Twitter, "aad" per Azure Active Directory e così via.

	Di seguito viene riportato un URL di accesso di esempio per Azure Active Directory:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. Immettere le credenziali quando il browser visualizza una pagina di accesso.
 
	Se si è configurato l'accesso di Azure Active Directory, accedere come uno degli utenti elencati nella scheda **Utenti** per l'applicazione creata nella scheda Azure Active Directory del [portale di Azure], ad esempio admin@contoso.onmicrosoft.com.

	Quando l'accesso ha esito positivo, viene visualizzata una pagina analoga ad "Accesso completato".

	![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### Fornire l'identità dell'utente a Dropbox

Per ottenere l'autorizzazione Dropbox per l'uso dell'API Dropbox, sarà necessario fornire le credenziali dell'utente a Dropbox. Azure avvierà automaticamente il processo, ma per attivare il processo è necessario passare a un URL del gateway specifico nel browser. Per ottenere l'URL è necessario inviare una richiesta HTTP Post al gateway.

La richiesta HTTP Post al gateway deve includere il token di autenticazione fornito da Azure durante l'accesso. Per le richieste del browser l'inclusione del token è automatica, poiché il token viene archiviato in un cookie, ma per una richiesta HTTP Post che usa lo strumento client REST è necessario ottenere il token dal cookie e inserirlo nell'intestazione della richiesta HTTP Post.

1. Nella finestra del browser in cui è visualizzato un messaggio analogo ad "Accesso completato" passare agli strumenti per sviluppatori del browser e individuare il cookie `x-zumo-auth`. Mantenere aperta questa finestra in modo da copiare il valore del cookie nel passaggio successivo.
 
	Per ottenere il valore cookie in Chrome, eseguire i passaggi seguenti:

	- Premere F12 per aprire gli strumenti per sviluppatori.
	- Passare alla scheda **Resources**.
	- Individuare i cookie per il sito del gateway e fare tre volte clic su **Value** per il cookie `x-zumo-auth`, in modo da selezionarli tutti. Assicurarsi di ottenere tutti i valori del cookie. Se si fa doppio clic, è possibile che si ottenga solo la parte iniziale.  

	![Copiare il token](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. In una nuova scheda o finestra del browser creare e inviare una richiesta HTTP Post al gateway per richiedere un URL di autorizzazione. Includere il token `x-zumo-auth` come intestazione HTTP.

	L'URL usa il modello seguente:

		[gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

	ad esempio:

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

	Per inviare la richiesta in Postman in Chrome, eseguire i passaggi seguenti:

	- Immettere l'**URL richiesta** riportato in precedenza.
	- Impostare il metodo su **Post**.
	- Aggiungere un'intestazione con nome `x-zumo-auth`.
	- Nel campo **Value** dell'intestazione incollare il valore copiato dal cookie `x-zumo-auth`.
	- Fare clic su **Send**.
	 
	La figura seguente mostra lo strumento Postman in Chrome:

	![Inviare l'URL di autorizzazione](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

	La risposta include un URL da usare per l'avvio del processo di accesso dell'utente con Dropbox. Se si ottiene una risposta di errore che indica che il metodo Get non è supportato, anche se l'elenco a discesa del metodo è impostato su **Post**, verificare che l'URL del gateway sia HTTPS e non HTTP.

	![URL di autorizzazione](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. Passare all'URL ricevuto in risposta alla richiesta HTTP Post.

	La risposta a questo URL reindirizza il browser al sito Dropbox, dove l'utente accede e concede all'app il consenso ad accedere all'account dell'utente.
	
	Al termine del processo di accesso e di consenso, Dropbox reindirizza il browser all'URL di reindirizzamento specificato, ad esempio il portale di anteprima di Azure se è stato seguito l'esempio ed è stato usato https://portal.azure.com). Se si chiamasse da un'app Web, questa sarebbe la pagina successiva visualizzata nell'app Web. L'app controllerebbe l'URL, perché, se ci fosse un errore nel processo di accesso o di consenso, l'URL di reindirizzamento potrebbe includere una variabile querystring `error`.

3. Mantenere aperta questa finestra del browser, poiché verrà usata nella sezione seguente.

### Chiamare l'API del connettore Dropbox 

Azure gestisce ora automaticamente tre token di autenticazione:

- Uno dal provider di autenticazione configurato per il gateway, ad esempio Azure Active Directory.
- Uno da Dropbox.
- Uno creato da Azure (token "zumo").

L'unico token da usare quando si effettuano richieste HTTP per l'interazione con Dropbox è il token zumo. Azure ha associato automaticamente tale token agli altri due e il connettore fornisce automaticamente gli altri due quando effettua chiamate a Dropbox.

Nei passaggi seguenti verrà effettuata una richiesta Get al connettore Dropbox per cercare file nell'account Dropbox. Poiché è possibile usare una finestra del browser per una richiesta Get e poiché la finestra del browser include già il token zumo in un cookie, sarà sufficiente passare a un URL e si riceveranno dati di Dropbox.

1. Nella finestra del browser in cui è aperto il portale di anteprima di Azure, tornare al pannello **App per le API** per il connettore Dropbox. 

2. Copiare l'URL dell'app per le API.
 
4. Fare clic sull'icona **Definizione API** per visualizzare i metodi API disponibili nel connettore.

	![Pannello App per le API](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

	![Definizione dell'API](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. Nella finestra del browser usata per l'autenticazione al gateway chiamare il metodo GET che recupera i nomi di file da una cartella. Di seguito è riportato il modello di URL:

		[connectorurl]/folder/[foldername]
   
3. Ad esempio, se l'URL del connettore è https://dropboxrg784237ad3e7.azurewebsites.net e si vogliono visualizzare i file nella cartella del blog, l'URL sarà:

		https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

	Browser diversi rispondono in modo diverso alle chiamate API. Se si usa Chrome, verrà visualizzata una risposta analoga all'esempio seguente:

	![Ottenere la risposta alla richiesta della cartella](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## Passaggi successivi

È stato illustrato come installare, configurare e testare un connettore SaaS. Per altre informazioni, vedere queste risorse:

* [Uso dei connettori](../app-service-logic/app-service-logic-connectors-list.md)
* [Autenticazione per app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md)  

[portale di anteprima di Azure]: https://portal.azure.com/
[portale di Azure]: https://manage.windowsazure.com/
 

<!---HONumber=AcomDC_1203_2015-->