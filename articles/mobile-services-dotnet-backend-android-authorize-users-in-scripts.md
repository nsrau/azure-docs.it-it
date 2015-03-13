<properties 
	pageTitle="Autorizzazione lato servizio (Android) | Mobile Dev Center" 
	description="Informazioni su come autorizzare gli utenti nel back-end .NET di Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="glenga"/>

# Autorizzazione sul lato servizio degli utenti di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

Questo argomento descrive come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app per Android. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione].  

## <a name="register-scripts"></a>Modifica dei metodi di accesso ai dati

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## Testare l'app

1. In Eclipse aprire il progetto modificato durante l'esercitazione [Introduzione all'autenticazione].

2. Dal menu **Run** scegliere **Run** per avviare il progetto nell'emulatore di Android.

   	Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3. Nell'app digitare un testo in **Add a ToDo item** e quindi fare clic su **Save**.

   	![][3]

   	Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

5. Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Sfoglia** e verificare che a ogni elemento appena aggiunto sia associato un valore userID.

6. (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'uso dell'autenticazione è terminata. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di Android per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con Android.

<!-- Anchors. -->
[Registrare gli script del server]: #register-scripts
[Passaggi successivi]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push

[Riferimento per i concetti e le procedure di Android per Servizi mobili]: /it-it/documentation/articles/mobile-services-android-how-to-use-client-library/


<!--HONumber=42-->
