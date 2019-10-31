---
title: Considerazioni sul browser di sistema di Novell Android (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni specifiche quando si usano i browser di sistema in Novell Android quando si usa Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83283cc02c60eb452d0e414c0b21843b48f343b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150352"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Considerazioni sul browser di sistema Novell Android con MSAL.NET

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