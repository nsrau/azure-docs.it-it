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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Eseguire il debug di un'app per le API del servizio app di Azure 

## Panoramica

In questa esercitazione verrà eseguito il debug di codice dell'API Web ASP.NET durante l'esecuzione in un'[app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](app-service-value-prop-what-is.md). È possibile eseguire l'esercitazione con l'app per le API [creata](app-service-dotnet-create-api-app.md) e [distribuita](app-service-dotnet-deploy-api-app.md) nelle esercitazioni precedenti di questa serie.

Usare prima di tutto la funzionalità **Client app per le API** di Visual Studio per generare codice client che chiama l'app per le API distribuita. Verrà quindi eseguito il debug dell'app client e dell'app per le API contemporaneamente, con l'app per le API eseguita dinamicamente nel cloud.

## Generare un client app per le API 

Gli strumenti App per le API in Visual Studio semplificano la generazione di codice C# che chiama le app per le API di Azure dal desktop, dall'archivio e dalle app per dispositivi mobili.

1. In Visual Studio aprire la soluzione che contiene l'app per le API usata nell'argomento [Creare un'app per le API](app-service-dotnet-create-api-app.md). 

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Aggiungi** > **Nuovo progetto**.

	![Aggiungere un nuovo progetto](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. Nella finestra di dialogo **Aggiungi nuovo progetto** eseguire la procedura seguente:

	1. Selezionare la categoria **Desktop Windows**.
	
	2. Selezionare il modello di progetto **Applicazione console**.
	
	3. Assegnare un nome al progetto.
	
	4. Fare clic su **OK** per generare il nuovo progetto nella soluzione esistente.
	
	![Aggiungere un nuovo progetto](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. Fare clic con il pulsante destro del mouse sul progetto Applicazione console appena creato e scegliere **Aggiungi** > **Client app per le API di Azure**.

	![Aggiungere un nuovo client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. Nella finestra di dialogo **Aggiungi client app per le API di Microsoft Azure** eseguire la procedura seguente:

	1. Selezionare l'opzione **Download**. 
	
	2. Dall'elenco a discesa selezionare l'app per le API creata in precedenza.
	
	3. Fare clic su **OK**.

	![Schermata di generazione](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	La procedura guidata scaricherà il file di metadati dell'API e genererà un'interfaccia tipizzata per richiamare l'app per le API.

	![Generazione in corso](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	Al termine della generazione di codice, in Esplora soluzioni verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa i modelli di client e dati.

	![Generazione completata](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. Aprire il file **Program.cs** dalla radice del progetto e sostituire il metodo **Main** con il codice seguente:

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Testare il client app per le API

Dopo la scrittura di codice per l'app per le API, è possibile testare il codice nel browser.

1. Aprire **Esplora soluzioni**.

2. Fare clic con il pulsante destro del mouse sull'applicazione console creata nella sezione precedente.

3. Dal menu contestuale dell'applicazione console selezionare **Debug > Avvia nuova istanza**.

4. Verrà visualizzata una finestra della console, in cui vengono visualizzati tutti i contatti.

	![App console in esecuzione](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. Premere **INVIO** per chiudere la finestra della console.

## Eseguire il debug dell'app per le API 

Dopo avere codificato e testato l'app per le API, è necessario eseguire il debug, come illustrato di seguito.

1. Scegliere **Esplora server** dal menu **Visualizza** di Visual Studio. 

2. In **Esplora server** espandere il nodo **Azure > Servizio app**.

3. Individuare il gruppo di risorse creato per la distribuzione dell'app per le API.

4. Nel gruppo di risorse fare clic con il pulsante destro del mouse sul nodo per l'app per le API e scegliere **Collega debugger**.

	![Collegamento del debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	Il debugger remoto tenterà di connettersi. In alcuni casi, potrebbe essere necessario riprovare scegliendo **Collega debugger** per stabilire una connessione. In caso di errore, ripetere quindi l'operazione.

	![Collegamento del debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. Dopo aver stabilito la connessione, aprire il file **ContactsController.cs** nel progetto app per le API e aggiungere punti di interruzione nei metodi `Get` e `Post`. Tali punti potrebbero inizialmente non essere visualizzati come attivi, ma se il debugger remoto è connesso si è pronti per eseguire il debug.

	![Applicazione di punti di interruzione al controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. Per eseguire il debug, fare clic con il pulsante destro del mouse sull'app console in **Esplora soluzioni** e scegliere **Debug** > **Avvia nuova istanza**. Ora è possibile eseguire il debug dell'app per le API in remoto e l'app client in locale e visualizzare l'intero flusso dei dati.

	La schermata seguente mostra il debugger quando raggiunge il punto di interruzione per il metodo `Post`. È possibile vedere che i dati di contatto del client sono stati deserializzati in un oggetto `Contact` fortemente tipizzato.

	![Debug del client locale per fare riferimento all'API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Passaggi successivi

Il debug remoto per le app per le API semplifica la visualizzazione del codice in esecuzione nel servizio app di Azure. Dati avanzati di debug e diagnostica sono direttamente disponibili nell'IDE di Visual Studio per le app per le API di Azure.

Le app per le API del servizio app sono app Web del servizio app con funzionalità aggiuntive per l'hosting di servizi Web. È quindi possibile usare per il debug e la risoluzione dei problemi delle app per le API gli stessi strumenti usati per le app Web. Per altre informazioni, vedere [Risolvere i problemi di un'app Web nel servizio app di Azure usando Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

L'app per le API creata in questa serie è disponibile pubblicamente per la chiamata da parte di qualsiasi utente. Per informazioni su come proteggere l'app per le API in modo che possa essere chiamata solo da utenti autenticati, vedere [Proteggere un'app per le API: aggiungere l'autenticazione di Azure Active Directory o del provider di servizi di social networking](app-service-api-dotnet-add-authentication.md).

<!--HONumber=54-->