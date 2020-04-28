---
title: Agenti di autorizzazione e modalità di abilitazione | Azure
description: Informazioni sui diversi agenti di autorizzazione che Microsoft Authentication Library (MSAL) consente all'app Android di usare e come abilitarli.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77085327"
---
# <a name="authorization-agents-android"></a>Agenti di autorizzazione (Android)

Questo articolo descrive i diversi agenti di autorizzazione che Microsoft Authentication Library (MSAL) consente all'app di usare e come abilitarli.

La scelta di una strategia specifica per gli agenti di autorizzazione è facoltativa e rappresenta altre app di funzionalità che possono essere personalizzate. Per la maggior parte delle app vengono usate le impostazioni predefinite di MSAL (vedere [informazioni sul file di configurazione di Android MSAL](msal-configuration.md) per visualizzare le diverse impostazioni predefinite).

MSAL supporta l'autorizzazione utilizzando `WebView`un o il browser del sistema.  Nell'immagine seguente viene illustrato il modo in cui `WebView`viene utilizzato il o il browser di sistema con CustomTabs o senza CustomTabs:

![Esempi di accesso MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicazioni di Single Sign-on

Per impostazione predefinita, le applicazioni integrate con MSAL utilizzano le schede personalizzate del browser del sistema per l'autorizzazione. A differenza delle visualizzazioni Web, le schede personalizzate condividono un file jar dei cookie con il browser di sistema predefinito che consente un minor numero di accessi con le app Web o altre app native integrate con schede personalizzate.

Se l'applicazione usa una `WebView` strategia senza integrare Microsoft Authenticator o portale aziendale supporto nell'app, gli utenti non avranno un'esperienza Single Sign-on (SSO) sul dispositivo o tra app native e app Web.

Se l'applicazione usa MSAL con supporto Microsoft Authenticator o Portale aziendale, gli utenti possono avere un'esperienza SSO tra le applicazioni se l'utente dispone di un accesso attivo con una delle app.

## <a name="webview"></a>WebView

Per usare la visualizzazione WebView in-app, inserire la riga seguente nel file JSON di configurazione dell'app che viene passato a MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Quando si usa in-app `WebView`, l'utente accede direttamente all'app. I token vengono conservati all'interno della sandbox dell'app e non sono disponibili al di fuori del file jar dei cookie dell'app. Di conseguenza, l'utente non può avere un'esperienza SSO tra le applicazioni, a meno che le app non si integrino con l'autenticatore o Portale aziendale.

Tuttavia, `WebView` offre la possibilità di personalizzare l'aspetto dell'interfaccia utente di accesso. Per altre informazioni su come eseguire questa personalizzazione, vedere [Webviews Android](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Browser predefinito più schede personalizzate

Per impostazione predefinita, MSAL usa il browser e una strategia personalizzata per le [schede](https://developer.chrome.com/multidevice/android/customtabs) . È possibile indicare in modo esplicito questa strategia per impedire modifiche nelle versioni `DEFAULT` future a usando la configurazione JSON seguente nel file di configurazione personalizzato:

```json
"authorization_user_agent" : "BROWSER"
```

Usare questo approccio per offrire un'esperienza SSO tramite il browser del dispositivo. MSAL usa un file jar di cookie condiviso, che consente ad altre app native o app Web di ottenere l'accesso SSO sul dispositivo usando il cookie di sessione di salvataggio permanente impostato da MSAL.

## <a name="browser-selection-heuristic"></a>Euristica selezione del browser

Poiché è impossibile per MSAL specificare il pacchetto del browser esatto da usare in ogni ampia gamma di telefoni Android, MSAL implementa un'euristica di selezione del browser che tenta di fornire il migliore SSO tra dispositivi.

MSAL recupera l'elenco completo dei browser installati sul dispositivo per selezionare il browser da usare. L'elenco è nell'ordine restituito da Gestione pacchetti, che riflette indirettamente le preferenze dell'utente. Ad esempio, il browser predefinito, se impostato, è la prima voce nell'elenco. Il _primo_ browser nell'elenco verrà scelto indipendentemente dal fatto che supporti schede personalizzate. Se il browser supporta le schede personalizzate, MSAL avvierà la scheda personalizzata. le schede personalizzate hanno un aspetto più vicino a un in-app `WebView` e consentono la personalizzazione dell'interfaccia utente di base. Per altre informazioni, vedere [schede personalizzate in Android](https://developer.chrome.com/multidevice/android/customtabs) .

Se non sono presenti pacchetti del browser nel dispositivo, MSAL usa il in-app `WebView`.

L'ordine dei browser nell'elenco del browser è determinato dal sistema operativo. Si tratta di un ordine dal più preferenziale al minimo. Se l'impostazione predefinita del dispositivo non viene modificata, è necessario avviare lo stesso browser per ogni accesso per garantire un'esperienza SSO.

> [!NOTE]
> MSAL non sempre preferisce Chrome se un altro browser è impostato come predefinito. Ad esempio, in un dispositivo che dispone sia di Chrome che di un altro browser pre-installato, MSAL utilizzerà il browser che l'utente ha impostato come predefinito.

### <a name="tested-browsers"></a>Browser testati

I browser seguenti sono stati testati per verificare se il reindirizzamento a `"redirect_uri"` specificato nel file di configurazione è stato eseguito correttamente:

| | Browser incorporato | Chrome | Opera  | Microsoft Edge | Browser UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |non applicabile |non applicabile |non applicabile |non applicabile |
| Samsung S7 (API 25) | passare | pass | pass | pass | fail |pass |
| Huawei (API 26) |passa * * | pass | fail | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|fail|
| Pixel 2 (API 26) |pass | pass | pass | pass | fail |pass |
| OPPO | pass | non applicabile * * * |non applicabile  |non applicabile |non applicabile | non applicabile|
| OnePlus (API 25) |pass | pass | pass | pass | fail |pass |
| Nexus (API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

* Il browser predefinito di Samsung è Samsung Internet.  
* * Il browser incorporato di Huawei è il browser Huawei.  
Non è possibile modificare il browser predefinito all'interno dell'impostazione del dispositivo oppo.
