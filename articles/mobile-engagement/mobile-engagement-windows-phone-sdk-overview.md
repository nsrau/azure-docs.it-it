<properties 
	pageTitle="Panoramica dell'SDK per Windows Phone Silverlight" 
	description="Panoramica di Azure Mobile Engagement SDK per Windows Phone Silverlight" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Panoramica di Azure Mobile Engagement SDK per Windows Phone Silverlight

Iniziare da qui per ottenere i dettagli su come integrare Azure Mobile Engagement in un'app per Windows Phone Silverlight. Se si vuole fare prima una prova, completare l'[esercitazione di 15 minuti](mobile-engagement-windows-phone-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-windows-phone-sdk-content.md).

##Procedure di integrazione

1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio Reach (di notifica) in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##Note sulla versione

###3\.3.0 (19/04/2016)
Parte del pacchetto NuGet *MicrosoftAzure.MobileEngagement* **v3.4.0**

-   Aggiunta dell'API "TestLogLevel" per abilitare/disabilitare/filtrare i log della console generati da SDK.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-windows-phone-release-notes.md).

##Procedure di aggiornamento

Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure.md) complete. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

###Dalla versione 2.0.0 alla 3.3.0

####Log di test

I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

### Eseguire l'aggiornamento da versioni precedenti

Vedere [Procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure.md).
 

<!---HONumber=AcomDC_0824_2016-->