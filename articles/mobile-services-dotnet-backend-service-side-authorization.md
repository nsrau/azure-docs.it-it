<properties
	pageTitle="Autorizzazione sul lato servizio degli utenti in Servizi mobili con back-end .NET | Mobile Developer Center"
	description="Informazioni su come autorizzare gli utenti nel back-end .NET di Servizi mobili di Azure."
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Autorizzazione sul lato servizio degli utenti in Servizi mobili

> [AZURE.SELECTOR-LIST (Piattaforma | Back-end)]
- [(Qualsiasi | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(Qualsiasi | Javascript)](/it-it/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

Questo argomento illustra come usare la logica sul lato servizio per autorizzare gli utenti.  In questa esercitazione, vengono modificati i metodi di accesso ai dati in .NET, vengono filtrate le query in base agli ID utente e si consente agli utenti di accedere solo ai propri dati.

Questa esercitazione si basa sull'esercitazione di introduzione di Servizi mobili e fa riferimento all'esercitazione [Aggiunta dell'autenticazione a un'app esistente di Servizi mobili]. Completare prima l'esercitazione [Aggiunta dell'autenticazione a un'app esistente di Servizi mobili].

## <a name="register-scripts"></a>Modificare i metodi di accesso ai dati

1. In Visual Studio aprire il progetto di servizio mobile, espandere la cartella DataObjects e aprire **TodoItem.cs**. La classe **TodoItem** definisce l'oggetto dati ed è necessario aggiungere una proprietà **UserId** da usare per il filtro. Aggiungere la nuova proprietà UserId seguente alla classe **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario usare [Migrazioni Code First](/it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

2. In Visual Studio espandere la cartella Controllers e aprire **TodoItemController.cs**. Individuare il metodo **PostTodoItem** e aggiungere il seguente codice all'inizio del metodo. Questo codice aggiunge l'ID utente dell'utente autenticato all'elemento, prima che venga inserito nella tabella TodoItem.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Individuare il metodo **GetAllTodoItems** e sostituire l'istruzione **return** esistente con la seguente riga di codice. Questa query filtra gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. Ripubblicare il progetto di servizio mobile in Azure.


## <a name="test-app"></a>Test dell'app

1. Si noti che quando si esegue l'app sul lato client, anche se alcuni elementi sono già presenti nel database da esercitazioni precedenti, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna ID utente e ora presentano valori Null.

2. Se si dispone di altri account di accesso, verificare che gli utenti possono visualizzare solo i propri dati chiudendo ed eliminando l'app per poi eseguirla nuovamente. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.



<!-- Anchors. -->
[Registrare gli script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Aggiunta dell'autenticazione a un'app esistente di servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

<!--HONumber=45--> 
