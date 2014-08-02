<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" />

Chiamata di un'API personalizzata dal client
============================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/en-us/documentation/articles/mobile-services-android-call-custom-api "Android")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-android-call-custom-api "Back-end JavaScript")

In questo argomento viene descritto come chiamare un'API personalizzata da un'app per Android. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag *completed* su `true` per tutti gli elementi todo nella tabella del servizio mobile. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-android-get-started/) o [Introduzione ai dati](/en-us/documentation/articles/mobile-services-android-get-started-data/). A questo scopo, verranno eseguiti i passaggi seguenti:

1.  [Definizione dell'API personalizzata](#define-custom-api)
2.  [Aggiornamento dell'app per la chiamata all'API personalizzata](#update-app)
3.  [Test dell'app](#test-app)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-android-get-started/) o [Introduzione ai dati](/en-us/documentation/articles/mobile-services-android-get-started-data/).

Definizione dell'API personalizzata
-----------------------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

Passaggi successivi
-------------------

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Android, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)

    Ulteriori informazioni sulla creazione di API personalizzate.

-   [Archiviazione degli script del server nel controllo del codice sorgente](/en-us/documentation/articles/mobile-services-store-scripts-source-control)

     Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.


