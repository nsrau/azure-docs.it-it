<properties 
	pageTitle="Compilare e distribuire un'app per le API Node.js API nel servizio app di Azure" description="Informazioni su come creare un pacchetto dell'app per le API e distribuirlo nel servizio app di Azure." 
	services="app-service-api" 
	documentationCenter="nodejs" 
	authors="pkefal" 
	manager="", 
	editor=""/\>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Compilare e distribuire un'app per le API Node.js in Servizio app di Azure

Questa esercitazione illustra come creare un'applicazione [Node.js](http://nodejs.org) e come distribuirla nelle app per le API del servizio app di Azure tramite [Git](http://git-scm.com). Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Node.

Di seguito è riportata una schermata dell'applicazione completata:

![][sample-api-app-page]

## Creare un'app per le API nel portale di anteprima di Azure

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/). È anche possibile provare gratuitamente gli [esempi di app del servizio app](http://tryappservice.azure.com).

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO** nella parte inferiore sinistra del portale.

3. Fare clic su **Web e dispositivi mobili \> App per le API**.

	![][portal-quick-create]

4. Immettere un valore in **Nome**, ad esempio NodejsAPIApp.

5. Selezionare un piano di Servizi app da usare o crearne uno nuovo. Se si crea un nuovo piano, selezionare i piano tariffario, la posizione e altre opzioni.

	![][portal-create-api]

6. Fare clic su **Create**.

	![][api-app-blade]

	Se la casella di controllo **Aggiungi a Schermata iniziale** è stata lasciata selezionata, il portale apre automaticamente il pannello relativo all'app per le API non appena questa viene creata. Se la casella di controllo è stata deselezionata, nella home page del portale fare clic su **Notifiche** per visualizzare lo stato della creazione dell'app per le API, quindi fare clic sulla notifica per passare al pannello relativo alla nuova app per le API.

7. Fare clic su **Impostazioni \> Impostazioni applicazione**.

9. Impostare il livello di accesso su **Pubblico \(anonimo\)**.

11. Fare clic su **Salva**.

	![][set-api-app-access-level]

## Abilitare la pubblicazione Git per la nuova app per le API

[Git](http://git-scm.com/%20target="_blank) è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Il codice scritto per l'app per le API verrà archiviato in un repository Git locale e verrà distribuito in Azure tramite il push in un repository remoto. Questo metodo di distribuzione è una funzionalità delle app Web del servizio app che è possibile usare in un'app per le API, in quanto queste app sono basate sulle app Web: un'app per le API nel servizio app di Azure non è altro che un'app Web con funzionalità aggiuntive per l'hosting di servizi Web.

Nel portale è possibile gestire le funzionalità specifiche delle app per le API nel pannello **App per le API** e le funzionalità condivise con le app Web nel pannello **Host app per le API**. Passare quindi al pannello **Host app per le API** per configurare la funzionalità di distribuzione Git.

1. Nel pannello App per le API fare clic su **Host app per le API**.

	![][api-app-host]

2. Individuare la sezione **Distribuzione** del pannello **App per le API** e fare clic su **Configura distribuzione continua**. Potrebbe essere necessario scorrere verso per visualizzare questa parte del pannello.

	![][deployment-part]

3. Fare clic su **Scegliere l'origine \> Repository Git locale**.

5. Fare clic su **OK**.

	![][setup-git-publishing]

6. Se le credenziali di distribuzione per la pubblicazione di un'app per le API o di un'altra app del servizio app non sono state configurate in precedenza, è possibile farlo ora:

	* Fare clic su **Imposta credenziali di distribuzione**.

	* Creare un nome utente e una password.

	* Fare clic su **Salva**.

	![][deployment-credentials]

1. Nel pannello **Host app per le API** fare clic su **Impostazioni \> Proprietà**. L'URL del repository Git remoto in cui verrà effettuata la distribuzione è visualizzato in "URL GIT".

2. Copiare l'URL. Questa informazione sarà necessaria in uno dei passaggi successivi dell'esercitazione.

	![][git-url]

## Scaricare ed esaminare il codice per un'app per le API Node.js

In questa sezione verrà scaricato ed esaminato il codice fornito nell'esempio NoteAPIApp.

1. Scaricare il codice in [questo repository GitHub](http://go.microsoft.com/fwlink/?LinkID=534023&clcid=0x409). È possibile clonare il repository o fare clic su **Download Zip** per scaricarlo come file ZIP. Se si scarica il file ZIP, decomprimerlo nel disco locale.

2. Passare alla cartella in cui è stato decompresso l'esempio.

	![][api-app-folder-browse]

3. Aprire il file **apiapp.json** in un editor di testo ed esaminarne il contenuto.

	![][apiapp-json]

	Il servizio app di Azure prevede due prerequisiti per il riconoscimento di un'applicazione Node.js come un'app per le API:

	+ Deve essere presente un file denominato *apiapp.json* nella directory radice dell'applicazione.
	+ Un endpoint di metadati Swagger 2.0 deve essere esposto dall'applicazione. L'URL di questo endpoint è specificato nel file *apiapp.json*.

	Notare la proprietà **apiDefinition**. Il percorso di questo URL è relativo all'URL dell'API e punta all'endpoint Swagger 2.0. Il servizio app di Azure usa questa proprietà per individuare la definizione dell'API e abilitare numerose funzionalità dell'app per le API del servizio app.

	> [AZURE.NOTE]L'endpoint deve essere conforme alla specifica Swagger 2.0, in quanto le versioni precedenti, ad esempio la versione 1.2, non sono supportate dalla piattaforma. L'applicazione di esempio usa swaggerize-express per creare un endpoint con specifica Swagger 2.0.

4. Aprire il file **server.js** ed esaminare il codice.

	![][server-js]

	Il codice usa il modulo swaggerize-express per creare l'endpoint Swagger 2.0.

		app.use(swaggerize({
		    api: require('./api.json'),
		    docspath: '/swagger',
		    handlers: './handlers/'
		}));

	La proprietà `api` punta al file api.json che contiene la definizione della specifica Swagger 2.0 dell'API. È possibile creare manualmente il file in un editor di testo o usare l'[editor di Swagger](http://editor.swagger.io) online e scaricare il file JSON da quella posizione. Il file *api.json* specifica una proprietà `host` ma il valore della proprietà è determinato e sostituito in modo dinamico in fase di esecuzione.

	La proprietà `docspath` punta all'endpoint Swagger 2.0. Questo URL è relativo al percorso di base dell'API. Il percorso di base è dichiarato nel file api.json. In questo esempio il percorso di base è */api/dati*, quindi il percorso relativo all'endpoint Swagger è */api/data/swagger*.

	> [AZURE.NOTE]Poiché il percorso di base è dichiarato nel file *api.json*, se di tenta di accedere all'endpoint */swagger* come un percorso relativo all'URL dell'app per le API, verrà restituito un errore con codice 404. Si tratta di un errore comune quando si tenta di accedere all'endpoint Swagger.

	La proprietà `handlers` punta alla cartella locale che contiene i gestori di route per il modulo Express.js.

## Eseguire il codice dell'app per le API localmente

In questa sezione l'applicazione verrà eseguita localmente per verificare se funziona, prima di procedere alla distribuzione.

1. Passare alla cartella in cui è stato scaricato l'esempio.

2. Aprire un prompt della riga di comando e immettere il comando seguente:

		npm install

3. Al termine del comando *install*, immettere il comando seguente:

		node server.js

	L'output della finestra della riga di comando visualizza l'indicazione "Server started..."

5. Aprire il browser all'indirizzo http://localhost:1337/

	Verrà visualizzata la pagina seguente

	![][sample-api-app-page]

6. Per visualizzare il file Swagger.json, passare a http://localhost:1337/api/data/swagger.

## Pubblicare il codice dell'app per le API nel servizio app di Azure

In questa sezione verrà creato un repository Git locale e verrà eseguito il push da questo repository in Azure per distribuire l'applicazione di esempio nell'app per le API in esecuzione nel servizio app di Azure.

1. Se Git non è installato, installarlo dalla [pagina di download di Git](http://git-scm.com/download%20target="_blank").

1. Dalla riga di comando passare alla directory dell'applicazione di esempio e immettere i comandi seguenti per inizializzare un repository Git locale.

		git init


2. Usare i comandi seguenti per aggiungere file al repository:

		git add .
		git commit -m "Initial commit of the API App"

3. Creare un riferimento remoto per effettuare il push degli aggiornamenti all'app Web \(host app per le API\) creata in precedenza, usando l'URL Git copiato in uno dei passaggi precedenti:

		git remote add azure [URL for remote repository]

4. Effettuare il push delle modifiche in Azure usando il comando seguente:

		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

	L'output da questo comando termina con un messaggio che indica che la distribuzione è stata completata correttamente:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	\* [new branch] master -\> master

## Visualizzare la definizione dell'API nel portale di anteprima di Azure

Dopo aver distribuito un'API nell'app per le API, è possibile visualizzare la definizione dell'API nel portale di anteprima di Azure. Per iniziare, è necessario riavviare il *gateway* per consentire ad Azure di riconoscere che la definizione dell'API dell'app per le API è stata modificata. Il gateway è un'app Web che gestisce l'autorizzazione e l'amministrazione dell'API per le app per le API in un gruppo di risorse.

6. Nel portale di anteprima di Azure passare al pannello **App per le API** relativo all'app per le API creata in precedenza e quindi fare clic sul collegamento **Gateway**.

	![](./media/app-service-api-nodejs-api-app/clickgateway.png)

7. Nel pannello **Gateway** fare clic su **Riavvia**. È ora possibile chiudere il pannello.

	![](./media/app-service-api-nodejs-api-app/gatewayrestart.png)

8. Nel pannello **App per le API** fare clic su **Definizione API**.

	![](./media/app-service-api-nodejs-api-app/apidef.png)

	Nel pannello **Definizione API** sono visualizzati due metodi GET.

	![](./media/app-service-api-nodejs-api-app/apidefblade.png)

## Eseguire l'applicazione di esempio in Azure

Nel portale di anteprima di Azure passare al pannello **Host app per le API** relativo all'app per le API e fare clic su **Sfoglia**.

![][browse-api-app-page]

Verrà mostrata la home page visualizzata in precedenza quando l'app di esempio è stata eseguita localmente.

## Metadati dell'app per le API

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../includes/app-service-api-direct-deploy-metadata.md)]

## Passaggi successivi

È stata distribuita un'applicazione Web Node.js che usa un back-end dell'app per le API in Azure. Per altre informazioni su come usare Node.js in Azure, vedere il [Centro per sviluppatori Node.js](/develop/nodejs/).

* Per provare questa app per le API di esempio, usare il servizio di [prova del servizio app](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-nodejs-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-nodejs-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-nodejs-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-nodejs-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-nodejs-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-nodejs-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-nodejs-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-nodejs-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-nodejs-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-nodejs-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-nodejs-api-app/apiapp-json.png
[server-js]: ./media/app-service-api-nodejs-api-app/server-js.png
[sample-api-app-page]: ./media/app-service-api-nodejs-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-nodejs-api-app/browse-api-app-page.png

<!--HONumber=52-->
