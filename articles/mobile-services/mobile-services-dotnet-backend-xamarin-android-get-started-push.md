<properties 
	pageTitle="Introduzione a Servizi mobili per app per Xamarin Android - Servizi mobili di Azure" 
	description="Informazioni su come usare Servizi mobili di Azure e Hub di notifica per inviare notifiche push all'app per Xamarin Android." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Questo argomento descrive come usare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.Android. In questa esercitazione si userà il servizio Google Cloud Messaging (GCM) per aggiungere notifiche push al progetto [Introduzione a Servizi mobili]. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

Questa esercitazione descrive le operazioni di base per abilitare le notifiche push:

1. [Abilitare Google Cloud Messaging](#register)
2. [Configurare Servizi mobili](#configure)
3. [Configurare il progetto per le notifiche push](#configure-app)
4. [Aggiungere il codice delle notifiche push all'app](#add-push)
5. [Inserire i dati per la ricezione delle notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

+ Account Google attivo
+ [Componente client di Google Cloud Messaging] Questo componente verrà aggiunto durante l'esercitazione.

Si presuppone che i componenti [Xamarin.Android] e [Servizi mobili di Azure][Azure Mobile Services Component] siano già installati nel progetto da quando è stata completata l'esercitazione [Introduzione a Servizi mobili].

##<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [Abilitare GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configurare il servizio mobile per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Aggiornare il servizio per l'invio di notifiche push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Configurare il progetto esistente per le notifiche push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Aggiungere il codice delle notifiche push all'app

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Testare l'app sul servizio mobile pubblicato

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

###<a id="local-testing"></a> Abilitare le notifiche push per i test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=July15_HO2-->