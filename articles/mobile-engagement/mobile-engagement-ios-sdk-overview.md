---
title: Panoramica di Azure Mobile Engagement SDK per iOS | Microsoft Docs
description: Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8bfadc110b8b2e0de470185ec9d84343125c960d


---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK per Azure Mobile Engagement
Da qui, è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app per iOS. Se si vuole fare prima una prova, eseguire l' [esercitazione di 15 minuti](mobile-engagement-ios-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per iOS](mobile-engagement-ios-integrate-engagement.md)
2. Per le notifiche: [Come integrare il servizio di copertura (di notifica) in un'app per iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Note sulla versione
### <a name="400-09122016"></a>4.0.0 (12/09/2016)
* Risolto il problema delle notifiche non prese in considerazione sui dispositivi iOS 10.
* Deprecare XCode 7.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere [Procedure di aggiornamento](mobile-engagement-ios-upgrade-procedure.md) per informazioni complete.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

### <a name="from-300-to-400"></a>Dalla versione 3.0.0 alla 4.0.0
### <a name="xcode-8"></a>XCode 8
XCode 8 è un componente obbligatorio a partire dalla versione 4.0.0 dell'SDK.

> [!NOTE]
> Se si dipende davvero da XCode 7, è possibile usare [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Esiste un bug noto nel modulo di copertura della versione precedente durante l'esecuzione su dispositivi iOS 10: le notifiche di sistema non vengono attivate. Per risolvere il problema è necessario implementare l'API deprecata `application:didReceiveRemoteNotification:` nel delegato dell'app come segue:
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Questa soluzione non è consigliata** dal momento che tale comportamento può cambiare in qualsiasi aggiornamento della versione iOS imminente (anche minore), poiché questa API iOS è deprecata. È opportuno passare a XCode 8 il prima possibile.
> 
> 

#### <a name="usernotifications-framework"></a>Framework di UserNotifications
È necessario aggiungere il framework di `UserNotifications` nelle fasi di compilazione.

Nell'area di esplorazione dei progetti aprire il riquadro del progetto, quindi selezionare la destinazione corretta. Aprire quindi la scheda **"Build phases"** e nel menu **"Link Binary With Libraries"** aggiungere il framework `UserNotifications.framework`, quindi impostare il link come `Optional`

#### <a name="application-push-capability"></a>Funzionalità di push dell'applicazione
XCode 8 può ripristinare la funzionalità di push dell'app; controllarla attentamente nella scheda `capability` della finestra di destinazione selezionata.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Aggiungere il nuovo codice di registrazione delle notifiche iOS 10
Il frammento di codice precedente per registrare l'app per le notifiche funziona ancora, ma usa API deprecate pur essendo eseguito in iOS 10. 

Importare il framework `User Notification` :

        #import <UserNotifications/UserNotifications.h>

Nel metodo `application:didFinishLaunchingWithOptions` del delegato dell'applicazione sostituire:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

con:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Se si dispone già di un'implementazione di UNUserNotificationCenterDelegate:
L'SDK ha la propria implementazione del protocollo UNUserNotificationCenterDelegate. Viene utilizzata dall'SDK per monitorare il ciclo di vita delle notifiche di Engagement sui dispositivi che eseguono iOS 10 o versioni successive. Se l'SDK rileva il delegato non utilizza la sua implementazione poiché può esistere un solo delegato UNUserNotificationCenter per applicazione. Ciò significa che è necessario aggiungere la logica di Engagement al proprio delegato.

A questo scopo è possibile procedere in due modi:

Inoltrando semplicemente le chiamate del delegato all'SDK

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

oppure ereditandola dalla classe `AEUserNotificationHandler`

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> È possibile determinare se una notifica proviene o meno da Engagement passando il suo dizionario `userInfo` al metodo della classe dell'agente `isEngagementPushPayload:`.
> 
> 




<!--HONumber=Nov16_HO3-->


