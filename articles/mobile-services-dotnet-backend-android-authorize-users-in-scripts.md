<properties pageTitle="Autorizzazione lato servizio (Android) | Mobile Developer Center" metaKeywords="" description="Informazioni su come autorizzare gli utenti nel back-end .NET di Servizi mobili di Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Autorizzazione lato servizio degli utenti di Servizi mobili" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# Autorizzazione lato servizio degli utenti di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

In questo argomento viene illustrato come autorizzare gli utenti autenticati per accedere ai dati in Servizi mobili di Azure da un'app per Android. In questa esercitazione verrà aggiunto il codice ai metodi di accesso ai dati nel controller per filtrare le query in base all'ID utente di un utente autenticato, per garantire che ogni utente possa visualizzare solo i relativi dati.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione precedente [Introduzione all'autenticazione][Introduzione all'autenticazione]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione all'autenticazione][Introduzione all'autenticazione].

## <a name="register-scripts"></a>Modifica dei metodi di accesso ai dati

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## Test dell'app

1.  In Eclipse aprire il progetto modificato durante l'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione].

2.  Scegliere **Run** dal menu **Run** per avviare il progetto nell'emulatore di Android.

    Si noti che questa volta non viene restituito alcun elemento, anche se nel corso delle esercitazioni precedenti sono stati aggiunti elementi nella tabella TodoItem. Questo si verifica perché gli elementi precedenti sono stati inseriti senza la colonna userId e ora presentano valori Null.

3.  Nell'app digitare un testo in **Add a ToDo item** e quindi fare clic su **Save**.

    ![][0]

    Testo e userId verranno inseriti nella tabella TodoItem nel servizio mobile. Poiché il nuovo elemento contiene il valore userId corretto, viene restituito dal servizio mobile e i dati vengono visualizzati nella seconda colonna.

4.  Tornare alla tabella **todoitem** nel [portale di gestione][portale di gestione di Azure], quindi fare clic su **Browse** e verificare che a ogni elemento appena aggiunto sia associato un valore userId.

5.  (Facoltativo) Se si dispone di altri account di accesso, per verificare che gli utenti possano visualizzare solo i propri dati, chiudere l'app ed eseguirla di nuovo. Quando viene visualizzata la finestra di dialogo per l'immissione delle credenziali di accesso, immettere un account di accesso diverso e verificare che i dati immessi nell'account precedente non siano visualizzati.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo dell'autenticazione è terminata. Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con Android][Riferimento per i concetti e le procedure di Servizi mobili con Android]
    Ulteriori informazioni su come usare Servizi mobili con Android.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [0]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Riferimento per i concetti e le procedure di Servizi mobili con Android]: /it-it/documentation/articles/mobile-services-android-how-to-use-client-library/
