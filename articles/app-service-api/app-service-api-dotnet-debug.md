<properties 
	pageTitle="Eseguire il debug di un'app per le API del servizio app di Azure" 
	description="Informazioni su come eseguire il debug di un'app per le API durante l'esecuzione nel servizio app di Azure usando Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="tdykstra"/>

# Eseguire il debug di un'app per le API del servizio app di Azure

## Panoramica

In questa esercitazione si esegue il debug di codice dell'API Web ASP.NET configurato per l'esecuzione in un'[app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Si esegue il debug del codice mentre è in esecuzione in locale e durante l'esecuzione in modalità remota in Azure.

È possibile eseguire l'esercitazione con l'app per le API [creata](app-service-dotnet-create-api-app.md) e [distribuita](app-service-dotnet-deploy-api-app.md) nelle esercitazioni precedenti di questa serie.

## Eseguire il debug di un'app API in modalità remota 

Per abilitare il debug remoto, è necessario distribuire una build di debug in Azure.

1. In **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse sul progetto distribuito nell'[esercitazione precedente](app-service-dotnet-deploy-api-app.md) e scegliere **Pubblica**.

2. Nella finestra di dialogo **Pubblica sito Web** selezionare la scheda **Impostazioni** e quindi selezionare **Debug** come configurazione della build.

4. Fare clic su **Pubblica**.

	![Pubblicazione del progetto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	Verrà aperta una finestra del browser con l'URL di base dell'app per le API.

4. Nella barra degli indirizzi del browser aggiungere /swagger alla fine dell'URL e premere INVIO.

	Questo passaggio presuppone che sia stata abilitata l'interfaccia utente di Swagger come indicato nell'esercitazione per la [creazione](app-service-dotnet-create-api-app.md) di un'app.

	![Interfaccia utente di Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Tornare a Visual Studio e fare clic su **Visualizza > Esplora server**.

6. In **Esplora server** espandere il nodo **Azure > Servizio app**.

7. Individuare il gruppo di risorse creato o selezionato per la distribuzione dell'app per le API.

8. Nel gruppo di risorse fare clic con il pulsante destro del mouse sul nodo per l'app per le API e scegliere **Collega debugger**.

	![Collegamento del debugger](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Il debugger remoto tenterà di connettersi. In alcuni casi, potrebbe essere necessario riprovare scegliendo **Collega debugger** per stabilire una connessione. In caso di errore, ripetere quindi l'operazione.

	![Collegamento del debugger](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Dopo aver stabilito la connessione, aprire il file **ContactsController.cs** nel progetto di app per le API e aggiungere un punto di interruzione nel metodo `Get`.

	![Applicazione di punti di interruzione al controller](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Tornare alla sessione del browser, fare clic su **Get** per visualizzare lo schema dell'oggetto *Contatto*, quindi fare clic su **Prova**. Visual Studio arresta l'esecuzione del programma in corrispondenza del punto di interruzione e si può eseguire il debug della logica del controller.

	![Provare il servizio](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Eseguire il debug di un'app per le API in locale 

A volte può essere opportuno eseguire il debug dell'app per le API localmente, ad esempio se i round trip al server Azure rallentano il debug. Questa sezione illustra come eseguire il debug dell'app per le API in locale usando l'interfaccia utente di Swagger come client di prova.

2. Nel browser passare al [portale di anteprima di Azure](https://portal.azure.com). 

3. Scegliere il pulsante **Sfoglia** sulla barra laterale e selezionare **App per le API**.

	![Opzioni di esplorazione nel portale di Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Selezionare l'app per le API creata dall'elenco di app per le API nella sottoscrizione.

	![Elenco di app per le API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. Nel pannello App per le API fare clic su **Host app per le API**.

	![Host di app per le API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. Nel pannello Host app per le API fare clic su **Tutte le impostazioni**.

	![Host di app per le API - Tutte le impostazioni](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Nel pannello **Impostazioni** fare clic su **Impostazioni applicazione**.

	![Host di app per le API - Impostazioni dell'applicazione](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. Nel pannello **Impostazioni app Web** scorrere verso il basso fino alla sezione **Impostazioni app**.

	![Host di app per le API - Impostazioni dell'applicazione per il debug locale](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. In Visual Studio aprire il file *web.config* del progetto dell'app per le API.

9. Da **Impostazioni app** aggiungere le chiavi e i valori seguenti alla sezione *appSettings* del file **web.config**.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Al termine, la sezione **appSettings** del file *web.config* dovrebbe avere un aspetto simile alla schermata seguente.

	![Host di app per le API - Impostazioni dell'applicazione per il debug locale](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Nota:** i valori *EMA\_* aggiunti al file *web.config* in questa sezione contengono informazioni di autorizzazione riservate. È quindi consigliabile evitare di salvare questi valori in un repository di controllo del codice sorgente pubblico. Per altre informazioni, vedere l'articolo [Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e nel servizio app di Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Inserire un punto di interruzione nel metodo `Get` del codice del controller dell'app per le API.

11. Premere F5 per avviare una sessione di debug di Visual Studio.
 
13.  Se il livello di accesso dell'app per le API è impostato su **Pubblico (anonimo)**, è possibile usare la pagina dell'interfaccia utente di Swagger per il test.

	* Quando il browser carica la pagina, viene visualizzato errore HTTP 403. Aggiungere */swagger* alla fine dell'URL nella barra degli indirizzi del browser e premere INVIO.

	* Quando sarà stata caricata l'interfaccia utente di Swagger, fare clic su **Get** per visualizzare lo schema dell'oggetto Contatto, quindi fare clic su **Prova**.

		Visual Studio arresta l'esecuzione del programma nel punto di interruzione e si può eseguire il debug della logica del controller.

14.	Se il livello di accesso dell'app per le API è impostato su **Pubblico (autenticato)**, sarà necessario autenticare e usare uno strumento di esplorazione seguendo le procedure illustrate in [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) per una richiesta Post, ovvero:

	* Passare all'URL di accesso al gateway e immettere le credenziali di accesso.
	* Ottenere il valore del token Zumo dal cookie x-zumo-auth.
	* Aggiungere un'intestazione x-zumo-auth alla richiesta e impostarne il valore sul valore del cookie x-zumo-auth.
	* Inviare la richiesta.

	**Nota:** durante l'esecuzione in locale, Azure non può controllare l'accesso all'app per le API per verificare che solo gli utenti autenticati possano eseguirne i metodi. Durante l'esecuzione in Azure, tutto il traffico destinato all'app per le API viene instradato attraverso il gateway e il gateway non passa le richieste non autenticate. Durante l'esecuzione locale il reindirizzamento non ha luogo e quindi alle richieste non autenticate non viene impedito l'accesso all'app per le API. L'importanza dell'autenticazione descritta sopra consiste nel fatto che è possibile eseguire il codice correlato all'autenticazione nell'app per le API, ad esempio il codice che recupera le informazioni sull'utente connesso. Per altre informazioni su come il gateway gestisce l'autenticazione per le app per le API, vedere [Autenticazione per le app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Passaggi successivi

In questa esercitazione si è visto come eseguire il debug di app per le API.

Per altre informazioni sulla risoluzione dei problemi, vedere [Risolvere i problemi di un'app Web nel servizio app di Azure tramite Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Le app per le API sono app Web con funzionalità aggiuntive per l'hosting di servizi Web. È quindi possibile usare gli stessi strumenti per il debug e la risoluzione dei problemi delle app per le API usati per le app Web.

<!---HONumber=Oct15_HO3-->