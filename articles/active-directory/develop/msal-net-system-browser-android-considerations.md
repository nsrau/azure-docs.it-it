---
title: Considerazioni su Novell Android (Microsoft Authentication Library per .NET) | Azure
description: Per informazioni su considerazioni specifiche, vedere l'articolo relativo all'uso di Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532583"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Novell considerazioni specifiche per Android con MSAL.NET
Questo articolo illustra alcune considerazioni specifiche quando si usa il browser di sistema in Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).

A partire da MSAL.NET 2.4.0-Preview, MSAL.NET supporta browser diversi da Chrome e non richiede più l'installazione di Chrome nel dispositivo Android per l'autenticazione.

Si consiglia di utilizzare browser che supportano schede personalizzate, ad esempio:

| Browser con supporto per schede personalizzate | Nome del pacchetto |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Incredibile | com. brave. browser|

Oltre ai browser con supporto per schede personalizzate, in base ai test, alcuni browser che non supportano le schede personalizzate funzioneranno anche per l'autenticazione: Opera, opera mini, inbrowser e Maxthon. Per ulteriori informazioni, vedere [tabella per i risultati dei test](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemi noti

- Se per l'utente non è abilitato alcun browser sul dispositivo, MSAL.NET genererà `AndroidActivityNotFound` un'eccezione. 
  - **Mitigazione**: Informare l'utente che è necessario abilitare un browser, preferibilmente uno con supporto per schede personalizzate, sul dispositivo.

- Se l'autenticazione ha esito negativo (ad esempio l'autenticazione viene avviata con DuckDuckGo), MSAL.NET `AuthenticationCanceled MsalClientException`restituirà. 
  - **Problema principale**: Nel dispositivo non è stato abilitato un browser con schede personalizzate. Autenticazione avviata con un browser alternativo, che non è stato in grado di completare l'autenticazione. 
  - **Mitigazione**: Informare l'utente che è necessario installare un browser, preferibilmente uno con supporto per schede personalizzate, nel dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivi e browser testati
La tabella seguente elenca i dispositivi e i browser che sono stati testati.

| | Browser&ast;     |  Risultato  | 
| ------------- |:-------------:|:-----:|
| Huawei/uno + | Chrome&ast; | Superato|
| Huawei/uno + | Bordo&ast; | Superato|
| Huawei/uno + | Firefox&ast; | Superato|
| Huawei/uno + | Incredibile&ast; | Superato|
| Uno + | Ecosia&ast; | Superato|
| Uno + | Kiwi&ast; | Superato|
| Huawei/uno + | Opera | Superato|
| Huawei | OperaMini | Superato|
| Huawei/uno + | InBrowser | Superato|
| Uno + | Maxthon | Superato|
| Huawei/uno + | DuckDuckGo | Autenticazione annullata dall'utente|
| Huawei/uno + | Browser UC | Autenticazione annullata dall'utente|
| Uno + | Delfino | Autenticazione annullata dall'utente|
| Uno + | Browser CM | Autenticazione annullata dall'utente|
| Huawei/uno + | Nessuno installato | AndroidActivityNotFound ex|

&ast;Supporta schede personalizzate

## <a name="next-steps"></a>Passaggi successivi
Per i frammenti di codice e per informazioni aggiuntive sull'uso del browser di sistema con Novell Android, vedere questa [Guida](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  