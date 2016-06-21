<properties
	pageTitle="Aggiungere notifiche push all'app per Android con le app per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili di Azure per inviare notifiche push all'app per Android."
	services="app-service\mobile"
	documentationCenter="android"
	manager="erikre"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="yuaxu;ricksal"/>

# Aggiungere notifiche push all'app Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Panoramica
In questa esercitazione si aggiungeranno notifiche push al progetto di [avvio rapido di Android ] per attivare l'invio di una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di Android ], che deve essere completata per prima. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Account Google](http://go.microsoft.com/fwlink/p/?LinkId=268302) con un indirizzo di posta elettronica verificato.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) (non necessario per un progetto di back-end Node.js).
* Completare l'[esercitazione di avvio rapido](app-service-mobile-android-get-started.md).

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##Configurare il back-end dell’app per dispositivi mobili per l'invio di richieste push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Aggiungere notifiche push all'app

È necessario assicurarsi che il progetto dell’app per Android sia pronto per gestire le notifiche push.

###Verificare la versione di Android SDK

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

###Aggiungere Google Play Services al progetto

[AZURE.INCLUDE [Aggiungere Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

###Aggiungere codice

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Eseguire il test dell'app sul servizio mobile pubblicato

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

##<a id="more"></a>Altro

* I tag consentono di orientarsi a clienti segmentati con notifiche push. [Lavorare con l’SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) illustra come aggiungere tag all’installazione di un dispositivo.

<!-- URLs -->
[avvio rapido di Android ]: app-service-mobile-android-get-started.md

[configurazione di Google Play Services SDK]: https://developers.google.com/android/guides/setup

<!---HONumber=AcomDC_0608_2016-->