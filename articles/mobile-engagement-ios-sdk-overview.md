<properties 
	pageTitle="Panoramica dell'SDK iOS di Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure relativi all'SDK iOS di Azure Mobile Engagement"
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

#2.0.0

Da qui, è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app iOS. Se si desidera eseguire una prova preliminare, completare il [tutorial di 15 minuti](mobile-engagement-ios-get-started.md).

Fare clic per visualizzare il [contenuto SDK](mobile-engagement-ios-sdk-content.md)

##Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement nella propria app iOS](mobile-engagement-ios-integrate-engagement.md)

2. Per quanto riguarda le notifiche: [Come integrare Reach (notifiche) nella propria app iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementazione piano tag: [Come utilizzare l'API avanzata di assegnazione tag di Mobile Engagement nella propria app iOS](mobile-engagement-ios-use-engagement-api.md)


##Note sulla versione

###2.0.0 (17/02/2015)

- Versione iniziale di Azure Mobile Engagement
- La configurazione di appId/sdkKey viene sostituita da una configurazione della stringa di connessione.
- L'API non invia e non riceve più messaggi XMPP da entità XMPP arbitrarie.
- L'API non riceve e non invia più messaggi tra i dispositivi.
- Miglioramenti della protezione.
- Eliminato il rilevamento di SmartAd.

Per la versione precedente, vedere le [note sulla versione complete](mobile-engagement-ios-release-notes.md)

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Potrebbe essere necessario effettuare più di una procedura, nel caso in cui l'utente abbia ignorato alcune versioni dell'SDK. A tal proposito, consultare le [procedure di aggiornamento] complete.(mobile-engagement-ios-upgrade-procedure.md).

Per ogni nuova versione dell'SDK è necessario innanzitutto sostituire (rimuovere e importare nuovamente in Xcode) le cartelle EngagementSDK ed EngagementReach.

###Dalla versione 1.16.0 a quella 2.0.0
Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio Capptain fornito da Capptain SAS in un'app con tecnologia Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain e Mobile Engagement non sono servizi uguali e la procedura riportata di seguito indica solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON non trasferirà i dati dai server Capptain ai server Mobile Engagement

Se si esegue la migrazione da una versione precedente, consultare il sito Web di Capptain per eseguire innanzitutto la migrazione alla versione 1.16. In seguito, eseguire la procedura seguente.

#### Agente

Il metodo `registerApp:` è stato sostituito da quello "init:" più recente. È necessario aggiornare il delegato dell'applicazione di conseguenza e utilizzare la stringa di connessione:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			 [...]
			 [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			 [...]
			}

Se il rilevamento di SmartAd è stato rimosso dall'SDK, è sufficiente rimuovere tutte le istanze della classe `AETrackModule`

#### Modifiche del nome della classe

Durante la procedura di rebranding, è necessario modificare alcuni nomi di classi/file.

A tutte le classi con prefisso "CP" viene assegnato quello "AE".

Esempio:

- `CPModule.h` viene rinominato come `AEModule.h`.

Tutte le classi con prefisso "Capptain" vengono rinominate con il prefisso "Engagement".

Esempi:

- La classe `CapptainAgent` viene rinominata come `EngagementAgent`.
- La classe `CapptainTableViewController` viene rinominata come `EngagementTableViewController`.
- La classe `CapptainUtils` viene rinominata come `EngagementUtils`.
- La classe `CapptainViewController` viene rinominata come `EngagementViewController`.

<!--HONumber=47-->
