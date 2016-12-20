---
title: Integrazione di Android SDK per Azure Mobile Engagement
description: Descrive come integrare l&quot;SDK di Azure Mobile Engagement nelle app Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integrazione di Android SDK per Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows universale](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Questo documento descrive tutte le opzioni di configurazione e integrazione disponibili per Android SDK per Azure Mobile Engagement.

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Funzionalità avanzate
### <a name="reporting-features"></a>Funzionalità di segnalazione
È possibile aggiungere queste funzionalità:

1. [Opzioni di segnalazione avanzata](mobile-engagement-android-advanced-reporting.md)
2. [Opzioni di segnalazione della posizione](mobile-engagement-android-location-reporting.md)
3. [Opzioni di configurazione avanzate](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notifiche:
[Come integrare la copertura (notifiche) nell'app per Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [Come integrare GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [Come integrare ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementazione del piano di tag:
[Come usare l'API di assegnazione di tag di Mobile Engagement avanzata nell'app per Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Note sulla versione
### <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Rimozione del blocco Wi-Fi.
* Correzione di un deadlock quando si chiama getDeviceId prima di init (bug introdotto nella versione 4.2.0).

Per tutte le versioni, vedere le [note sulla versione complete](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, vedere le [procedure di aggiornamento](mobile-engagement-android-upgrade-procedure.md).




<!--HONumber=Nov16_HO3-->


