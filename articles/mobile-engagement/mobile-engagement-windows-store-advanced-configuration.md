<properties
	pageTitle="Configurazione avanzata per Engagement SDK per app universali di Windows"
	description="Opzioni di configurazione avanzata per Azure Mobile Engagement per app universali di Windows" 					
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Configurazione avanzata per Engagement SDK per app universali di Windows

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advan.mdced-configuration.md)

Questa procedura descrive come configurare le diverse opzioni di configurazione per le app Android di Azure Mobile Engagement.

## Prerequisiti

[AZURE.INCLUDE [Prerequisiti](../../includes/mobile-engagement-windows-store-prereqs.md)]

##Configurazione avanzata

### Disabilitare la segnalazione automatica degli arresti anomali

È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quando si verifica un'eccezione non gestita, Engagement non eseguirà alcuna azione.

> [AZURE.WARNING] Se si disabilita questa funzionalità, quando si verifica un arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione in base al modo in cui che è stata dichiarata:

#### Dal file `EngagementConfiguration.xml`

Impostare la segnalazione degli arresti anomali su `false` tra i tag `<reportCrash>` e `</reportCrash>`.

#### Dall'oggetto `EngagementConfiguration` in fase di esecuzione

Impostare la segnalazione degli arresti anomali su false usando l'oggetto EngagementConfiguration.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Disabilitare la segnalazione in tempo reale

Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala spesso i log, è preferibile memorizzare i log nel buffer e segnalarli tutti insieme con cadenza regolare, la cosiddetta "modalità burst".

A tale scopo, chiamare il metodo:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. Ogni volta che si vuole riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi viene arrotondata alla soglia di burst e, di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili. È consigliabile usare una soglia di burst non maggiore di 30000, ovvero 30 secondi. Per i log salvati è previsto un limite di 300 elementi. Se l'invio richiede troppo tempo, è possibile che alcuni log vadano persi.

> [AZURE.WARNING] La soglia di burst non può essere configurata per un periodo inferiore a un secondo. Se si imposta un valore minore, l'SDK mostrerà una traccia con l'errore e verrà reimpostato automaticamente sul valore predefinito, vale a dire, zero secondi. In questo modo si attiva l'SDK per la segnalazione dei log in tempo reale.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview

<!---HONumber=AcomDC_0817_2016-->