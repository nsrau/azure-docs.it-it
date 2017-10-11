---
title: Esercitazione sull'invio di ultime notizie localizzate per iOS mediante Hub di notifica
description: Informazioni su come usare Hub di notifica del bus di servizio di Azure per inviare notifiche relative alle ultime notizie localizzate (iOS).
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: fd2b7d9dfd4f432bbcbaa3ed76f8bec0b9677e17
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Uso di Hub di notifica per inviare le ultime notizie localizzate ai dispositivi iOS
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Panoramica
In questo argomento viene illustrato come utilizzare la funzionalità relativa ai [modelli](notification-hubs-templates-cross-platform-push-messages.md) di Hub di notifica di Azure per trasmettere notifiche relative alle ultime notizie localizzate in base alla lingua e al dispositivo. In questa esercitazione verrà utilizzata l'app di iOS creata in [Utilizzo di Hub di notifica per inviare le e ultime notizie]. Al termine, sarà possibile effettuare la registrazione per le categorie di proprio interesse, specificare la lingua in cui ricevere le notifiche e ricevere solo notifiche push per le categorie selezionate nella lingua scelta.

Lo scenario è composto da due parti:

* l'app per iOS consente ai dispositivi client di specificare una lingua e di sottoscrivere categorie diverse di ultime notizie;
* il back-end trasmette le notifiche usando le funzionalità relative ai **tag** e ai **modelli** di Hub di notifica di Azure.

## <a name="prerequisites"></a>Prerequisiti
È necessario aver completato l'esercitazione [Utilizzo di Hub di notifica per inviare le e ultime notizie] e disporre del relativo codice, in quanto questa esercitazione è basata direttamente su tale codice.

Visual Studio 2012 o versione successiva è facoltativo.

## <a name="template-concepts"></a>Concetti relativi ai modelli
In [Utilizzo di Hub di notifica per inviare le e ultime notizie] è stata creata un'app che utilizza i **tag** per sottoscrivere le notifiche per diverse categorie di notizie.
Molte app, tuttavia, sono destinate a più mercati ed è necessario localizzarle. Questo significa che il contenuto delle notifiche deve essere localizzato e inviato al set di dispositivi corretto.
In questo argomento verrà illustrato come usare la funzionalità relativa ai **modelli** di Hub di notifica per inviare facilmente notifiche relative alle ultime notizie localizzate.

Nota: un possibile modo per inviare notifiche localizzate consiste nel creare più versioni di ogni tag. Per supportare l'inglese, il francese e il mandarino, ad esempio, sono necessari tre tag diversi per le ultime notizie internazionali: "world_en", "world_fr" e "world_ch". È quindi necessario inviare una versione localizzata delle ultime notizie internazionali a ogni tag. In questo argomento verranno utilizzati i modelli per evitare la proliferazione di tag e la necessità di inviare più messaggi.

In linea generale, i modelli consentono di specificare in che modo uno specifico dispositivo deve ricevere una notifica. Il modello definisce lo specifico formato di payload da utilizzare, facendo riferimento alle proprietà del messaggio inviato dal back-end dell'app. In questo caso verrà inviato un messaggio indipendente dalle impostazioni locali, che contiene tutte le lingue supportate:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Si procederà quindi a verificare che i dispositivi effettuino la registrazione con un modello che faccia riferimento alla proprietà corretta. Per un'app per iOS che deve essere registrata per le notizie in francese, ad esempio, si effettuerà la registrazione nel modo seguente:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

I modelli rappresentano uno strumento particolarmente efficace. Per altre informazioni, vedere l'articolo [Modelli](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="the-app-user-interface"></a>Interfaccia utente dell'app
L'app Breaking News creata nell'argomento [Utilizzo di Hub di notifica per inviare le e ultime notizie] verrà ora modificata in modo da inviare notizie localizzate mediante modelli.

In MainStoryboard_iPhone.storyboard aggiungere un controllo Segmented con le tre lingue che saranno supportate: inglese, francese e mandarino.

![][13]

Assicurarsi quindi di aggiungere un elemento IBOutlet in ViewController.h come illustrato di seguito:

![][14]

## <a name="building-the-ios-app"></a>Compilazione dell'app per iOS
1. In Notification.h aggiungere il metodo *retrieveLocale* , quindi modificare i metodi store e subscribe come illustrato di seguito:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    In Notification.m modificare il metodo *storeCategoriesAndSubscribe* aggiungendo il parametro relativo alle impostazioni locali e archiviandolo nelle impostazioni predefinite dell'utente:
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
   
    Modificare quindi il metodo *subscribe* in modo da includere le impostazioni locali:
   
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
   
    Si noti che si sta ora usando il metodo *registerTemplateWithDeviceToken* anziché *registerNativeWithDeviceToken*. Quando si effettua la registrazione per un modello, è necessario fornire il modello json e un nome per il modello. Questo perché l'app potrebbe richiedere la registrazione di modelli diversi. Assicurarsi di registrare le categorie come tag, poiché è necessario ricevere notifiche per le notizie.
   
    Aggiungere un metodo per recuperare le impostazioni locali dalle impostazioni predefinite dell'utente:
   
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
2. Ora che è stata modificata la classe Notifications, è necessario assicurarsi che ViewController utilizzi il nuovo UISegmentControl. Aggiungere la riga seguente nel metodo *viewDidLoad* per assicurarsi che vengano mostrate le impostazioni locali attualmente selezionate:
   
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
   
    Nel metodo *subscribe* modificare quindi la chiamata a *storeCategoriesAndSubscribe* come segue:
   
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
3. Aggiornare infine il metodo *didRegisterForRemoteNotificationsWithDeviceToken* in AppDelegate.m, affinché sia possibile aggiornare correttamente la registrazione all'avvio dell'applicazione. Modificare la chiamata al metodo *subscribe* delle notifiche come segue:
   
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facoltativo) Invio di notifiche modello localizzate dall’app console .NET.
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(facoltativo) Invio di notifiche modello localizzato dal dispositivo
Se non si ha accesso a Visual Studio o si desidera solo l'invio di notifiche modello localizzato direttamente dall'app sul dispositivo.  È possibile aggiungere semplicemente i parametri di modello localizzato per il metodo `SendNotificationRESTAPI` definito nell'esercitazione precedente.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei modelli, vedere:

* [Usare Hub di notifica per inviare notifiche agli utenti: ASP.NET]
* [Usare Hub di notifica per inviare notifiche agli utenti: Servizi mobili]

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizzo di Hub di notifica per inviare le e ultime notizie]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Usare Hub di notifica per inviare notifiche agli utenti: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Usare Hub di notifica per inviare notifiche agli utenti: Servizi mobili]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
