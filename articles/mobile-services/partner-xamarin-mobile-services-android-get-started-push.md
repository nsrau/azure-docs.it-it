<properties
	pageTitle="Aggiungere notifiche push all'app Android per Xamarin | Microsoft Azure"
	description="Informazioni su come configurare le notifiche push con Google Cloud Messaging per le app Xamarin.Android mediante Servizi mobili di Azure e Hub di notifica di Azure."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="glenga"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Panoramica
Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.Android. In questa esercitazione si userà il servizio Google Cloud Messaging (GCM) per aggiungere notifiche push al progetto [Introduzione a Servizi mobili]. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Per completare questa esercitazione, è necessario disporre di:

+ Account Google attivo
+ [Componente client di Google Cloud Messaging] Questo componente verrà aggiunto durante l'esercitazione.

Si presuppone che i componenti [Xamarin.Android] e [Servizi mobili di Azure] siano già installati nel progetto da quando è stata completata l'esercitazione [Introduzione a Servizi mobili].

##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configurare il servizio mobile per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Aggiornare lo script insert registrato per l'invio di notifiche

>[AZURE.TIP]Le procedure seguenti descrivono come aggiornare lo script registrato per l'operazione di inserimento nella tabella TodoItem del portale di Azure classico. È anche possibile accedere e modificare questo script del servizio mobile direttamente in Visual Studio, nel nodo Azure di Esplora server.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>Configurare il progetto esistente per le notifiche push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Aggiungere il codice delle notifiche push all'app

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>Testare le notifiche push nell'app

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

L'esercitazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Introduzione all'autenticazione](mobile-services-android-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi tramite Servizi mobili.

* [Informazioni su Hub di notifica](../notification-hubs-overview.md) <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive per la risoluzione dei problemi e il debug delle soluzioni di Hub di notifica.

* [Come usare la libreria client .NET per Servizi mobili](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Informazioni sull'uso di Servizi mobili con codice C# per Xamarin.

* [Riferimento per gli script del server di Servizi mobili](mobile-services-how-to-use-server-scripts.md) <br/>Informazioni su come implementare la logica di business nel servizio mobile.

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-ios-get-started.md

[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_1203_2015-->