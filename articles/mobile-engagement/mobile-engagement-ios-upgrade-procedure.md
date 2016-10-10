<properties
	pageTitle="Procedure di aggiornamento di Azure Mobile Engagement SDK per iOS | Microsoft Azure"
	description="Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

##Dalla versione 3.0.0 alla 4.0.0

### XCode 8
XCode 8 è un componente obbligatorio a partire dalla versione 4.0.0 dell'SDK.

> [AZURE.NOTE] Se si dipende davvero da XCode 7, è possibile usare [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Esiste un bug noto nel modulo di copertura della versione precedente durante l'esecuzione su dispositivi iOS 10: le notifiche di sistema non vengono messe in atto. Per risolvere il problema è necessario implementare l'API deprecata `application:didReceiveRemoteNotification:` nel delegato dell'app come segue:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Questa soluzione non è consigliata** dal momento che tale comportamento può cambiare in qualsiasi aggiornamento della versione iOS imminente (anche minore), poiché questa API iOS è deprecata. È opportuno passare a XCode 8 il prima possibile.

### Framework di UserNotifications
È necessario aggiungere il framework di `UserNotifications` nelle fasi di compilazione.

Nell'area di esplorazione dei progetti aprire il riquadro del progetto, quindi selezionare la destinazione corretta. Aprire quindi la scheda **"Build phases"** e nel menu **"Link Binary With Libraries"** aggiungere il framework `UserNotifications.framework`, quindi impostare il link come `Optional`

### Funzionalità di push dell'applicazione
XCode 8 può ripristinare la funzionalità di push dell'app; controllarla attentamente nella scheda `capability` della finestra di destinazione selezionata.

### Aggiungere il nuovo codice di registrazione delle notifiche iOS 10
Il frammento di codice precedente per registrare l'app per le notifiche funziona ancora, ma usa API deprecate pur essendo eseguito in iOS 10.

Importare il framework `User Notification`:

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

### Se si dispone già di un'implementazione di UNUserNotificationCenterDelegate:

L'SDK ha anche la propria implementazione del protocollo UNUserNotificationCenterDelegate. Viene utilizzata dall'SDK per monitorare il ciclo di vita delle notifiche di Engagement sui dispositivi che eseguono iOS 10 o versioni successive. Se l'SDK rileva il delegato non utilizza la sua implementazione poiché può esistere un solo delegato UNUserNotificationCenter per applicazione. Ciò significa che è necessario aggiungere la logica di Engagement al proprio delegato.

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

> [AZURE.NOTE] È possibile determinare se una notifica proviene o meno da Engagement passando il suo dizionario `userInfo` al metodo della classe dell'agente `isEngagementPushPayload:`.

##Dalla versione 2.0.0 alla 3.0.0
Eliminazione del supporto per iOS 4.X. A partire da questa versione, la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

Se si usa Reach nell'applicazione, è necessario aggiungere il valore `remote-notification` alla matrice `UIBackgroundModes` nel file Info.plist per ricevere notifiche remote.

Il metodo `application:didReceiveRemoteNotification:` deve essere sostituito da `application:didReceiveRemoteNotification:fetchCompletionHandler:` nel delegato dell'applicazione.

"AEPushDelegate.h" è un'interfaccia deprecata ed è necessario rimuovere tutti i riferimenti. Ciò include la rimozione di `[[EngagementAgent shared] setPushDelegate:self]` e dei metodi delegati dal delegato dell'applicazione:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##Dalla versione 1.16.0 alla 2.0.0
La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.16 e quindi applicare la procedura seguente.

>[AZURE.IMPORTANT] Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement

### Agente

Il metodo `registerApp:` è stato sostituito dal nuovo metodo `init:`. È necessario aggiornare il delegato dell'applicazione di conseguenza e usare la stringa di connessione:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Se il rilevamento di SmartAd è stato rimosso dall'SDK, è sufficiente rimuovere tutte le istanze della classe `AETrackModule`.

### Modifiche del nome della classe

Durante la procedura di rebranding, è necessario modificare alcuni nomi di classi/file.

A tutte le classi con prefisso "CP" viene assegnato quello "AE".

Esempio:

-   `CPModule.h` viene rinominata come `AEModule.h`.

Tutte le classi con prefisso "Capptain" vengono rinominate con il prefisso "Engagement".

Esempi:

-   La classe `CapptainAgent` viene rinominata come `EngagementAgent`.
-   La classe `CapptainTableViewController` viene rinominata come `EngagementTableViewController`.
-   La classe `CapptainUtils` viene rinominata come `EngagementUtils`.
-   La classe `CapptainViewController` viene rinominata come `EngagementViewController`.

<!---HONumber=AcomDC_0928_2016-->