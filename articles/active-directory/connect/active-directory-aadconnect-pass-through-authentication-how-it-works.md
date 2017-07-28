---
title: 'Azure AD Connect: funzionamento dell''autenticazione pass-through | Microsoft Docs'
description: Questo articolo descrive il funzionamento dell'autenticazione pass-through di Azure Active Directory.
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
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 294ffde02fdf6b54e03837b2ace77b2940441459
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticazione pass-through di Azure Active Directory: approfondimento tecnico

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funzionamento dell'autenticazione pass-through di Azure Active Directory

Quando un utente tenta di accedere a un'applicazione Azure Active Directory (Azure AD), se l'autenticazione pass-through è abilitata nel tenant, si verificano i passaggi seguenti:

1. L'utente tenta di accedere a un'applicazione, ad esempio a Outlook Web App, https://outlook.office365.com/owa/.
2. Se l'utente non ha ancora eseguito l'accesso, viene reindirizzato alla pagina di accesso di Azure AD.
3. L'utente immette il nome utente e la password nella pagina di accesso di Azure AD e fa clic sul pulsante "Accedi".
4. Quando riceve la richiesta di accesso, Azure AD inserisce il nome utente e la password, crittografata con una chiave pubblica, in una coda.
5. Un agente di autenticazione pass-through locale esegue una chiamata in uscita alla coda e recupera il nome utente e la password crittografata.
6. L'agente decrittografa la password tramite la chiave privata.
7. L'agente convalida quindi il nome utente e la password in Active Directory usando le API Windows standard. Questo meccanismo è simile a quello usato da Active Directory Federation Services. Il nome utente può essere il nome utente predefinito locale (in genere `userPrincipalName`) o un altro attributo configurato in Azure AD Connect (noto come `Alternate ID`).
8. Il controller di dominio (DC, Domain Controller) di Active Directory locale valuta quindi la richiesta e restituisce all'agente la risposta appropriata che può essere esito positivo, errore, password scaduta o utente bloccato.
9. L'agente, a sua volta, restituisce la risposta ad Azure AD.
10. Azure AD valuta la risposta e risponde all'utente come appropriato. Ad esempio, consente l'accesso immediato dell'utente o richiede l'autenticazione a più fattori (MFA, Multi-Factor Authentication).
11. Se l'accesso dell'utente ha esito positivo, l'utente può accedere all'applicazione.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Passaggi successivi
- [**Current limitations**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) (Limitazioni correnti): questa funzionalità è attualmente in anteprima. Informazioni su quali scenari sono supportati e quali non lo sono.
- [**Quick Start**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Get up and running Azure AD Pass-through Authentication (Guida introduttiva: avvio ed esecuzione dell'autenticazione pass-through di Azure AD).
- [**Domande frequenti**](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Accesso SSO facile di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

