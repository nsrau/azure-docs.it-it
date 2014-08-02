<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Windows Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Call a custom API from the client" />

Chiamata di un'API personalizzata dal client
============================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS")[Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-android-call-custom-api "Back-end JavaScript")

In questo argomento viene descritto come chiamare un'API personalizzata da un'app per Android. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag *completed* su `true` per tutti gli elementi todo nella tabella del servizio mobile. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/) o [Introduzione ai dati](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/). A questo scopo, verranno eseguiti i passaggi seguenti:

1.  [Definizione dell'API personalizzata](#define-custom-api)
2.  [Aggiornamento dell'app per la chiamata all'API personalizzata](#update-app)
3.  [Test dell'app](#test-app)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/).

Definizione dell'API personalizzata
-----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

Passaggi successivi
-------------------

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Android, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Ulteriori informazioni sulla creazione di API personalizzate.

-   [Archiviazione degli script del server nel controllo del codice sorgente](/en-us/documentation/articles/mobile-services-store-scripts-source-control)
     <br/>Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.


<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Store server scripts in source control]: /en-us/documentation/articles/mobile-services-store-scripts-source-control
