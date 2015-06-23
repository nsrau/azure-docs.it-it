<properties 
	pageTitle="Procedura di aggiornamento di Azure Mobile Engagement SDK per iOS" 
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per iOS"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

##Dalla versione 1.16.0 alla 2.0.0
La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.16 e quindi applicare la procedura seguente.

>[Azure.IMPORTANT]Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement

### Agente

Il metodo `registerApp:` è stato sostituito dal nuovo metodo `init:`. È necessario aggiornare il delegato dell'applicazione di conseguenza e utilizzare la stringa di connessione:

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

<!--HONumber=54--> 