---
title: "Azure AD Connect: funzionamento dell'autenticazione pass-through | Microsoft Docs"
description: Questo articolo descrive il funzionamento dell'autenticazione pass-through di Azure Active Directory
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4a98b971367c9f83826e85bdc24bbcfe48483f57
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916266"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticazione pass-through di Azure Active Directory: approfondimento tecnico
Questo articolo offre una panoramica del funzionamento dell'autenticazione pass-through di Azure Active Directory (Azure AD). Per informazioni approfondite di tipo tecnico e relative alla sicurezza, vedere l'articolo di [approfondimento sulla sicurezza](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funzionamento dell'autenticazione pass-through di Azure Active Directory

Quando un utente tenta di accedere a un'applicazione Azure Active Directory, se l'autenticazione pass-through è abilitata nel tenant, si verificano i passaggi seguenti:

1. L'utente tenta di accedere a un'applicazione, ad esempio [Outlook Web App](https://outlook.office365.com/owa/).
2. Se l'utente non ha ancora eseguito l'accesso, viene reindirizzato alla pagina **Accesso utente** di Azure AD.
3. L'utente immette il nome utente nella pagina di accesso di Azure AD e seleziona il pulsante **Avanti**.
4. L'utente immette la password nella pagina di accesso di Azure AD e seleziona il pulsante **Accedi**.
5. Quando riceve la richiesta di accesso, Azure AD inserisce in una coda il nome utente e la password, crittografata con una chiave pubblica degli Agenti autenticazione.
6. Un agente di autenticazione locale recupera il nome utente e la password crittografata dalla coda. Si noti che l'agente non esegue di frequente il polling per le richieste dalla coda, ma recupera le richieste su una connessione permanente prestabilita.
7. L'agente decrittografa la password tramite la chiave privata.
8. L'agente convalida il nome utente e la password in Active Directory usando le API Windows standard. Questo meccanismo è simile a quello usato da Active Directory Federation Services (AD FS). Il nome utente può essere il nome utente predefinito locale (in genere `userPrincipalName`) o un altro attributo configurato in Azure AD Connect (noto come `Alternate ID`).
9. Il controller di dominio (DC, Domain Controller) di Active Directory locale valuta la richiesta e restituisce all'agente la risposta appropriata che può essere esito positivo, errore, password scaduta o utente bloccato.
10. L'agente di autenticazione, a sua volta, restituisce la risposta ad Azure AD.
11. Azure AD valuta la risposta e risponde all'utente nel modo appropriato. Ad esempio, Azure AD esegue l'accesso immediato dell'utente o richiede l'esecuzione dell'autenticazione a più fattori di Azure.
12. Se l'accesso dell'utente ha esito positivo, l'utente può accedere all'applicazione.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati:

![Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Passaggi successivi
- [Limitazioni correnti](active-directory-aadconnect-pass-through-authentication-current-limitations.md): apprendere quali sono gli scenari supportati.
- [Guida introduttiva](active-directory-aadconnect-pass-through-authentication-quick-start.md): iniziare a usare l'autenticazione pass-through di Azure AD.
- [Blocco intelligente](../authentication/howto-password-smart-lockout.md): configurare la funzionalità Blocco intelligente nel tenant per proteggere gli account utente.
- [Domande frequenti](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni con la funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): informazioni tecniche approfondite sull'autenticazione pass-through di Azure AD.
- [Accesso Single Sign-On facile di Azure Active Directory](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.

