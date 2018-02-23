---
title: 'Azure AD Connect: Accesso Single Sign-On facile | Microsoft Docs'
description: "Questo argomento descrive la funzionalità Accesso Single Sign-On facile di Azure Active Directory (Azure AD) e il modo in cui consente di offrire un reale accesso Single Sign-On agli utenti dei desktop aziendali all'interno della rete aziendale."
services: active-directory
keywords: "che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: billmath
ms.openlocfilehash: b71a2f19fee370ab1d732becd1c3b644505e2233
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Accesso Single Sign-On facile di Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Che cos'è l'accesso Single Sign-On facile di Azure Active Directory?

L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Quando la funzionalità è abilitata, gli utenti non devono digitare la password per accedere ad Azure AD e in genere non devono neppure digitare il proprio nome utente. Gli utenti possono accedere facilmente alle applicazioni basate sul cloud senza usare componenti aggiuntivi in locale.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

L'accesso SSO facile può essere combinato con i metodi di accesso che usano la [sincronizzazione dell'hash delle password](active-directory-aadconnectsync-implement-password-synchronization.md) o l'[autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md).

![Accesso Single Sign-On facile](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>L'accesso Single Sign-On facile _non_ è applicabile ad Active Directory Federation Services (AD FS).

## <a name="key-benefits"></a>Vantaggi principali

- *Miglioramento dell'esperienza utente*
  - Gli utenti vengono automaticamente connessi sia alle applicazioni locali che a quelle basate sul cloud.
  - Gli utenti non devono immettere ripetutamente le proprie password.
- *Facilità di distribuzione e gestione*
  - Non sono necessari componenti aggiuntivi locali per usare la funzionalità.
  - Funziona con qualsiasi metodo di autenticazione cloud: [sincronizzazione dell'hash delle password](active-directory-aadconnectsync-implement-password-synchronization.md) o [autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md).
  - Può essere implementata per alcuni o tutti gli utenti con Criteri di gruppo.
  - Registrare dispositivi non Windows 10 con Azure AD, senza che sia necessaria un'infrastruttura AD FS. Per questa funzionalità è necessaria la versione 2.1 o successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funzionalità in primo piano

- Il nome utente usato per l'accesso può essere il nome utente predefinito locale (`userPrincipalName`) o un altro attributo configurato in Azure AD Connect (`Alternate ID`). Sono supportati entrambi i casi d'uso, perché l'accesso Single Sign-On facile usa l'attestazione `securityIdentifier` nel ticket Kerberos per cercare l'oggetto utente corrispondente in Azure AD.
- L'accesso SSO facile è una funzionalità opportunistica. Se per qualche motivo ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente deve immettere la propria password nella pagina di accesso.
- Se un'applicazione inoltra un parametro `domain_hint` (OpenID Connect) o `whr` (SAML) per identificare il tenant o un parametro `login_hint` per identificare l'utente, nella richiesta di accesso ad Azure AD l'accesso degli utenti viene effettuato automaticamente, senza che debbano immettere i nomi utente o le password.
- Può essere abilitata da Azure AD Connect.
- È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla.
- Può essere usata per i client basati su Web browser e i client di Office che supportano l'[autenticazione moderna](https://aka.ms/modernauthga) nelle piattaforme e nei browser idonei per l'autenticazione Kerberos:

| SO\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sì|No |Sì|Sì\*|N/D
|Windows 8.1|Sì|N/D|Sì|Sì\*|N/D
|Windows 8|Sì|N/D|Sì|Sì\*|N/D
|Windows 7|Sì|N/D|Sì|Sì\*|N/D
|Mac OS X|N/D|N/D|Sì\*|Sì\*|Sì\*

\*Richiede una [configurazione aggiuntiva](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Per Windows 10, si consiglia di usare il [join per Azure AD](../active-directory-azureadjoin-overview.md) per ottenere risultati ottimali dall'accesso Single Sign-On con Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [**Avvio rapido**](active-directory-aadconnect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Approfondimento tecnico**](active-directory-aadconnect-sso-how-it-works.md): informazioni sul funzionamento dell'accesso facile.
- [**Domande frequenti**](active-directory-aadconnect-sso-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
