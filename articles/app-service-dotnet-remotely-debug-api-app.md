<properties 
	pageTitle="Eseguire il debug in remoto di un'app per le API del servizio app di Azure" 
	description="Uso di Visual Studio per eseguire il debug in remoto di un'app per le API del servizio app di Azure." 
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

# Eseguire il debug in remoto di un'app per le API del servizio app di Azure 

## Informazioni generali

Le funzionalità di debug remoto di Visual Studio sono state estese per supportare le app per le API nel servizio app di Azure. Ciò significa che è possibile usare strumenti di debug comuni per vedere il codice eseguito dinamicamente in Azure. Questo argomento descrive come usare **il client app per le API** di Visual Studio per generare il codice client che chiama l'app per le API distribuita. Verrà quindi eseguito il debug dell'app client e dell'app per le API contemporaneamente, con l'app per le API eseguita dinamicamente nel cloud.

Questa è l'ultima di una serie di tre esercitazioni:

1. Nell'esercitazione relativa alla [creazione di un'app per le API](app-service-dotnet-create-api-app.md) è stato creato un progetto di app per le API. 
* Nell'esercitazione sulla [distribuzione di un'app per le API](app-service-dotnet-deploy-api-app.md) l'app per le API è stata distribuita nella sottoscrizione di Azure.
* In questa esercitazione viene usato Visual Studio per eseguire in modalità remota il debug del codice mentre questo è in esecuzione in Azure.

## Generare un client app per le API 

Gli strumenti App per le API in Visual Studio semplificano la generazione di codice C# che chiama le app per le API di Azure dal desktop, dall'archivio e dalle app mobili. 

In Visual Studio aprire la soluzione che contiene l'app per le API della [prima](app-service-dotnet-create-api-app.md) esercitazione. Fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Aggiungi** > **Nuovo progetto**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Selezionare la categoria **Windows Desktop** e il modello di progetto **Applicazione console**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Fare clic con il pulsante destro del mouse sul progetto Applicazione console e selezionare **Aggiungi** > **Client app per le API di Azure**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
Nella finestra di dialogo selezionare l'opzione **Download**. Dall'elenco a discesa selezionare l'app per le API creata in precedenza. Fare clic su **OK**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

La procedura guidata scaricherà il file di metadati dell'API e genererà un'interfaccia tipizzata per richiamare l'app per le API.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

Al termine della generazione di codice, in Esplora soluzioni verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa i modelli di client e dati. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Aprire il file **Program.cs** dalla radice del progetto e sostituire il metodo **Main** con il codice seguente: 

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

Dal menu **Visualizza** selezionare **Esplora server**. Nella finestra Esplora server espandere il nodo * Azure > Servizio app**. Trovare il gruppo di risorse creato per la distribuzione dell'app per le API. Fare clic con il pulsante destro del mouse sul nodo per l'app per le API e scegliere **Collega debugger**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

Il debugger remoto tenterà di connettersi. In alcuni casi, potrebbe essere necessario riprovare scegliendo **Collega debugger** per stabilire una connessione; pertanto, in caso di errore, ripetere l'operazione.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

Dopo aver stabilito la connessione, aprire il file **ContactsController.cs** nel progetto di app per le API e aggiungere punti di interruzione nei metodi `Get` e `Post`. Tali punti potrebbero inizialmente non essere visualizzati come attivi, ma se il debugger remoto è connesso si è pronti per eseguire il debug. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Per eseguire il debug, fare clic con il pulsante destro del mouse sull'app console in Esplora soluzioni e scegliere **Debug** > **Avvia nuova istanza**. Ora è possibile eseguire il debug dell'app per le API in remoto e l'app client in locale e visualizzare l'intero flusso dei dati. 

La schermata seguente mostra il debugger quando raggiunge il punto di interruzione per il metodo `Post`. È possibile vedere che i dati di contatto del client sono stati deserializzati in un oggetto `Contact` fortemente tipizzato. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Riepilogo

Il debug remoto per le app per le API semplifica la visualizzazione del codice in esecuzione nel servizio app di Azure. Dati avanzati di debug e diagnostica sono direttamente disponibili nell'IDE di Visual Studio per le app per le API di Azure eseguite in remoto.


<!--HONumber=49-->