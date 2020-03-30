---
title: MSAL per iOS & differenze di macOS Azure
titleSuffix: Microsoft identity platform
description: Descrive le differenze di utilizzo di Microsoft Authentication Library (MSAL) tra iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084973"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Differenze tra Microsoft Authentication Library per iOS e macOS

In questo articolo vengono illustrate le differenze di funzionalità tra Microsoft Authentication Library (MSAL) per iOS e macOS.

> [!NOTE]
> Sul Mac, MSAL supporta solo le app macOS.

## <a name="general-differences"></a>Differenze generali

MSAL per macOS è un sottoinsieme delle funzionalità disponibili per iOS.

MSAL per macOS non supporta:

- diversi tipi di `ASWebAuthenticationSession` `SFAuthenticationSession`browser `SFSafariViewController`come , , .
- L'autenticazione negoziata tramite l'app Microsoft Authenticator non è supportata per macOS.

La condivisione del portachiavi tra le app dello stesso editore è più limitata su macOS 10.14 e versioni precedenti. Usare gli elenchi di controllo di [accesso](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) per specificare i percorsi delle app che devono condividere il portachiavi. L'utente può visualizzare ulteriori richieste del portachiavi.

Su macOS 10.15, il comportamento di MSAL è lo stesso tra iOS e macOS. MSAL utilizza i gruppi di [accesso portachiavi](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) per la condivisione del portachiavi. 

### <a name="conditional-access-authentication-differences"></a>Differenze di autenticazione dell'accesso condizionaleConditional access authentication differences

Per gli scenari di accesso condizionale, ci saranno meno richieste dell'utente quando si utilizza MSAL per iOS.For Conditional Access scenarios, there will be fewer user prompts when you use MSAL for iOS. Questo perché iOS usa l'app broker (Microsoft Authenticator) che nega la necessità di richiedere all'utente in alcuni casi.

### <a name="project-setup-differences"></a>Differenze di impostazione del progetto

**Macos**

- Quando configuri il progetto in macOS, assicurati che l'applicazione sia firmata con un certificato di sviluppo o produzione valido. MSAL funziona ancora in modalità senza segno, ma si comporterà in modo diverso per quanto riguarda la persistenza della cache. L'app deve essere eseguita solo senza segno a scopo di debug. Se distribuisci l'app senza firma,:
1. Su 10.14 e versioni precedenti, MSAL richiederà all'utente una password portachiavi ogni volta che riavvia l'app.
2. In 10.15, MSAL richiederà all'utente le credenziali per ogni acquisizione di token. 

- Le app macOS non devono implementare la chiamata AppDelegate.

**iOS**

- Esistono passaggi aggiuntivi per configurare il progetto per supportare il flusso del broker di autenticazione. I passaggi sono descritti nell'esercitazione.
- I progetti iOS devono registrare schemi personalizzati nel file info.plist. Questo non è richiesto su macOS.
