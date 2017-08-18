---
title: 'Azure AD Connect: Autenticazione pass-through - Limitazioni correnti | Microsoft Docs'
description: Questo articolo descrive le limitazioni correnti dell'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 37c0ea094d02208f2516a4a040f75894e046c670
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticazione pass-through di Azure Active Directory - Limitazioni correnti

>[!IMPORTANT]
>L'autenticazione pass-through di Azure AD è attualmente in fase di anteprima. È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla. L'autenticazione pass-through è disponibile solo nell'istanza di Azure AD a livello mondiale, non in [Microsoft Cloud per la Germania](http://www.microsoft.de/cloud-deutschland) o nel [cloud di Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenari supportati

Gli scenari seguenti sono completamente supportati in fase di anteprima:

- L'utente accede a tutte le applicazioni basate su Web browser.
- L'utente accede ad applicazioni client di Office 365 che supportano l'[autenticazione moderna](https://aka.ms/modernauthga).
- Aggiunta ad Azure AD per dispositivi Windows 10.
- Supporto di Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti _non_ sono supportati in fase di anteprima:

- Accesso degli utenti ad applicazioni client legacy di Office (Office 2013 o versioni precedenti). Le organizzazioni sono incoraggiate a passare all'autenticazione moderna, se possibile. L'autenticazione moderna permette di supportare l'autenticazione pass-through e contribuisce anche a proteggere gli account utente tramite le funzionalità di [accesso condizionale](../active-directory-conditional-access.md), come l'autenticazione a più fattori.
- Accesso degli utenti ad applicazioni client Skype for Business, incluso Skype for Business 2016.
- L'utente accede a PowerShell v 1.0. È consigliabile tuttavia usare PowerShell 2.0.

>[!IMPORTANT]
>Come soluzione alternativa per gli scenari non supportati, abilitare la sincronizzazione dell'hash della password nella pagina [Funzionalità facoltative](active-directory-aadconnect-get-started-custom.md#optional-features) della procedura guidata di Azure AD Connect. La sincronizzazione dell'hash della password funziona come fallback _solo_ per gli scenari precedenti (e _non_ come fallback generico per l'autenticazione pass-through). Se questi scenari non sono necessari, disabilitare la sincronizzazione dell'hash della password.

## <a name="next-steps"></a>Passaggi successivi
- [**Guida introduttiva**](active-directory-aadconnect-pass-through-authentication-quick-start.md): avvio ed esecuzione dell'autenticazione pass-through di Azure AD.
- [**Approfondimento tecnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Domande frequenti**](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Seamless Single Sign-On di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

