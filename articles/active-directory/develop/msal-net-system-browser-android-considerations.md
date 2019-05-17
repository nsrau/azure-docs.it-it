---
title: Considerazioni di Xamarin. Android (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sulle considerazioni specifiche quando si usa Xamarin. Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544432"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerazioni specifiche di Xamarin Android con MSAL.NET
Questo articolo tratta alcuni aspetti specifici quando si usa il browser del sistema in Xamarin. Android con Microsoft Authentication Library per .NET (MSAL.NET).

A partire da MSAL.NET 2.4.0-preview, MSAL.NET supporta i browser diversi da Chrome e non richiede più Chrome essere installato nel dispositivo Android per l'autenticazione.

Si consiglia di che usare browser che supportano le schede personalizzate, come i seguenti:

| Browser con supporto per le schede personalizzate | Nome del pacchetto |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Coraggioso | com.brave.browser|

Oltre ai browser con il supporto di schede personalizzate, sulla base dei nostri test alcuni browser che non supportano le schede personalizzate funzioneranno anche per l'autenticazione: Opera, Opera Mini, InBrowser e Maxthon. Per altre informazioni, leggere [tabella per i risultati dei test](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemi noti

- Se l'utente non dispone di alcun browser abilitate sul dispositivo, MSAL.NET genererà un `AndroidActivityNotFound` eccezione. 
  - **Mitigazione**: Informare l'utente di abilitare un browser (preferibilmente un oggetto con il supporto di schede personalizzate) nel proprio dispositivo.

- Se l'autenticazione ha esito negativo (ad es. l'autenticazione viene avviata con DuckDuckGo), MSAL.NET restituirà un `AuthenticationCanceled MsalClientException`. 
  - **Radice problema**: Un browser con supporto per le schede personalizzate non è stato abilitato nel dispositivo. Autenticazione avviato con un browser alternativo, che non è riuscito a completare l'autenticazione. 
  - **Mitigazione**: Informare l'utente che è consigliabile installare un browser (preferibilmente un oggetto con il supporto di scheda personalizzata) nel proprio dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivi e browser testati
La tabella seguente elenca i dispositivi e browser che sono stati testati.

| | Browser&ast;     |  Risultato  | 
| ------------- |:-------------:|:-----:|
| Huawei uno + | Chrome&ast; | Superato|
| Huawei uno + | Edge&ast; | Superato|
| Huawei uno + | Firefox&ast; | Superato|
| Huawei uno + | Coraggioso&ast; | Superato|
| Una o più | Ecosia&ast; | Superato|
| Una o più | Kiwi&ast; | Superato|
| Huawei uno + | Opera | Superato|
| Huawei | OperaMini | Superato|
| Huawei uno + | InBrowser | Superato|
| Una o più | Maxthon | Superato|
| Huawei uno + | DuckDuckGo | Autenticazione annullato dall'utente|
| Huawei uno + | Browser UC | Autenticazione annullato dall'utente|
| Una o più | delfino | Autenticazione annullato dall'utente|
| Una o più | Browser CM | Autenticazione annullato dall'utente|
| Huawei uno + | Nessuna installata | AndroidActivityNotFound ex|

&ast; Supporta le schede personalizzate

## <a name="next-steps"></a>Passaggi successivi
Per il codice frammenti di codice e informazioni aggiuntive sull'uso di browser del sistema con Xamarin. Android, leggere questo [Guida](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  