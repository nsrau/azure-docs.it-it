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
	ms.date="07/08/2015" 
	ms.author="bradyg;tarcher"/>

# Eseguire il debug di un'app per le API del servizio app di Azure

## Panoramica

In questa esercitazione verrà illustrato come eseguire il debug di codice dell'API Web ASP.NET configurato per l'esecuzione in un'[app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Verrà eseguito il debug in modalità locale e remota (durante l'esecuzione in Azure). È possibile eseguire l'esercitazione con l'app per le API [creata](app-service-dotnet-create-api-app.md) e [distribuita](app-service-dotnet-deploy-api-app.md) nelle esercitazioni precedenti di questa serie.

## Eseguire il debug di un'app API in modalità remota 

I passaggi seguenti consentono di eseguire il debug dell'app API mentre è in esecuzione nel cloud usando l'interfaccia utente Swagger come client di prova.

1. In **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse sul progetto [distribuito nell'esercitazione precedente](app-service-dotnet-deploy-api-app.md), quindi scegliere **Pubblica**.

	![Pubblicazione del progetto](./media/app-service-api-dotnet-debug/rd-publish.png)

2. Nella finestra di dialogo **Pubblica sul Web**, selezionare la scheda Impostazioni e verificare che sia stata selezionata la configurazione di build **Debug**. Al termine, fare clic su **Pubblica** per inserire le modifiche nella sottoscrizione di Azure.

	![Pubblicazione del progetto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. Verrà aperta una finestra del browser con un messaggio che conferma la creazione corretta dell'app per le API.

4. Nella barra degli indirizzi del browser aggiungere /swagger alla fine dell'URL e premere &lt;Invio>. Viene visualizzato il client dell'interfaccia utente di Swagger.

	![Interfaccia utente di Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Tornare a Visual Studio e, nel menu **Visualizza**, scegliere **Esplora server**.

6. In **Esplora server** espandere il nodo **Azure > Servizio app**.

7. Individuare il gruppo di risorse creato per la distribuzione dell'app per le API.

8. Nel gruppo di risorse fare clic con il pulsante destro del mouse sul nodo per l'app per le API e scegliere **Collega debugger**.

	![Collegamento del debugger](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Il debugger remoto tenterà di connettersi. In alcuni casi, potrebbe essere necessario riprovare scegliendo **Collega debugger** per stabilire una connessione. In caso di errore, ripetere quindi l'operazione.

	![Collegamento del debugger](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Dopo aver stabilito la connessione, aprire il file **ContactsController.cs** nel progetto app per le API e aggiungere punti di interruzione nei metodi `Get` e `Post`. Tali punti potrebbero inizialmente non essere visualizzati come attivi, ma se il debugger remoto è connesso si è pronti per eseguire il debug.

	![Applicazione di punti di interruzione al controller](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Tornare alla sessione del browser, fare clic su **Get** per visualizzare lo schema dell'oggetto *Contatto*, quindi fare clic su **Prova**. Se si imposta un punto di interruzione nel metodo **Get** del controller, Visual Studio interrompe l'esecuzione del programma e sarà quindi possibile eseguire il debug della logica del controller.

	![Provare il servizio](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Eseguire il debug di un'app API in modalità locale 

A volte potrebbe essere opportuno eseguire il debug dell'app per le API in locale, ad esempio per evitare round trip potenzialmente lenti durante il ciclo di test/debug. I passaggi seguenti illustrano come eseguire il debug dell'app per le API in locale usando l'interfaccia utente Swagger come client di prova.

1. In Visual Studio aprire il file *web.config* del progetto dell'app per le API. 
 
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

9. Da **Impostazioni app**, cercare ciascuno dei valori seguenti e aggiungerli alla sezione **appSettings** del file *web.config*.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Al termine, la sezione **appSettings** del file *web.config* avrà un aspetto simile al seguente.

	![Host di app per le API - Impostazioni dell'applicazione per il debug locale](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Nota:** i valori *EMA\_* aggiunti al file *web.config* in questa sezione contengono informazioni di autorizzazione riservate. Di conseguenza, è consigliabile prestare attenzione durante il commit di questo file in un supporto di controllo sorgente pubblico (ad esempio *github*) perché questi segreti saranno visibili ad altri utenti. Per altre informazioni, vedere l'articolo [Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e Siti Web di Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Inserire uno o più punti di interruzione nel codice del controller dell'app per le API (nei metodi `Get` e `Post`).

	![Impostazione dei punti di interruzione](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. Premere F5 per avviare una sessione di debug di Visual Studio.
 
13.  Se il livello di accesso dell'app per le API è impostato su **Pubblico (anonimo)**, è possibile usare la pagina dell'interfaccia utente di Swagger per il test.

	* Quando il browser carica la pagina, si vede un messaggio di errore. Aggiungere */swagger* alla fine dell'URL nella barra degli indirizzi del browser e premere INVIO.

	* Quando sarà stata caricata l'interfaccia utente di Swagger, fare clic su **Get** per visualizzare lo schema dell'oggetto Contatto, quindi fare clic su **Prova**.

		Visual Studio interromperà l'esecuzione del programma nei punti di interruzione precedentemente impostati e sarà quindi possibile eseguire il debug della logica del controller.

		![Provare il servizio](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

14.	Se il livello di accesso dell'app per le API è impostato su **Pubblico (autenticato)**, sarà necessario autenticare e usare uno strumento di esplorazione seguendo le procedure illustrate in [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) per una richiesta Post, ovvero:

	* Passare all'URL di accesso al gateway e immettere le credenziali di accesso.
	* Ottenere il valore del token Zumo dal cookie x-zumo-auth.
	* Aggiungere un'intestazione x-zumo-auth alla richiesta e impostarne il valore sul valore del cookie x-zumo-auth.
	* Inviare la richiesta.

	**Nota:** durante l'esecuzione locale, Azure non può controllare l'accesso all'app per le API per verificare che solo gli utenti autenticati possano eseguirne i metodi. Durante l'esecuzione in Azure, tutto il traffico destinato all'app per le API viene instradato attraverso il gateway e il gateway non passa le richieste non autenticate. Durante l'esecuzione locale il reindirizzamento non ha luogo e quindi alle richieste non autenticate non viene impedito l'accesso all'app per le API. L'importanza dell'autenticazione descritta sopra consiste nel fatto che è possibile eseguire il codice correlato all'autenticazione nell'app per le API, ad esempio il codice che recupera le informazioni sull'utente connesso. Per altre informazioni su come il gateway gestisce l'autenticazione per le app per le API, vedere [Autenticazione per app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Passaggi successivi

Il debug remoto per le app per le API semplifica la visualizzazione del codice in esecuzione nel servizio app di Azure. Dati avanzati di debug e diagnostica sono direttamente disponibili nell'IDE di Visual Studio per le app per le API di Azure.

Le app per le API del servizio app sono app Web del servizio app con funzionalità aggiuntive per l'hosting di servizi Web. È quindi possibile usare per il debug e la risoluzione dei problemi delle app per le API gli stessi strumenti usati per le app Web. Per altre informazioni, vedere [Risolvere i problemi di un'app Web nel servizio app di Azure usando Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

L'app per le API creata in questa serie è disponibile pubblicamente per la chiamata da parte di qualsiasi utente. Per informazioni su come proteggere le app per le API in modo che solo gli utenti autenticati possano chiamarle, vedere [Autenticazione per app per le API e per dispositivi mobili nel servizio app di Azure](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=August15_HO6-->