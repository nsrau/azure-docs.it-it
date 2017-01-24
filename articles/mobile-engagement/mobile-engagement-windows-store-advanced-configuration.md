---
title: Configurazione avanzata per Engagement SDK per app universali di Windows
description: Opzioni di configurazione avanzata per Azure Mobile Engagement per app universali di Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877


---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configurazione avanzata per Engagement SDK per app universali di Windows
> [!div class="op_single_selector"]
> * [Windows universale](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

Questa procedura descrive come configurare le diverse opzioni di configurazione per le app Android di Azure Mobile Engagement.

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Configurazione avanzata
### <a name="disable-automatic-crash-reporting"></a>Disabilitare la segnalazione automatica degli arresti anomali
È possibile disabilitare la funzionalità di segnalazione automatica degli arresti anomali di Engagement. Quando si verifica un'eccezione non gestita, Engagement non eseguirà alcuna azione.

> [!WARNING]
> Se si disabilita questa funzionalità, quando si verifica un arresto anomalo non gestito nell'app, Engagement non lo invierà **E** non chiuderà la sessione e i processi.
> 
> 

Per disabilitare la segnalazione automatica degli arresti anomali, personalizzare la configurazione in base al modo in cui che è stata dichiarata:

#### <a name="from-engagementconfigurationxml-file"></a>Dal file `EngagementConfiguration.xml`
Impostare la segnalazione degli arresti anomali su `false` tra i tag `<reportCrash>` e `</reportCrash>`.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Dall'oggetto `EngagementConfiguration` in fase di esecuzione
Impostare la segnalazione degli arresti anomali su false usando l'oggetto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Disabilitare la segnalazione in tempo reale
Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala spesso i log, è preferibile memorizzare i log nel buffer e segnalarli tutti insieme con cadenza regolare, la cosiddetta "modalità burst".

A tale scopo, chiamare il metodo:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. Ogni volta che si vuole riattivare la registrazione in tempo reale, chiamare il metodo senza alcun parametro o con il valore 0.

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi viene arrotondata alla soglia di burst e, di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili. È consigliabile usare una soglia di burst non maggiore di 30000, ovvero 30 secondi. Per i log salvati è previsto un limite di 300 elementi. Se l'invio richiede troppo tempo, è possibile che alcuni log vadano persi.

> [!WARNING]
> La soglia di burst non può essere configurata per un periodo inferiore a un secondo. Se si imposta un valore minore, l'SDK mostrerà una traccia con l'errore e verrà reimpostato automaticamente sul valore predefinito, vale a dire, zero secondi. In questo modo si attiva l'SDK per la segnalazione dei log in tempo reale.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview



<!--HONumber=Dec16_HO2-->


