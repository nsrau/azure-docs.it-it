<properties pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="" solutions="" writer="glenga" manager="" editor="" />

Chiamata di un'API personalizzata dal client
============================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS")[Android](/it-it/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Back-end JavaScript")

In questo argomento viene descritto come chiamare un'API personalizzata da un'app di Windows Store. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-windows-store-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). A questo scopo, verranno eseguiti i passaggi seguenti:

1.  [Definizione dell'API personalizzata](#define-custom-api)
2.  [Aggiornamento dell'app per la chiamata all'API personalizzata](#update-app)
3.  [Test dell'app](#test-app)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-windows-store-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Per completare questa esercitazione, è necessario utilizzare Visual Studio 2012 Express per Windows 8.

Definizione dell'API personalizzata
-----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

Passaggi successivi
-------------------

Dopo avere creato un'API personalizzata e averla chiamata dall'app di Windows Store, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

-   [Definizione di un'API personalizzata che supporta le notifiche periodiche](/it-it/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications)
    <br/>Ulteriori informazioni su come utilizzare un'API personalizzata che supporta le notifiche periodiche in un'app di Windows Store. Se le notifiche periodiche sono abilitate, Windows accederà periodicamente all'endpoint dell'API personalizzata e utilizzerà il file XML restituito, in un formato specifico del riquadro, per aggiornare il riquadro dell'app nel menu Start.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Ulteriori informazioni sulla creazione di API personalizzate.

-   [Archiviazione degli script del server nel controllo del codice sorgente](/it-it/documentation/articles/mobile-services-store-scripts-source-control)
    <br/>Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.


<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Get started with authentication]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Get started with push notifications]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Define a custom API that supports periodic notifications]: /it-it/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Store server scripts in source control]: /it-it/documentation/articles/mobile-services-store-scripts-source-control