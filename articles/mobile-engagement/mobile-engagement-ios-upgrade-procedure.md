---
title: Procedure di aggiornamento di Azure Mobile Engagement SDK per iOS | Microsoft Docs
description: Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: c4b5b8bc05365ddc63b0d7a6a3c63eaee31af957
ms.openlocfilehash: 37c7f133d079186f828d58cabce0d2a259efd085
ms.contentlocale: it-it
ms.lasthandoff: 12/14/2016

---
# <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

## <a name="from-300-to-400"></a>Dalla versione 3.0.0 alla 4.0.0
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

### <a name="usernotifications-framework"></a>Framework di UserNotifications
È necessario aggiungere il framework di `UserNotifications` nelle fasi di compilazione.

Nell'area di esplorazione dei progetti aprire il riquadro del progetto, quindi selezionare la destinazione corretta. Aprire quindi la scheda **"Build phases"** e nel menu **"Link Binary With Libraries"** aggiungere il framework `UserNotifications.framework`, quindi impostare il link come `Optional`

### <a name="application-push-capability"></a>Funzionalità di push dell'applicazione
XCode 8 può ripristinare la funzionalità di push dell'app; controllarla attentamente nella scheda `capability` della finestra di destinazione selezionata.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Aggiungere il nuovo codice di registrazione delle notifiche iOS 10
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

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Risolvere i conflitti del delegato UNUserNotificationCenter

*Se né l'applicazione né una delle librerie di terze parti implementa il valore `UNUserNotificationCenterDelegate`, è possibile ignorare questa parte.*

Un delegato `UNUserNotificationCenter` viene usato dall'SDK per monitorare il ciclo di vita delle notifiche di Engagement sui dispositivi che eseguono iOS 10 o versioni successive. L'SDK include un'implementazione specifica del protocollo `UNUserNotificationCenterDelegate`, ma può essere presente solo un delegato `UNUserNotificationCenter` per ogni applicazione. Qualsiasi altro delegato aggiunto all'oggetto `UNUserNotificationCenter` sarà in conflitto con l'oggetto Engagement. Se l'SDK rileva il delegato dell'utente o di terze parti, non userà l'implementazione specifica per consentire di risolvere i conflitti. Sarà necessario aggiungere la logica di Engagement al delegato per risolvere i conflitti.

A questo scopo è possibile procedere in due modi:

Proposta 1: inoltrando semplicemente le chiamate del delegato all'SDK

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

Proposta 2: ereditandola dalla classe `AEUserNotificationHandler`

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

Assicurarsi che il delegato dell'oggetto `UNUserNotificationCenter` sia impostato sul delegato entro il metodo `application:willFinishLaunchingWithOptions:` o il metodo `application:didFinishLaunchingWithOptions:` del delegato dell'applicazione.
Se, ad esempio, è stata implementata la Proposta 1 precedente:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="from-200-to-300"></a>Dalla versione 2.0.0 alla 3.0.0
Eliminazione del supporto per iOS 4.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

Se si usa Reach nell'applicazione, è necessario aggiungere il valore `remote-notification` alla matrice `UIBackgroundModes` nel file Info.plist per ricevere notifiche remote.

Il metodo `application:didReceiveRemoteNotification:` deve essere sostituito da `application:didReceiveRemoteNotification:fetchCompletionHandler:` nel delegato dell'applicazione.

"AEPushDelegate.h" è un'interfaccia deprecata ed è necessario rimuovere tutti i riferimenti. Ciò include la rimozione di `[[EngagementAgent shared] setPushDelegate:self]` e dei metodi delegati dal delegato dell'applicazione:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>Dalla versione 1.16.0 alla 2.0.0
La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement.
Se si esegue la migrazione da una versione precedente, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.16 e quindi applicare la procedura seguente.

> [!IMPORTANT]
> Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement
> 
> 

### <a name="agent"></a>Agente
Il metodo `registerApp:` è stato sostituito dal nuovo metodo `init:`. È necessario aggiornare il delegato dell'applicazione di conseguenza e usare la stringa di connessione:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Se il rilevamento di SmartAd è stato rimosso dall'SDK, è sufficiente rimuovere tutte le istanze della classe `AETrackModule` .

### <a name="class-name-changes"></a>Modifiche del nome della classe
Durante la procedura di rebranding, è necessario modificare alcuni nomi di classi/file.

A tutte le classi con prefisso "CP" viene assegnato quello "AE".

Esempio:

* `CPModule.h` viene rinominata come `AEModule.h`.

Tutte le classi con prefisso "Capptain" vengono rinominate con il prefisso "Engagement".

Esempi:

* La classe `CapptainAgent` viene rinominata come `EngagementAgent`.
* La classe `CapptainTableViewController` viene rinominata come `EngagementTableViewController`.
* La classe `CapptainUtils` viene rinominata come `EngagementUtils`.
* La classe `CapptainViewController` viene rinominata come `EngagementViewController`.


