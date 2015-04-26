<properties 
	pageTitle="Autorizzazione lato servizio (HTML) | Mobile Dev Center" 
	description="Informazioni su come autorizzare gli utenti nel back-end JavaScript di Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Autorizzazione sul lato servizio degli utenti di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


Questo argomento descrive come usare gli script del server per autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app HTML.  In questa esercitazione verranno registrati gli script con Servizi mobili per filtrare le query in base all'userId di un utente autenticato, per garantire che ogni utente possa visualizzare solo i propri dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione].  

## <a name="register-scripts"></a>Registrare gli script
Poiché l'app di guida introduttiva legge e inserisce i dati, è necessario registrare gli script per queste operazioni sulla tabella TodoItem.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][0]

2. Fare clic sulla scheda **Dati** quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

   	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Questo script aggiunge un valore userId, ovvero l'ID utente dell'utente autenticato, all'elemento prima che venga inserito nella tabella TodoItem. 

    > [AUZRE.IMPORTANT] È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **userId** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.

5. Ripetere i passaggi 3 e 4 per sostituire l'operazione **Read** esistente con la funzione seguente:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Questo script consente di filtrare gli oggetti TodoItem restituiti, in modo che ogni utente riceva solo gli elementi inseriti personalmente.

## Testare l'app

1. In un Web browser, passare alla pagina index.html dell'app, accedere al provider di identità scelto. 

   	Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3. Nell'app digitare un testo in **Enter new task** e quindi fare clic su **Add**.

   	![][3]

   	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

5. Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Sfoglia** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

6. (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i relativi dati, chiudere l'app (ALT+F4) ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati. 

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'uso dell'autenticazione è terminata. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

* [Riferimento per i concetti e le procedure di HTML/JavaScript per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con HTML/JavaScript.
  
<!-- Anchors. -->
[Registrare gli script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-html
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
