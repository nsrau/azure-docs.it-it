---
title: Aggiungere notifiche push all&quot;app Android per Xamarin | Documentazione Microsoft
description: Informazioni su come usare Servizio app di Azure e Hub di notifica di Azure per inviare notifiche push all&quot;app per Xamarin Android.
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c9f8c46fa5fba1e69382a383ff94700de07db4ab


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Aggiungere notifiche push all'app Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overview
In questa esercitazione vengono aggiunte notifiche push al progetto [avvio rapido di Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md), in modo che una notifica push venga inviata al dispositivo a ogni inserimento di record.

Se non si usa il progetto server di avvio rapido scaricato, sarà necessario aggiungere il pacchetto di estensione di notifica push. Per altre informazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* Account Google attivo È possibile iscriversi a un account di Google in [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Componente client di Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/)

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>Configurare un hub di notifica
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraenable-firebase-cloud-messaging"></a><a id="register"></a>Abilitare Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configurare Azure per l'invio di richieste push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="a-idupdate-serveraupdate-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Aggiornare il progetto server per l'invio di notifiche push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-idconfigure-appaconfigure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Configurare il progetto client per le notifiche push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="a-idadd-pushaadd-push-notifications-code-to-your-app"></a><a id="add-push"></a>Aggiungere il codice delle notifiche push all'app
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>Testare le notifiche push nell'app
È possibile testare l'app mediante un dispositivo virtuale nell'emulatore. Ci sono altri passaggi di configurazione richiesti durante l'esecuzione in un emulatore.

1. Assicurarsi di distribuire o eseguire il debug su un dispositivo virtuale che ha le API Google API impostate come destinazione, come illustrato di seguito nel gestore del dispositivo virtuale Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Aggiungere un account Google al dispositivo Android facendo clic su **Apps** (App) > **Settings** (Impostazioni) > **Add account** (Aggiungi account), quindi seguire le istruzioni.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Eseguire l'app todolist come fatto in precedenza e inserire un nuovo elemento todo. Questa volta, viene visualizzata un'icona di notifica nell'area di notifica. È possibile aprire la cassetta della notifica per visualizzare il testo completo della notifica.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Avvio rapido di Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/



<!--HONumber=Nov16_HO3-->


