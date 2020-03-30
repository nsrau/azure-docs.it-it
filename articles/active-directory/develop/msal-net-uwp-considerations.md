---
title: Considerazioni sulla piattaforma UWP (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni per l'utilizzo della piattaforma UWP (Universal Windows Platform) con la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132517"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerazioni sull'utilizzo della piattaforma Windows universale con MSAL.NET
Gli sviluppatori di applicazioni che utilizzano la piattaforma UWP (Universal Windows Platform) con MSAL.NET devono considerare i concetti presentati in questo articolo.

## <a name="the-usecorporatenetwork-property"></a>La proprietà UseCorporateNetwork
Sulla piattaforma Windows Runtime (WinRT), `PublicClientApplication` ha `UseCorporateNetwork`la proprietà booleana . Questa proprietà consente alle applicazioni Windows 8.1 e alle applicazioni UWP di trarre vantaggio dall'autenticazione integrata di Windows (IWA) se l'utente ha eseguito l'accesso a un account con un tenant federato di Azure Active Directory (Azure AD). Gli utenti che hanno eseguito l'accesso al sistema operativo possono inoltre utilizzare Single Sign-On (SSO). Quando si `UseCorporateNetwork` imposta la proprietà, MSAL.NET utilizza un broker di autenticazione Web (WAB).

> [!IMPORTANT]
> L'impostazione della `UseCorporateNetwork` proprietà su true presuppone che lo sviluppatore dell'applicazione abbia abilitato IWA nell'applicazione. Per abilitare IWA:
> - Nell'applicazione `Package.appxmanifest`UWP, nella scheda **Funzionalità,** abilitare le funzionalità seguenti:
>   - **Autenticazione aziendale**
>   - **Reti private (client e server)**
>   - **Certificato utente condiviso**

IWA non è abilitato per impostazione predefinita perché Microsoft Store richiede un livello elevato di verifica prima di accettare le applicazioni che richiedono le funzionalità dell'autenticazione aziendale o dei certificati utente condivisi. Non tutti gli sviluppatori desiderano eseguire questo livello di verifica.

Nella piattaforma UWP, l'implementazione WAB sottostante non funziona correttamente negli scenari aziendali in cui è abilitato l'accesso condizionale. Gli utenti vedono i sintomi di questo problema quando tentano di accedere utilizzando Windows Hello. Quando all'utente viene chiesto di scegliere un certificato:

- Il certificato per il PIN non è stato trovato.
- Dopo che l'utente ha scelto un certificato, non viene richiesto il PIN.

È possibile evitare questo problema utilizzando un metodo alternativo, ad esempio nome utente-password e autenticazione del telefono, ma l'esperienza non è buona.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Alcuni clienti hanno segnalato il seguente errore di accesso in ambienti aziendali specifici in cui sanno di avere una connessione Internet e che la connessione funziona con una rete pubblica.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

È possibile evitare questo problema assicurandosi che WAB (il componente di Windows sottostante) consenta una rete privata. È possibile farlo impostando una chiave del Registro di sistema:You can do that by setting a registry key:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Per ulteriori informazioni, vedere [Broker di autenticazione Web - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Passaggi successivi
Negli esempi seguenti vengono fornite ulteriori informazioni.

Esempio | Piattaforma | Descrizione 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Un'applicazione client UWP che utilizza MSAL.NET. Accede a Microsoft Graph per un utente che esegue l'autenticazione usando un endpoint di Azure AD 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Xamarin Forms che mostra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint di Azure AD 2.0. Viene inoltre illustrato come accedere a Microsoft Graph e viene illustrato il token risultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
