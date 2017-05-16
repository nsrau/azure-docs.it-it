---
title: Integrazione di Android SDK per Azure Mobile Engagement
description: Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 03/30/2017
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e50c1c70a4378560f8ce3182069cba4832fa4d22
ms.lasthandoff: 03/31/2017


---
# <a name="release-notes"></a>Note sulla versione

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Correggere i colori del testo della notifica in-app in Android 7 in modo che corrispondano a quelli di versioni precedenti di Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Rimozione del blocco Wi-Fi.
* Correzione di un deadlock quando si chiama getDeviceId prima di init (bug introdotto nella versione 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Miglioramenti della stabilità.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Sicurezza: disabilitare l'accesso al file locale di visualizzazione Web.
* Sicurezza: rimozione della classe `EngagementPreferenceActivity` che estende la classe `PreferenceActivity` obsoleta e non sicura.
* Sicurezza: le attività del servizio di copertura vengono ora documentate per l'uso di `exported="false"`. Questo flag può essere usato anche nelle versioni precedenti dell'SDK.

## <a name="420-03112016"></a>4.2.0 (11/03/2016)
* L'SDK ora è concesso in licenza con MIT.
* Consentire la specifica di un identificatore del dispositivo personalizzato in fase di inizializzazione dell'SDK.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)
* Miglioramenti della stabilità.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)
* Miglioramenti della stabilità.

## <a name="413-1292015"></a>4.1.3 (9/12/2015)
* Miglioramenti della stabilità.

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* Miglioramenti della stabilità.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Miglioramenti della stabilità.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* Gestione di un nuovo modello di autorizzazione per Android M.
* Ora è possibile configurare le funzionalità di posizione in fase di esecuzione anziché usare `AndroidManifest.xml`.
* Correzione di un bug delle autorizzazioni: se si utilizza `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` non è più necessario.
* Miglioramenti della stabilità.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)
* Modifiche al protocollo interno per rendere più affidabili le analisi e il push.
* Il push nativo (GCM/ADM) viene ora usato anche per le notifiche in-app. È quindi necessario configurare le credenziali del push nativo per qualsiasi tipo di campagna push.
* Correzione della notifica generale: elementi visualizzati solo 10 secondi dopo il push.
* Correzione di un bug nella visualizzazione Web: facendo clic su un collegamento durante si eseguiva anche l'URL di azione predefinito.
* Correzione di un arresto anomalo raro correlato alla gestione dell'archivio locale.
* Correzione della gestione delle stringhe di configurazione dinamiche.
* Aggiornamento del contratto di licenza.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)
* Versione iniziale di Azure Mobile Engagement
* La configurazione di appId è stata sostituita con la configurazione della stringa di connessione.
* L'API non invia e non riceve più messaggi XMPP da entità XMPP arbitrarie.
* L'API non riceve e non invia più messaggi tra i dispositivi.
* Sono stati introdotti miglioramenti per la sicurezza.
* È stata rimossa la verifica per Google Play e SmartAd.


