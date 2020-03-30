---
title: Agenti di autorizzazione e come abilitarli Azure
description: Informazioni sui diversi agenti di autorizzazione che Microsoft Authentication Library (MSAL) consente all'app Android di usare e su come abilitarli.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085327"
---
# <a name="authorization-agents-android"></a>Agenti di autorizzazione (Android)

Questo articolo descrive i diversi agenti di autorizzazione che la libreria di autenticazione Microsoft (MSAL) consente all'app di usare e come abilitarli.

La scelta di una strategia specifica per gli agenti di autorizzazione è facoltativa e rappresenta funzionalità aggiuntive che le app possono personalizzare. La maggior parte delle app utilizzerà le impostazioni predefinite di MSAL (vedere Informazioni sul file di [configurazione di Android MSAL](msal-configuration.md) per visualizzare le varie impostazioni predefinite).

MSAL supporta l'autorizzazione utilizzando un `WebView`o il browser di sistema.  L'immagine seguente mostra l'aspetto utilizzando il `WebView`browser di sistema con CustomTabs o senza CustomTabs:

![Esempi di accesso MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicazioni dell'accesso singolo

Per impostazione predefinita, le applicazioni integrate con MSAL utilizzano le schede personalizzate del browser di sistema per autorizzare. A differenza di WebViews, le schede personalizzate condividono un cookie jar con il browser di sistema predefinito consentendo meno invii con web o altre app native che si sono integrate con le schede personalizzate.

Se l'applicazione `WebView` usa una strategia senza integrare il supporto di Microsoft Authenticator o Portale aziendale nell'app, gli utenti non avranno un'esperienza Single Sign-On (SSO) nel dispositivo o tra app native e app Web.

Se l'applicazione utilizza MSAL con il supporto di Microsoft Authenticator o Portale aziendale, gli utenti possono avere un'esperienza SSO tra le applicazioni se l'utente ha un accesso attivo con una delle app.

## <a name="webview"></a>WebView

Per usare WebView in-app, inserire la riga seguente nel codice JSON di configurazione dell'app passato a MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Quando usi in-app `WebView`, l'utente accede direttamente all'app. I token vengono mantenuti all'interno della sandbox dell'app e non sono disponibili all'esterno del jar dei cookie dell'app. Di conseguenza, l'utente non può avere un'esperienza SSO tra le applicazioni a meno che le applicazioni non si integrino con l'autenticatore o il portale aziendale.

Tuttavia, `WebView` offre la possibilità di personalizzare l'aspetto dell'interfaccia utente di accesso. Per altre informazioni su come eseguire questa personalizzazione, vedere [Visualizzazioni Web Android.See Android WebViews](https://developer.android.com/reference/android/webkit/WebView) for more about how to do this customization.

## <a name="default-browser-plus-custom-tabs"></a>Browser predefinito più schede personalizzate

Per impostazione predefinita, MSAL utilizza il browser e una strategia [di schede personalizzate.](https://developer.chrome.com/multidevice/android/customtabs) È possibile indicare in modo esplicito `DEFAULT` questa strategia per impedire le modifiche nelle versioni future usando la seguente configurazione JSON nel file di configurazione personalizzato:You can explicitly indicate this strategy to prevent changes in future releases to using the following JSON configuration in the custom configuration file:

```json
"authorization_user_agent" : "BROWSER"
```

Utilizzare questo approccio per fornire un'esperienza SSO tramite il browser del dispositivo. MSAL utilizza un file jar di cookie condiviso, che consente ad altre app native o app Web di ottenere SSO nel dispositivo usando il cookie di sessione persistente impostato da MSAL.

## <a name="browser-selection-heuristic"></a>Euristica di selezione del browser

Poiché è impossibile per MSAL specificare il pacchetto esatto del browser da utilizzare su ciascuno dei diversi tipi di telefoni Android, MSAL implementa un'euristica di selezione del browser che cerca di fornire il miglior SSO tra dispositivi.

MSAL recupera l'elenco completo dei browser installati sul dispositivo per selezionare il browser da utilizzare. L'elenco è nell'ordine restituito dal gestore di pacchetti, che riflette indirettamente le preferenze dell'utente. Ad esempio, il browser predefinito, se impostato, è la prima voce dell'elenco. Il _primo_ browser nell'elenco verrà scelto indipendentemente dal fatto che supporti le schede personalizzate. Se il browser supporta le schede personalizzate, MSAL avvierà la scheda personalizzata. `WebView` Le schede personalizzate hanno un aspetto più vicino a un'app e consentiranno la personalizzazione di base dell'interfaccia utente. Per altre informazioni, vedere [Schede personalizzate in Android.See Custom Tabs in Android](https://developer.chrome.com/multidevice/android/customtabs) to learn more.

Se nel dispositivo non sono presenti pacchetti browser, `WebView`MSAL utilizza l'opzione in-app .

L'ordine dei browser nell'elenco dei browser è determinato dal sistema operativo. È in ordine dal più preferito al meno. Se l'impostazione predefinita del dispositivo non viene modificata, lo stesso browser deve essere avviato per ogni accesso per garantire un'esperienza SSO.

> [!NOTE]
> MSAL non preferisce più sempre Chrome se un altro browser è impostato come predefinito. Ad esempio, su un dispositivo che ha sia Chrome che un altro browser preinstallato, MSAL utilizzerà il browser che l'utente ha impostato come predefinito.

### <a name="tested-browsers"></a>Browser testati

I seguenti browser sono stati testati per vedere `"redirect_uri"` se reindirizzano correttamente al specificato nel file di configurazione:

| | Browser integrato | Chrome | Opera  | Microsoft Edge | UC Browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |non applicabile |non applicabile |non applicabile |non applicabile |
| Samsung S7 (API 25) | pass ( : | pass | pass | pass | fail |pass |
| Huawei (API 26) |pass. | pass | fail | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|fail|
| Pixel 2 (API 26) |pass | pass | pass | pass | fail |pass |
| Oppo | pass | NON applicabile |non applicabile  |non applicabile |non applicabile | non applicabile|
| OnePlus (API 25) |pass | pass | pass | pass | fail |pass |
| Nexus (API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

Il browser integrato di Samsung è Samsung Internet.  
Il browser integrato di Huawei è Huawei Browser.  
Il browser predefinito non può essere modificato all'interno dell'impostazione del dispositivo Oppo.
