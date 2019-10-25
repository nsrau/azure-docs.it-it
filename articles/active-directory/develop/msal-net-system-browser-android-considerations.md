---
title: Considerazioni su Novell Android (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802744"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Novell considerazioni specifiche per Android con MSAL.NET
Questo articolo illustra alcune considerazioni specifiche quando si usa il browser di sistema in Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).

A partire da MSAL.NET 2.4.0-Preview, MSAL.NET supporta browser diversi da Chrome e non richiede più l'installazione di Chrome nel dispositivo Android per l'autenticazione.

Si consiglia di utilizzare browser che supportano schede personalizzate, ad esempio:

| Browser con supporto per schede personalizzate | Nome del pacchetto |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. Mozilla. Firefox|
|Ecosia | com. Ecosia. Android|
|Kiwi | com. kiwibrowser. browser|
|Incredibile | com. brave. browser|

Oltre ai browser con supporto per schede personalizzate, in base ai test, alcuni browser che non supportano schede personalizzate funzioneranno anche per l'autenticazione: opera, mini, inbrowser e Maxthon. Per ulteriori informazioni, vedere [tabella per i risultati dei test](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemi noti

- Se per l'utente non è abilitato alcun browser sul dispositivo, MSAL.NET genererà un'eccezione `AndroidActivityNotFound`. 
  - **Mitigazione**: informare l'utente che è necessario abilitare un browser, preferibilmente uno con supporto per schede personalizzate, sul dispositivo.

- Se l'autenticazione ha esito negativo (ad esempio l'autenticazione viene avviata con DuckDuckGo), MSAL.NET restituirà un `AuthenticationCanceled MsalClientException`. 
  - **Problema principale**: nel dispositivo non è stato abilitato un browser con schede personalizzate. Autenticazione avviata con un browser alternativo, che non è stato in grado di completare l'autenticazione. 
  - **Mitigazione**: informare l'utente che è necessario installare un browser, preferibilmente uno con supporto per schede personalizzate, nel dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivi e browser testati
La tabella seguente elenca i dispositivi e i browser che sono stati testati.

| | &ast; del browser     |  Risultato  | 
| ------------- |:-------------:|:-----:|
| Huawei/uno + | &ast; di Chrome | Pass|
| Huawei/uno + | &ast; Edge | Pass|
| Huawei/uno + | &ast; Firefox | Pass|
| Huawei/uno + | &ast; coraggioso | Pass|
| Uno + | &ast; Ecosia | Pass|
| Uno + | Kiwi&ast; | Pass|
| Huawei/uno + | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/uno + | Inbrowser | Pass|
| Uno + | Maxthon | Pass|
| Huawei/uno + | DuckDuckGo | Autenticazione annullata dall'utente|
| Huawei/uno + | Browser UC | Autenticazione annullata dall'utente|
| Uno + | Delfino | Autenticazione annullata dall'utente|
| Uno + | Browser CM | Autenticazione annullata dall'utente|
| Huawei/uno + | Nessuno installato | AndroidActivityNotFound ex|

&ast; supporta le schede personalizzate

## <a name="next-steps"></a>Passaggi successivi
Per i frammenti di codice e per informazioni aggiuntive sull'uso del browser di sistema con Novell Android, vedere questa [Guida](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  