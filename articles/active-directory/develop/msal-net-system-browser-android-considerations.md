---
title: Xamarin Android browser considerazioni (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni per l'utilizzo dei browser di sistema in Xamarin Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132606"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android considerazioni sul browser di sistema per l'utilizzo di MSAL.NET

In questo articolo vengono illustrati gli aspetti da considerare quando si utilizza il browser di sistema in Xamarin Android con Microsoft Authentication Library for .NET (MSAL.NET).

A partire da MSAL.NET 2.4.0 Preview, MSAL.NET supporta browser diversi da Chrome. Non richiede più che Chrome sia installato sul dispositivo Android per l'autenticazione.

Si consiglia di utilizzare browser che supportano schede personalizzate. Ecco alcuni esempi di questi browser:

| I browser con schede personalizzate supportano | Nome del pacchetto |
|------| ------- |
|Chrome | com.Android.Chrome|
|Microsoft Edge | com.microsoft.emmx (informazioni in com)com.microsoft.emmx (informazioni in ucrain|
|Firefox | org.mozilla.firefox (informazioni in base alle|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Coraggioso | com.brave.browser|

Oltre a identificare i browser che offrono il supporto delle schede personalizzate, il nostro test indica che alcuni browser che non supportano schede personalizzate funzionano anche per l'autenticazione. Questi browser includono Opera, Opera Mini, InBrowser e Maxthon. 

## <a name="tested-devices-and-browsers"></a>Dispositivi e browser testati
Nella tabella seguente sono elencati i dispositivi e i browser che sono stati testati per la compatibilità dell'autenticazione.

| Dispositivo | Browser     |  Risultato  | 
| ------------- |:-------------:|:-----:|
| Huawei/Uno | Chrome\* | Pass|
| Huawei/Uno | Bordo\* | Pass|
| Huawei/Uno | Firefox\* | Pass|
| Huawei/Uno | Coraggioso\* | Pass|
| Uno e il primo | Ecosia\* | Pass|
| Uno e il primo | Kiwi\* | Pass|
| Huawei/Uno | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/Uno | InBrowser | Pass|
| Uno e il primo | Maxthon | Pass|
| Huawei/Uno | DuckDuckGo | Autenticazione annullata dall'utente|
| Huawei/Uno | UC Browser | Autenticazione annullata dall'utente|
| Uno e il primo | delfino | Autenticazione annullata dall'utente|
| Uno e il primo | CM Browser | Autenticazione annullata dall'utente|
| Huawei/Uno | Nessuno installato | AndroidActivityNotFound (eccezione)|

\*Supporta schede personalizzate

## <a name="known-issues"></a>Problemi noti

Se l'utente non ha attivato il browser `AndroidActivityNotFound` sul dispositivo, MSAL.NET genererà un'eccezione.  
  - **Mitigazione**: Chiedere all'utente di abilitare un browser sul proprio dispositivo. Consigliare un browser che supporta le schede personalizzate.

Se l'autenticazione non riesce (ad esempio, se `AuthenticationCanceled MsalClientException`l'autenticazione viene avviata con DuckDuckGo), MSAL.NET restituirà . 
  - **Problema principale**: Un browser che supporta le schede personalizzate non è stato abilitato sul dispositivo. Autenticazione avviata con un browser che non è stato in stato di completa completa l'autenticazione. 
  - **Mitigazione**: Chiedere all'utente di abilitare un browser sul proprio dispositivo. Consigliare un browser che supporta le schede personalizzate.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni ed esempi di codice, consultate Scelta tra un browser Web incorporato e un browser di [sistema in Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) e interfaccia utente Web [incorporata e di sistema.](msal-net-web-browsers.md#embedded-vs-system-web-ui)  