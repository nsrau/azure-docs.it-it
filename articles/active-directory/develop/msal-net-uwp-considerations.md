---
title: Considerazioni sulla piattaforma Windows Universal (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sulle considerazioni specifiche quando si usa (Universal Windows Platform) con Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544127"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerazioni specifiche della piattaforma Windows universale con MSAL.NET
In Xamarin iOS, esistono diverse considerazioni di cui è necessario tenere conto quando si usa MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>La proprietà UseCorporateNetwork
Nella piattaforma, WinRT `PublicClientApplication` dispone della proprietà booleana seguente ``UseCorporateNetwork``. Questa proprietà consente alle applicazioni Win8.1 e della piattaforma UWP per trarre vantaggio dall'autenticazione integrata di Windows (e pertanto l'accesso SSO con l'utente connesso con il sistema operativo) se l'utente viene eseguito l'accesso aggiuntivo con un account di Azure federato tenant di AD. Questo consente di sfruttare WAB (Web Authentication Broker). 

> [!IMPORTANT]
> Impostando questa proprietà su true, si presuppone che lo sviluppatore dell'applicazione è abilitata l'autenticazione integrata di Windows (IWA) nell'applicazione. A tale scopo:
> - Nel ``Package.appxmanifest`` per l'applicazione UWP, nella **funzionalità** scheda, abilitare le funzionalità seguenti:
>   - Autenticazione aziendale
>   - Reti private (client e server)
>   - Certificati utente condivisi

Autenticazione integrata di Windows non è abilitato per impostazione predefinita in quanto le applicazioni che richiedono la funzionalità autenticazione Enterprise o i certificati utente condivisi richiedono un livello superiore della verifica di essere accettati in di Windows Store e gli sviluppatori non può essere utile eseguire più elevato livello di verifica. 

L'implementazione sottostante nella piattaforma UWP (WAB) non funziona correttamente in scenari aziendali in cui è stato abilitato l'accesso condizionale. Il sintomo è che l'utente tenta di effettuare l'accesso con hello di Windows e viene proposto per scegliere un certificato, ma il certificato per il pin non viene trovato, o l'utente la sceglie, ma mai ottenere richiesto il Pin. Soluzione alternativa consiste nell'usare un metodo alternativo (nome utente/password + telefono autenticazione), ma l'esperienza non è valida. 

## <a name="next-steps"></a>Passaggi successivi
Altri dettagli sono disponibili negli esempi seguenti:

Esempio | Piattaforma | Descrizione 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Un'applicazione client (Universal Windows Platform) tramite msal.net, l'accesso a Microsoft Graph per un utente esegue l'autenticazione con l'endpoint v2.0 di Azure AD. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Xamarin. Forms che illustra come usare MSAL per autenticare l'account del servizio gestito e Azure AD tramite l'endpoint v2.0 AAD e accedere a Microsoft Graph con il token risultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|