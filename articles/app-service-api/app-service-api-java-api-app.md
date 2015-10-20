<properties
	pageTitle="Compilare e distribuire un'app per le API Java nel servizio app di Azure"
	description="Informazioni su come creare un pacchetto dell'app per le API Java e distribuirlo nel servizio app di Azure."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="08/11/2015"
	ms.author="pakefali"/>

# Compilare e distribuire un'app per le API Java nel servizio app di Azure

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

Questa esercitazione illustra come creare un'applicazione Java e come distribuirla nelle app per le API del servizio app di Azure tramite [Git](http://git-scm.com). Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Java. Questa esercitazione usa anche [Gradle](https://gradle.org) per abilitare l'automazione della compilazione e la risoluzione delle dipendenze dei pacchetti per l'applicazione Java. Viene infine usato [RESTEasy](http://resteasy.jboss.org/) per creare il servizio RESTful, implementando completamente la specifica [JaxRS](https://jax-rs-spec.java.net/).

Di seguito è riportata una schermata dell'applicazione completata:

![][sample-api-app-page]

## Creare un'app per le API nel portale di Azure

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/) oppure [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/). È anche possibile provare gratuitamente gli [esempi di app del servizio app](http://tryappservice.azure.com).

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

2. Fare clic su **NUOVO** nella parte inferiore sinistra del portale.

3. Fare clic su **Web e dispositivi mobili > App per le API**.

	![][portal-quick-create]

4. Immettere un valore in **Nome**, ad esempio JavaAPIApp.

5. Selezionare un piano di Servizi app da usare o crearne uno nuovo. Se si crea un nuovo piano, selezionare i piano tariffario, la posizione e altre opzioni.

	![][portal-create-api]

6. Fare clic su **Crea**.

	![][api-app-blade]

	Se la casella di controllo **Aggiungi a Schermata iniziale** è stata lasciata selezionata, il portale apre automaticamente il pannello relativo all'app per le API non appena questa viene creata. Se la casella di controllo è stata deselezionata, nella home page del portale fare clic su **Notifiche** per visualizzare lo stato della creazione dell'app per le API, quindi fare clic sulla notifica per passare al pannello relativo alla nuova app per le API.

7. Fare clic su **Impostazioni > Impostazioni applicazione**.

9. Impostare il livello di accesso su **Pubblico (anonimo)**.

11. Fare clic su **Salva**.

	![][set-api-app-access-level]

## Abilitare la pubblicazione Git per la nuova app per le API

[Git](http://git-scm.com) è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Il codice scritto per l'app per le API verrà archiviato in un repository Git locale e verrà distribuito in Azure tramite il push in un repository remoto. Questo metodo di distribuzione è una funzionalità delle app Web del servizio app che è possibile usare in un'app per le API, in quanto queste app sono basate sulle app Web: un'app per le API nel servizio app di Azure non è altro che un'app Web con funzionalità aggiuntive per l'hosting di servizi Web.

Nel portale è possibile gestire le funzionalità specifiche delle app per le API nel pannello **App per le API** e le funzionalità condivise con le app Web nel pannello **Host app per le API**. Passare quindi al pannello **Host app per le API** per configurare la funzionalità di distribuzione Git.

1. Nel pannello App per le API fare clic su **Host app per le API**.

	![][api-app-host]

2. Individuare la sezione **Distribuzione** del pannello **App per le API** e fare clic su **Configura distribuzione continua**. Potrebbe essere necessario scorrere verso per visualizzare questa parte del pannello.

	![][deployment-part]

3. Fare clic su **Scegliere l'origine > Repository Git locale**.

5. Fare clic su **OK**.

	![][setup-git-publishing]

6. Se le credenziali di distribuzione per la pubblicazione di un'app per le API o di un'altra app del servizio app non sono state configurate in precedenza, è possibile farlo ora:

	* Fare clic su **Imposta credenziali di distribuzione**.

	* Creare un nome utente e una password.

	* Fare clic su **Salva**.

	![][deployment-credentials]

1. Nel pannello **Host app API** fare clic su **Impostazioni > Proprietà**. L'URL del repository Git remoto in cui verrà effettuata la distribuzione è visualizzato in "URL GIT".

2. Copiare l'URL. Questa informazione sarà necessaria in uno dei passaggi successivi dell'esercitazione.

	![][git-url]

## Abilitare il runtime Java nella nuova app per le API

Per permettere all'app per le API di ospitare correttamente un'app Java, è necessario abilitare il runtime Java e scegliere un server applicazioni. Il portale permette di eseguire facilmente questa operazione. Verranno abilitati Java 7 e Jetty per l'hosting dell'applicazione.

1. Nel pannello App per le API fare clic su **Host app per le API**.

	![][api-app-host]

2. Fare clic su **Impostazioni > Impostazioni applicazione**. Abilitare Java e selezionare Jetty come server applicazioni. Fare clic su **Save**.

	![][api-app-enable-java]

Ciò permetterà l'**abilitazione del runtime Java** nell'app per le API e la creazione di una cartella **webapps/** nella radice del sito. La cartella includerà tutti i file con estensione war delle applicazioni.

## Scaricare ed esaminare il codice per un'app per le API Java

In questa sezione verrà scaricato ed esaminato il codice fornito nell'esempio JavaAPIApp.

1. Scaricare il codice in [questo repository GitHub](http://go.microsoft.com/fwlink/?LinkId=571009). È possibile clonare il repository o fare clic su **Download Zip** per scaricarlo come file ZIP. Se si scarica il file ZIP, decomprimerlo nel disco locale.

2. Passare alla cartella in cui è stato decompresso l'esempio e quindi alla cartella `build\libs`.

	![][api-app-folder-browse]

3. Aprire il file **apiapp.json** in un editor di testo ed esaminarne il contenuto.

	![][apiapp-json]

	Il servizio app di Azure prevede due prerequisiti per il riconoscimento di un'applicazione Java come un'app per le API:

	+ Deve essere presente un file denominato *apiapp.json* nella directory radice dell'applicazione.
	+ Un endpoint di metadati Swagger 2.0 deve essere esposto dall'applicazione. L'URL di questo endpoint è specificato nel file *apiapp.json*.

	Notare la proprietà **apiDefinition**. Il percorso di questo URL è relativo all'URL dell'API e punta all'endpoint Swagger 2.0. Il servizio app di Azure usa questa proprietà per individuare la definizione dell'API e abilitare numerose funzionalità dell'app per le API del servizio app.

4. Passare a `src\main\java\com\microsoft\trysamples\javaapiapp`, aprire il file **App.java** ed esaminare il codice.

	![][app-java]

	Il codice usa il pacchetto Swagger per JaxRS per creare l'endpoint Swagger 2.0.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	Il metodo `setVersion` imposta la versione dell'API nei metadati gestiti da Swagger.

	Il metodo `setBasePath` imposta il percorso di base usato da Swagger per generare i metadati. Questo URL è relativo al percorso di base dell'app per le API.

	Il metodo `setHost` imposta l'host in cui è in attesa l'API. In questo caso viene usata la variabile `websitehostname`, assegnata alcune righe prima per l'impostazione dinamica su `localhost` in caso di esecuzione locale oppure il nome host dell'app per le API in caso di esecuzione dell'applicazione nel servizio app di Azure.

	Il metodo `setResourcePackage` imposta il pacchetto che verrà analizzato da Swagger e verrà incluso nel file Swagger.json, contenente i metadati dell'API.

	Il metodo `setSchemes` definisce gli schemi supportati.

	Il metodo `setScan` fa in modo che Swagger generi la documentazione per l'app.

	Nella [pagina Wiki](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig) di Swagger sono disponibili altri metodi che personalizzano l'output di Swagger quando si usa RESTEasy.

	> [AZURE.NOTE]È possibile accedere al file di metadati di Swagger in `/JavaAPIApp/api/swagger.json`.

## Eseguire il codice dell'app per le API localmente

In questa sezione l'applicazione verrà eseguita localmente per verificare se funziona, prima di procedere alla distribuzione.

1. Passare alla cartella in cui è stato scaricato l'esempio.

2. Aprire un prompt della riga di comando e immettere il comando seguente:

		gradlew.bat

3. Al termine dell'esecuzione del comando, immettere il comando seguente:

		gradlew.bat jettyRunWar

	L'output della finestra della riga di comando sarà analogo al seguente:

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. Aprire il browser all'indirizzo `http://localhost:8080/JavaAPIApp/`

	Verrà visualizzata la pagina seguente

	![][sample-api-app-page]

6. Per visualizzare il file Swagger.json, passare a `http://localhost:8080/JavaAPIApp/api/Swagger.json`.

## Pubblicare il codice dell'app per le API nel servizio app di Azure

In questa sezione verrà creato un repository Git locale e verrà eseguito il push da questo repository in Azure per distribuire l'applicazione di esempio nell'app per le API in esecuzione nel servizio app di Azure.

1. Se Git non è installato, installarlo dalla [pagina di download di Git](http://git-scm.com/download).

1. Dalla riga di comando passare alla directory dell'applicazione di esempio , quindi `build\libs` e immettere i comandi seguenti per inizializzare un repository Git locale.

		git init


2. Usare i comandi seguenti per aggiungere file al repository:

		git add .
		git commit -m "Initial commit of the API App"

3. Creare un riferimento remoto per effettuare il push degli aggiornamenti all'app Web (host app per le API) creata in precedenza, usando l'URL Git copiato in uno dei passaggi precedenti:

		git remote add azure [URL for remote repository]

4. Effettuare il push delle modifiche in Azure usando il comando seguente:

		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

	L'output da questo comando termina con un messaggio che indica che la distribuzione è stata completata correttamente:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Visualizzare la definizione dell'API nel portale di Azure

Dopo aver distribuito un'API nell'app per le API, è possibile visualizzare la definizione dell'API nel portale di Azure. Per iniziare, è necessario riavviare il *gateway* per consentire ad Azure di riconoscere che la definizione dell'API dell'app per le API è stata modificata. Il gateway è un'app Web che gestisce l'autorizzazione e l'amministrazione dell'API per le app per le API in un gruppo di risorse.

6. Nel portale di Azure passare al pannello **App per le API** relativo all'app per le API creata in precedenza e quindi fare clic sul collegamento **Gateway**.

	![][click-gateway]

7. Nel pannello **Gateway** fare clic su **Riavvia**. È ora possibile chiudere il pannello.

	![][restart-gateway]

8. Nel pannello **App per le API** fare clic su **Definizione API**.

	![][api-definition-click]

	Nel pannello **Definizione API** è visualizzato un metodo GET.

	![][api-definition-blade]

## Eseguire l'applicazione di esempio in Azure

Nel portale di Azure passare al pannello **Host app API** per l'app per le API e fare clic su **Sfoglia** .

![][browse-api-app-page]

Verrà mostrata la home page visualizzata in precedenza quando l'app di esempio è stata eseguita localmente.

## Passaggi successivi

È stata distribuita un'applicazione Web Java che usa un back-end dell'app per le API in Azure. Per altre informazioni su come usare Java in Azure, vedere il [Centro per sviluppatori Java](/develop/java/).

Per provare questa app per le API di esempio, usare il servizio di [prova del servizio app](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=Oct15_HO3-->