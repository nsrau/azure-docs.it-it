<properties
	pageTitle="Aggiungere notifiche push all'app Xamarin.Android| Servizio app di Azure"
	description="Informazioni su come usare Servizio app di Azure e Hub di notifica di Azure per inviare notifiche push all'app per Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="glenga"/>

# Aggiungere notifiche push all'app Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Overview

In questa esercitazione vengono aggiunte notifiche push al progetto [Creare un'app per Xamarin.Android] in modo che venga inviata una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione [Creare un'app per Xamarin.Android] che deve essere completata per prima. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Account Google attivo È possibile iscriversi a un account di Google in [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

+ [Componente client di Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/) Questo componente verrà aggiunto durante l'esercitazione.

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [Abilitare GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configurare il back-end dell’app per dispositivi mobili per l'invio di richieste push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurare il progetto client per le notifiche push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Aggiungere il codice delle notifiche push all'app

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testare le notifiche push nell'app

È possibile testare l'app mediante un dispositivo virtuale nell'emulatore. Ci sono altri passaggi di configurazione richiesti durante l'esecuzione in un emulatore.

1. Assicurarsi di distribuire o eseguire il debug su un dispositivo virtuale che ha le API Google API impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android (AVD).

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Aggiungere un account Google nel dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiungi account**, quindi seguire le istruzioni per usare Aggiungi un account di Google esistente sul dispositivo per crearne uno nuovo.

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Eseguire l'app todolist come fatto in precedenza e inserire un nuovo elemento todo. Questa volta, viene visualizzata un'icona di notifica nell'area di notifica. È possibile aprire la cassetta della notifica per visualizzare il testo completo della notifica.

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quickstart]: app-service-mobile-xamarin-android-get-started.md
[Creare un'app per Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0817_2016-->