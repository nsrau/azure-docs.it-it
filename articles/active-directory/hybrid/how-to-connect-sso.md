---
title: 'Azure AD Connect: Accesso Single Sign-On facile | Microsoft Docs'
description: Questo argomento descrive la funzionalità Accesso Single Sign-On facile di Azure Active Directory (Azure AD) e il modo in cui consente di offrire un reale accesso Single Sign-On agli utenti dei desktop aziendali all'interno della rete aziendale.
services: active-directory
keywords: che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483755"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Accesso Single Sign-On facile di Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Che cos'è l'accesso Single Sign-On facile di Azure Active Directory?

L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Quando la funzionalità è abilitata, gli utenti non devono digitare la password per accedere ad Azure AD e in genere non devono neppure digitare il proprio nome utente. Gli utenti possono accedere facilmente alle applicazioni basate sul cloud senza usare componenti aggiuntivi in locale.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

L'accesso SSO facile può essere combinato con i metodi di accesso che usano la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) o l'[autenticazione pass-through](how-to-connect-pta.md). L'accesso Single Sign-On facile _non_ è applicabile ad Active Directory Federation Services (AD FS).

![Accesso Single Sign-On facile](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Seamless SSO richiede che il dispositivo dell'utente sia solo aggiunto a **un dominio,** ma non viene usato nei dispositivi [aggiunti ad Azure AD](../devices/concept-azure-ad-join.md) o Azure AD ibrido di Azure [AD.](../devices/concept-azure-ad-join-hybrid.md) SSO in Azure AD aggiunto e hybrid Azure AD aggiunto funziona in base al token di [aggiornamento primario](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Vantaggi principali

- *Miglioramento dell'esperienza utente*
  - Gli utenti vengono automaticamente connessi sia alle applicazioni locali che a quelle basate sul cloud.
  - Gli utenti non devono immettere ripetutamente le proprie password.
- *Facilità di distribuzione e gestione*
  - Non sono necessari componenti aggiuntivi locali per usare la funzionalità.
  - Funziona con qualsiasi metodo di autenticazione cloud: [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) o [autenticazione pass-through](how-to-connect-pta.md).
  - Può essere implementata per alcuni o tutti gli utenti con Criteri di gruppo.
  - Registrare dispositivi non Windows 10 con Azure AD, senza che sia necessaria un'infrastruttura AD FS. Per questa funzionalità è necessaria la versione 2.1 o successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funzionalità in primo piano

- Il nome utente usato per l'accesso può essere il nome utente predefinito locale (`userPrincipalName`) o un altro attributo configurato in Azure AD Connect (`Alternate ID`). Sono supportati entrambi i casi d'uso, perché l'accesso Single Sign-On facile usa l'attestazione `securityIdentifier` nel ticket Kerberos per cercare l'oggetto utente corrispondente in Azure AD.
- L'accesso SSO facile è una funzionalità opportunistica. Se per qualche motivo ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente deve immettere la propria password nella pagina di accesso.
- Se un'applicazione (ad `https://myapps.microsoft.com/contoso.com`esempio, `domain_hint` ) inoltra `whr` un parametro (OpenID Connect) `login_hint` o (SAML), identificando il tenant o il parametro, identificando l'utente, nella richiesta di accesso di Azure AD, gli utenti vengono automaticamente connessi senza immettere nomi utente o password.
- Gli utenti ottengono inoltre un'esperienza di accesso `https://contoso.sharepoint.com`invisibile all'utente se un'applicazione ( ad esempio, ) `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` invia richieste di accesso agli endpoint di `https://login.microsoftonline.com/common/<...>`Azure AD impostati come tenant, ovvero oppure, anziché l'endpoint comune di Azure AD, ovvero .
- La disconnessione è supportata. In questo modo gli utenti possono scegliere di eseguire l'accesso con un altro account di Azure AD anziché accedere automaticamente tramite l'accesso Single Sign-On facile.
- I client Office 365 Win32 (Outlook, Word, Excel e altri) con le versioni 16.0.8730.xxxx e successive sono supportati tramite un flusso non interattivo. Per OneDrive, è necessario attivare la [funzionalità di configurazione automatica di OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) per un'esperienza di accesso automatico.
- Può essere abilitata da Azure AD Connect.
- È una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per utilizzarla.
- Può essere usata per i client basati su Web browser e i client di Office che supportano l'[autenticazione moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) nelle piattaforme e nei browser idonei per l'autenticazione Kerberos:

| SO\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sì\*|Sì|Sì|Sì\*\*\*|N/D
|Windows 8.1|Sì\*|N/D|Sì|Sì\*\*\*|N/D
|Windows 8|Sì\*|N/D|Sì|Sì\*\*\*|N/D
|Windows 7|Sì\*|N/D|Sì|Sì\*\*\*|N/D
|Windows Server 2012 R2 o versione successiva|Sì\*\*|N/D|Sì|Sì\*\*\*|N/D
|Mac OS X|N/D|N/D|Sì\*\*\*|Sì\*\*\*|Sì\*\*\*


\*Richiede Internet Explorer versione 10 o una versione successiva

\*\*Richiede Internet Explorer versione 10 o successiva. Disabilitare la modalità protetta avanzata

\*\*\*Richiede [una configurazione aggiuntiva](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Per Windows 10, si consiglia di usare il [join per Azure AD](../devices/concept-azure-ad-join.md) per ottenere risultati ottimali dall'accesso Single Sign-On con Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [**Guida introduttiva**](how-to-connect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Piano di distribuzione**](https://aka.ms/deploymentplans/sso) - Piano di distribuzione dettagliato.
- [**Approfondimento tecnico**](how-to-connect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Domande frequenti**](how-to-connect-sso-faq.md) - Risposte alle domande frequenti.
- [**Risoluzione dei problemi:**](tshoot-connect-sso.md) informazioni su come risolvere i problemi comuni relativi alla funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

