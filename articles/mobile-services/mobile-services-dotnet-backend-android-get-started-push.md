<properties
	pageTitle="Introduzione a push (Android) | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app .Net per Android."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Aggiungere notifiche push all'app di Servizi mobili
[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere notifiche push all'app Android](../app-service-mobile/app-service-mobile-android-get-started-push.md).
 
&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare [Introduzione a Servizi mobili] per connettere il progetto al servizio mobile. Di conseguenza, per completare l'esercitazione è necessario anche Visual Studio 2013.

## Codice di esempio
Per visualizzare il codice sorgente completato, passare [qui](https://github.com/RickSaling/mobile-services-samples/tree/push/GettingStartedWithPush).

## Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Configurare il servizio mobile per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Aggiornare il servizio per l'invio di notifiche push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## Aggiungere notifiche push all'app

###Verificare la versione di Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Aggiungere codice

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

## Eseguire il test dell'app sul servizio mobile pubblicato

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

### Abilitare le notifiche push per test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

L'esercitazione è stata completata.

## Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per Android di usare Servizi mobili e Hub di notifica per inviare notifiche push. In seguito può essere opportuno completare l'esercitazione seguente, Inviare notifiche push agli utenti autenticati, che spiega come usare i tag per inviare notifiche push da un servizio mobile unicamente agli utenti autenticati.

+ [Inviare notifiche di trasmissione ai sottoscrittori] <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie a cui sono interessati.

+ [Inviare notifiche basate su modelli ad altri sottoscrittori] <br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per altre informazioni su Servizi mobili e su Hub di notifica, vedere i seguenti argomenti:

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni di Hub di notifica.

* [Come usare la libreria client di Android per Servizi mobili] <br/>Altre informazioni su come usare Servizi mobili con Android.

<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-ec.md
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-android-get-started.md
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Come usare la libreria client di Android per Servizi mobili]: mobile-services-android-how-to-use-client-library.md

[Informazioni su Hub di notifica]: ../notification-hubs-overview.md
[Inviare notifiche di trasmissione ai sottoscrittori]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Inviare notifiche basate su modelli ad altri sottoscrittori]: ../notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!---HONumber=AcomDC_0907_2016-->