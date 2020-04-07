---
title: Authentication
description: Spiega come funziona l'autenticazione
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681636"
---
# <a name="configure-authentication"></a>Configurare l'autenticazione

Il rendering remoto di Azure usa lo stesso meccanismo di autenticazione degli ancoraggi spaziali di Azure.Azure Remote Rendering uses the same authentication mechanism as [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). I client devono impostare *AccountKey*, *AuthenticationToken*o *AccessToken* per chiamare correttamente le API REST. *AccountKey* può essere ottenuto nella scheda "Chiavi" per l'account di rendering remoto nel portale di Azure.AccountKey can be obtained in the "Keys" tab for the Remote Rendering account on the Azure portal. *AuthenticationToken* è un token di Azure AD, che può essere ottenuto tramite la [libreria ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* è un token MR, che può essere ottenuto dal servizio token di sicurezza di azure a realtà mista.

## <a name="authentication-for-deployed-applications"></a>Autenticazione per le applicazioni distribuite

 L'uso delle chiavi dell'account è consigliato per l'onboarding rapido, ma solo durante lo sviluppo/prototipazione. È consigliabile non spedire l'applicazione alla produzione usando una chiave dell'account incorporata e usare invece gli approcci di autenticazione di Azure AD basati sull'utente o basati su servizi.

 L'autenticazione di Azure AD è descritta nella sezione [Autenticazione utente di Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) del servizio Azure Spatial [Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesso ad applicazioni, servizi o utenti di Azure AD del servizio, sono stati creati i ruoli seguenti da assegnare in base alle esigenze per gli account di rendering remoto di Azure:To help control the level of access granted to applications, services or Azure AD users of your service, the following roles have been created for you to assign as needed against your Azure Remote Rendering accounts:

* **Amministratore per il rendering remoto:** fornisce all'utente funzionalità di conversione, gestione di sessioni, rendering e diagnostica per il rendering remoto di Azure.Remote Rendering Administrator : Provides user with conversion, manage session, rendering, and diagnostics capabilities for Azure Remote Rendering.
* **Client di rendering remoto:** fornisce all'utente funzionalità di gestione di sessione, rendering e diagnostica per il rendering remoto di Azure.Remote Rendering Client : Provides user with manage session, rendering, and diagnostics capabilities for Azure Remote Rendering.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account](create-an-account.md)
* [Uso delle API front-end di Azure per l'autenticazioneUsing the Azure Frontend APIs for authentication](frontend-apis.md)
* [Script di PowerShell di esempioExample PowerShell scripts](../samples/powershell-example-scripts.md)
