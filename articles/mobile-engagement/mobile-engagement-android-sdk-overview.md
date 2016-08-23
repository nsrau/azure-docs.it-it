<properties
	pageTitle="Integrazione di Android SDK per Azure Mobile Engagement"
	description="Descrive come integrare l'SDK di Azure Mobile Engagement nelle app Android"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="piyushjo;ricksal" />  

# Integrazione di Android SDK per Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

Questo documento descrive tutte le opzioni di configurazione e integrazione disponibili per Android SDK per Azure Mobile Engagement.

## Prerequisiti

[AZURE.INCLUDE [Prerequisiti](../../includes/mobile-engagement-android-prereqs.md)]

## Funzionalità avanzate

### Funzionalità di segnalazione

È possibile aggiungere queste funzionalità:

1. [Opzioni di segnalazione avanzata](mobile-engagement-android-advanced-reporting.md)
2. [Opzioni di segnalazione della posizione](mobile-engagement-android-location-reporting.md)
3. [Opzioni di configurazione avanzate](mobile-engagement-android-advanced-configuration.md)

### Notifiche:
[Come integrare la copertura (notifiche) nell'app per Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [Come integrare GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)

2. Amazon Device Messaging (ADM): [Come integrare ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### Implementazione del piano di tag:
[Come usare l'API di assegnazione di tag di Mobile Engagement avanzata nell'app per Android](mobile-engagement-android-use-engagement-api.md)

## Note sulla versione

### 4\.2.3 (08/10/2016)

 - Rimozione del blocco Wi-Fi.
 - Correzione di un deadlock quando si chiama getDeviceId prima di init (bug introdotto nella versione 4.2.0).

Per tutte le versioni, vedere le [note sulla versione complete](mobile-engagement-android-release-notes.md).

## Procedure di aggiornamento

Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, consultare [Procedure di aggiornamento](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0810_2016-->