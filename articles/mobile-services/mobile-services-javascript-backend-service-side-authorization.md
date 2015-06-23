<properties
	pageTitle="Autorizzazione sul lato servizio degli utenti in Servizi mobili con back-end JavaScript | Mobile Developer Center"
	description="Informazioni su come autorizzare gli utenti nel back-end JavaScript di Servizi mobili di Azure."
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

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Questo argomento illustra come usare gli script sul lato servizio per autorizzare gli utenti. In questa esercitazione, vengono registrati script con Servizi mobili di Azure, vengono filtrate le query in base agli ID utente e si consente agli utenti di accedere solo ai propri dati.

Questa esercitazione si basa sull'esercitazione di introduzione di Servizi mobili e fa riferimento ad [Aggiungere l'autenticazione all'app di Servizi mobili esistente]. Completare prima l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili esistente].

## <a name="register-scripts"></a>Registrare gli script

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

2. Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

   	![][2]

3. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**. Questo script aggiunge l'ID utente dell'utente autenticato all'elemento prima dell'inserimento.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [Dynamic schema must be enabled](https://msdn.microsoft.com/library/azure/jj193175.aspx) per il funzionamento. Questa impostazione è abilitata per impostazione predefinita per i nuovi servizi mobili.

5. Analogamente, sostituire l'operazione **Read** esistente con la funzione seguente. Questo script consente di filtrare gli oggetti TodoItem in modo che un utente riceva solo gli elementi inseriti personalmente.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Testare l'app

1. Si noti che quando si esegue l'app sul lato client, anche se alcuni elementi sono già presenti nella tabella _TodoItem_ da esercitazioni precedenti, non viene restituito alcun elemento. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna ID utente e ora presentano valori Null. Verificare che agli elementi appena aggiunti sia associato un valore userId nella tabella _TodoItem_.

2. Se si dispone di altri account di accesso, verificare che gli utenti possono visualizzare solo i propri dati chiudendo ed eliminando l'app per poi eseguirla nuovamente. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-javascript-backend-service-side-authorization/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-with-data-ios
[Aggiungere l'autenticazione all'app di Servizi mobili esistente]: /develop/mobile/tutorials/get-started-with-users-ios
[Add Push Notifications to Existing App]: /develop/mobile/tutorials/get-started-with-push-ios

[portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=54--> 