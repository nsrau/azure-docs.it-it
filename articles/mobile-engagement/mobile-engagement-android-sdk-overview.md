---
title: Integrazione di Android SDK per Azure Mobile Engagement
description: Descrive come integrare l'SDK di Azure Mobile Engagement nelle app Android
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
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
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

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Correzione di un arresto anomalo del sistema che può verificarsi raramente quando si chiama `EngagementAgentUtils.isInDedicatedEngagementProcess`, che viene usato anche dalla classe `EngagementApplication`.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Supporto Android 8 (versioni precedenti dell'SDK non funzionano in Android 8).
* Nessuna dipendenza nella raccolta di supporto.
* Rimuovere la classe `EngagementFragmentActivity`.
* A causa di [limiti di esecuzione in Background](https://developer.android.com/preview/features/background.html) in Android 8, i log in background potrebbero subire un ritardo fino a quando l'utente non interagisce con il dispositivo; ciò avrà un impatto sulla campagna Push **Consegnata** e sulle statistiche **Notifica di sistema visualizzata** che subiscono un ritardo se il dispositivo era in sospensione (la notifica verrà ancora visualizzata, suonerà e vibrerà in tempo reale senza problemi).
* A causa di [limiti del percorso di background](https://developer.android.com/preview/features/background-location-limits.html), il percorso in tempo reale in background non verrà aggiornato frequentemente in Android 8.

Per tutte le versioni, vedere le [note sulla versione complete](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, vedere le [procedure di aggiornamento](mobile-engagement-android-upgrade-procedure.md).

