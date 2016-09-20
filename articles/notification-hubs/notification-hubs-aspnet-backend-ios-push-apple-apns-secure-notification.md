<properties
	pageTitle="Push sicuro degli hub di notifica di Azure"
	description="Informazioni su come inviare notifiche push sicure a un'app per iOS da Azure. Gli esempi di codice sono scritti in Objective-C e C#."
	documentationCenter="ios"
	authors="wesmc7777"
	manager="erikre"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>  

#Push sicuro degli hub di notifica di Azure

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##Overview

Il supporto per le notifiche push in Microsoft Azure consente di accedere a un'infrastruttura push di facile utilizzo, multipiattaforma con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili.

A causa di vincoli normativi o di sicurezza, un'applicazione potrebbe talvolta includere nella notifica informazioni che non è possibile trasmettere attraverso l'infrastruttura di notifiche push standard. In questa esercitazione viene descritto come conseguire la stessa esperienza inviando informazioni sensibili attraverso una connessione autenticata e sicura tra il dispositivo client e il back-end dell'app.

A livello generale, il flusso è il seguente:

1. Il back-end dell'app:
	- Archivia il payload sicuro nel database back-end.
	- Invia l'ID di questa notifica al dispositivo (non vengono inviate informazioni sicure).
2. L'app sul dispositivo, quando riceve la notifica:
	- Il dispositivo contatta il back-end richiedendo il payload sicuro.
	- L'app può indicare il payload come una notifica sul dispositivo.

È importante notare che nel flusso precedente e in questa esercitazione si presuppone che il dispositivo archivi un token di autenticazione nella memoria locale, dopo l’accesso dell'utente. Ciò garantisce un'esperienza completamente lineare, in quanto il dispositivo può recuperare il payload sicuro della notifica tramite questo token. Se invece l'applicazione non archivia i token di autenticazione nel dispositivo o se questi hanno una scadenza, l'app per dispositivo, alla ricezione della notifica, dovrà visualizzare una notifica generica in cui si richiede all'utente di avviare l'app. L'app autentica quindi l'utente e mostra il payload di notifica.

In questa esercitazione sul push sicuro viene illustrato come inviare una notifica push in modo sicuro. Poiché i passaggi qui descritti si basano sull'esercitazione [Utilizzo di Hub di notifica per inviare notifiche agli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md), sarà prima necessario completare i passaggi di quest'ultima.

> [AZURE.NOTE] In questa esercitazione si presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modifica del progetto iOS

Ora che è stato modificato il back-end dell'app in modo da inviare solo l'*ID* di una notifica, è necessario modificare l'app per iOS in modo da gestire tale notifica e richiamare il back-end per recuperare il messaggio sicuro da visualizzare.

Per conseguire questo obiettivo, è necessario scrivere la logica per recuperare il contenuto sicuro dal back-end dell'app.

1. In **AppDelegate.m** assicurarsi che l'app esegua la registrazione per le notifiche silenziose in modo da elaborare l'ID della notifica inviato dal back-end. Aggiungere l'opzione **UIRemoteNotificationTypeNewsstandContentAvailability** in didFinishLaunchingWithOptions:

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. In **AppDelegate.m** aggiungere una sezione di implementazione in alto con la dichiarazione seguente:

		@interface AppDelegate ()
		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		@end

3. Aggiungere quindi la sezione di implementazione al seguente codice, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint per il back-end ottenuto in precedenza:

```
		NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		{
		    // check if authenticated
		    ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
		    NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
		    if (!authenticationHeader) return;


		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"GET"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

		            NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

		            completion([json objectForKey:@"Payload"], nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}
```

	This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. A questo punto, è necessario gestire la notifica in arrivo e usare il metodo sopra citato per recuperare il contenuto da visualizzare. In primo luogo, è necessario abilitare l'esecuzione dell'app per iOS in background quando riceve una notifica push. In **XCode**, selezionare il progetto dell'app nel riquadro sinistro, quindi fare clic sull'app di destinazione principale nella sezione **Destinazioni** nel riquadro centrale.

5. Fare quindi clic sulla scheda **Funzionalità** nella parte superiore del riquadro centrale e selezionare la casella di controllo **Notifiche remote**.

	![][IOS1]


6. In **AppDelegate.m** aggiungere il metodo seguente per gestire le notifiche push:

		-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
		{
		    NSLog(@"%@", userInfo);

		    [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
		        if (!error) {
		            // show local notification
		            UILocalNotification* localNotification = [[UILocalNotification alloc] init];
		            localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
		            localNotification.alertBody = payload;
		            localNotification.timeZone = [NSTimeZone defaultTimeZone];
		            [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

		            completionHandler(UIBackgroundFetchResultNewData);
		        } else {
		            completionHandler(UIBackgroundFetchResultFailed);
		        }
		    }];

		}

	Notare che è preferibile gestire i casi in cui manca la proprietà dell'intestazione di autenticazione o di rifiuto da parte del back-end. La gestione specifica di questi casi dipende in larga misura dall'esperienza dell'utente di destinazione. Una delle opzioni consiste nel visualizzare una notifica con un prompt generico affinché l'utente possa autenticarsi per recuperare la notifica effettiva.

## Esecuzione dell'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzioneranno nel simulatore).

2. Nell'interfaccia utente dell'app per iOS immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

3. Nell'interfaccia utente dell'app per iOS fare clic su **Log in**. Fare clic su **Send push**. La notifica sicura verrà visualizzata nel Notification Center.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png

<!---HONumber=AcomDC_0907_2016-->