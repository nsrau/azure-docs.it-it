<properties
	pageTitle="Integrazione di Windows Universal SDK"
	description="Integrazione di Windows Universal per SDK per Azure Mobile Engagement" 									
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />  

#Integrazione di Windows Universal SDK per Azure Mobile Engagement

Questo documento descrive tutte le opzioni di configurazione e integrazione disponibili per Windows Universal SDK per Azure Mobile Engagement.

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario completare l'[esercitazione di 15 minuti](mobile-engagement-windows-store-dotnet-get-started.md).

## Funzionalità avanzate

### Funzionalità di segnalazione
È possibile aggiungere queste funzionalità:

1. [Opzioni di segnalazione avanzata](mobile-engagement-windows-store-advanced-reporting.md)

2. [Opzioni di configurazione avanzate](mobile-engagement-windows-store-advanced-configuration.md)

### Notifiche

[Come integrare il servizio di copertura (di notifica) in un'app universale di Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### Implementazione del piano di tag:

[Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md)

##Note sulla versione

###3\.4.0 (19/04/2016)

-   La sovrimpressione Reach è stata migliorata.
-   Aggiunta dell'API "TestLogLevel" per abilitare/disabilitare/filtrare i log della console generati da SDK.
-   Correzione delle notifiche in attività per la prima attività non visualizzata all'avvio dell'applicazione

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-windows-store-release-notes.md)

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate diverse versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md) complete. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

###Dalla versione 3.3.0 alla 3.4.0

####Log di test

I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per personalizzare, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

####Risorse

La sovrimpressione Reach è stata migliorata. Fa parte delle risorse del pacchetto NuGet di SDK.

Durante l'aggiornamento alla nuova versione di SDK, è possibile scegliere se mantenere i file esistenti contenuti nella cartella della sovrimpressione delle risorse:

* Se la sovrimpressione precedente è in funzione o si stanno integrando manualmente gli elementi `WebView`, è possibile decidere di mantenere i file esistenti per poter proseguire.
* Per passare alla sovrimpressione nuova, sostituire l'intera cartella `overlay` delle risorse con quella nuova disponibile nel pacchetto SDK. Dopo aver completo l'aggiornamento, nelle app UWP è possibile ottenere la cartella della nuova sovrimpressione da %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources.

> [AZURE.WARNING] Se si usa la sovrimpressione nuova, le personalizzazioni eseguite con la versione precedente saranno sovrascritte.

### Eseguire l'aggiornamento da versioni precedenti

Vedere [Procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md).

<!---HONumber=AcomDC_0817_2016-->