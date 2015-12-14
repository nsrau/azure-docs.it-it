<properties
	pageTitle="Autorizzazione sul lato servizio degli utenti in un servizio mobile back-end di JavaScript | Microsoft Azure"
	description="Informazioni su come autorizzare gli utenti nel back-end JavaScript di Servizi mobili di Azure."
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="08/25/2015"
	ms.author="krisragh"/>

# Autorizzazione sul lato servizio degli utenti in Servizi mobili

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Questo argomento illustra come usare gli script sul lato servizio per autorizzare gli utenti. In questa esercitazione, vengono registrati script con Servizi mobili di Azure, vengono filtrate le query in base agli ID utente e si consente agli utenti di accedere solo ai propri dati. Il filtro dei risultati di query dell'utente per ID utente è la forma più elementare di autorizzazione. A seconda dello scenario specifico, è possibile creare tabelle di utenti e ruoli al fine di tenere traccia delle informazioni di autorizzazione utente più dettagliate, ad esempio gli endpoint a cui un determinato utente può accedere.

Questa esercitazione si basa sull'esercitazione di introduzione di Servizi mobili e fa riferimento ad [Aggiungere l'autenticazione all'app di Servizi mobili esistente]. Completare prima l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili esistente].

## <a name="register-scripts"></a>Registrare gli script

1. Accedere al [portale di Azure classico], fare clic su **Servizi mobili** e quindi sul servizio mobile. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

2. Fare clic su **Script**, selezionare l’operazione **Inserisci**, sostituire lo script esistente con la funzione seguente, quindi fare clic su **Salva**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	Questo script aggiunge l'ID utente dell'utente autenticato all'elemento prima dell'inserimento.

    >[AZURE.NOTE]Assicurarsi che [lo schema dinamico](https://msdn.microsoft.com/library/azure/jj193175.aspx) sia abilitato. In caso contrario, la colonna *userId* non verrà aggiunta automaticamente. Questa opzione è abilitata per impostazione predefinita per un nuovo servizio mobile.

3. Analogamente, sostituire l'operazione **Read** esistente con la funzione seguente. Questo script consente di filtrare gli oggetti TodoItem in modo che un utente riceva solo gli elementi inseriti personalmente.

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

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Aggiungere l'autenticazione all'app di Servizi mobili esistente]: /develop/mobile/tutorials/get-started-with-users-ios

[portale di Azure classico]: https://manage.windowsazure.com/
 

<!---HONumber=AcomDC_1203_2015-->