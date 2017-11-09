---
title: Configurazione avanzata di Android SDK per Azure Mobile Engagement
description: Descrive le opzioni di configurazione avanzate tra cui il manifesto Android con Android SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configurazione avanzata di Android SDK per Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows universale](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Questa procedura descrive come configurare le diverse opzioni di configurazione per le app Android di Azure Mobile Engagement.

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisiti di autorizzazione
Alcune opzioni richiedono autorizzazioni specifiche, ognuna delle quali viene elencata di seguito a titolo di riferimento e inline nella funzionalità specifica. Aggiungere queste autorizzazioni al file AndroidManifest.xml del progetto immediatamente prima o dopo il tag `<application>`.

Il codice di autorizzazione deve essere simile al seguente. L'autorizzazione appropriata viene compilata in base alla tabella seguente.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Autorizzazione | Quando si usa |
| --- | --- |
| INTERNET |Obbligatorio. Per report di base |
| ACCESS_NETWORK_STATE |Obbligatorio. Per report di base |
| RECEIVE_BOOT_COMPLETED |Obbligatorio. Per visualizzare il centro notifiche dopo il riavvio del dispositivo |
| WAKE_LOCK |Consigliato. Abilita la raccolta dei dati quando si usa il WiFi o quando lo schermo è spento |
| VIBRATE |Facoltativo. Abilita la vibrazione alla ricezione delle notifiche |
| DOWNLOAD_WITHOUT_NOTIFICATION |Facoltativo. Abilita la notifica generale di Android |
| WRITE_EXTERNAL_STORAGE |Facoltativo. Abilita la notifica generale di Android |
| ACCESS_COARSE_LOCATION |Facoltativo. Abilita la segnalazione della posizione in tempo reale |
| ACCESS_FINE_LOCATION |Facoltativo. Abilita la segnalazione della posizione basata su GPS |

A partire da Android M, [alcune autorizzazioni vengono gestite in fase di esecuzione](mobile-engagement-android-location-reporting.md#android-m-permissions).

Se si usa già ``ACCESS_FINE_LOCATION`` non è necessario usare anche ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opzioni di configurazione del file manifesto Android
### <a name="crash-report"></a>Segnalazione di arresto anomalo 
Per disabilitare la segnalazione di arresti anomali del sistema, aggiungere questo codice tra i tag `<application>` e `</application>`:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Soglia del burst
Per impostazione predefinita, il servizio Engagement segnala i log in tempo reale. Se l'applicazione segnala i log molto spesso, è preferibile memorizzare i log nel buffer e segnalarli tutti insieme con cadenza regolare, la cosiddetta "modalità burst". A tale scopo, aggiungere questo codice tra i tag `<application>` e `</application>`:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

La modalità burst aumenta lievemente la durata della batteria ma ha un impatto su Monitor di Engagement: la durata di tutte le sessioni e di tutti i processi viene arrotondata alla soglia di burst e, di conseguenza, le sessioni e i processi inferiori alla soglia di burst potrebbero non essere visibili. La soglia di burst non dovrebbe essere maggiore di 30000 (30 secondi).

### <a name="session-timeout"></a>Timeout della sessione
 È possibile terminare un'attività premendo **Home** o **Indietro**, impostando l'inattività del telefono o passando a un'altra applicazione. Per impostazione predefinita, una sessione viene terminata 10 secondi dopo la fine dell'ultima attività. Ciò avviene per evitare una divisione di sessione ogni volta che l'utente esce e rientra nell'applicazione rapidamente, come può accadere quando preleva un'immagine, controlla una notifica e così via. Questo parametro può essere modificato. A tale scopo, aggiungere questo codice tra i tag `<application>` e `</application>`:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Disabilitare la segnalazione di log
### <a name="using-a-method-call"></a>Uso di una chiamata del metodo
Se si vuole che Engagement non invii più log, è possibile chiamare:

    EngagementAgent.getInstance(context).setEnabled(false);

Questa chiamata è persistente: usa un file di preferenze condivise.

Se Engagement è attivo quando si chiama questa funzione, l'arresto del servizio può richiedere 1 minuto. Al successivo avvio dell'applicazione, tuttavia, il servizio non verrà avviato.

È possibile abilitare di nuovo la segnalazione di log chiamando la stessa funzione con `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrazione nella propria classe `PreferenceActivity`
Invece di chiamare questa funzione, è anche possibile integrare questa impostazione direttamente nella classe `PreferenceActivity` esistente.

È possibile configurare Engagement in modo da usare il file di preferenze (con la modalità desiderata) nel file `AndroidManifest.xml` con `application meta-data`:

* La chiave `engagement:agent:settings:name` viene usata per definire il nome del file di preferenze condivise.
* La chiave `engagement:agent:settings:mode` viene usata per definire la modalità del file di preferenze condivise. Usare la stessa modalità usata per `PreferenceActivity`. La modalità deve essere passata come numero: se si usa una combinazione di flag di costanti nel codice, controllare il valore totale.

Engagement usa sempre la chiave booleana `engagement:key` all'interno del file di preferenze per la gestione di questa impostazione.

L'esempio seguente di `AndroidManifest.xml` mostra i valori predefiniti:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Sarà quindi possibile aggiungere un elemento `CheckBoxPreference` nel layout delle preferenze simile a quello riportato di seguito:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
