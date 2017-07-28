---
title: 'Azure AD Connect: domande frequenti sull''accesso Single Sign-On facile | Microsoft Docs'
description: Risposte alle domande frequenti sull'accesso Single Sign-On facile di Azure Active Directory.
services: active-directory
keywords: "che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
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
ms.openlocfilehash: e363ade43ab9e2b2432c9efdc3ce1b661e278b2a
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Accesso Single Sign-On facile di Azure Active Directory: domande frequenti

In questo articolo viene data risposta alle domande frequenti sull'accesso SSO facile di Azure AD. Visitare questa pagina regolarmente per nuovi contenuti.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Con quali metodi di accesso funziona l'accesso SSO facile?

L'accesso SSO facile può essere combinato con i metodi di accesso della [sincronizzazione dell'hash delle password](active-directory-aadconnectsync-implement-password-synchronization.md) o dell'[autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md), ma non con Active Directory Federation Services (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>La funzionalità Accesso SSO facile è disponibile gratuitamente?

La funzionalità Accesso SSO facile è una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per usarla. Rimane gratuita quando la funzionalità raggiunge la disponibilità generale.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quali applicazioni possono sfruttare le capacità dei parametri `domain_hint` o `login_hint` dell'accesso SSO facile?

Attualmente è in corso la compilazione dell'elenco delle applicazioni che inviano tali parametri e di quelle che non lo fanno. Se si hanno applicazioni interessate, segnalarlo nella sezione dei commenti.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L'accesso SSO facile supporta `Alternate ID` come nome utente, anziché `userPrincipalName`?

Sì. L'accesso SSO facile supporta `Alternate ID` come nome utente quando è configurato in Azure AD Connect, come illustrato [qui](active-directory-aadconnect-get-started-custom.md). Non tutte le applicazioni di Office 365 supportano `Alternate ID`. Fare riferimento alla documentazione dell'applicazione specifica per sapere se è supportato.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Se si vuole registrare dispositivi non Windows 10 con Azure AD, senza l'uso di AD FS, è possibile usare l'accesso SSO facile?

Sì, per questo scenario è necessaria la versione 2.1 o versione successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-disable-seamless-sso"></a>Come è possibile disabilitare l'accesso SSO facile?

La funzionalità Accesso SSO facile può essere disabilitata tramite Azure AD Connect.

Eseguire Azure AD Connect, scegliere la pagina "Cambia l'accesso utente" e fare clic su "Avanti". Deselezionare quindi l'opzione "Abilita Single Sign-On". Continuare la procedura guidata. Al termine della procedura guidata, l'accesso SSO facile è disabilitato nel tenant.

Viene tuttavia visualizzato un messaggio con il testo seguente:

"L'accesso Single Sign-On è ora disabilitato, ma è necessario eseguire altri passaggi manuali per completare la pulizia. Altre informazioni"

La procedura manuale da eseguire è la seguente:

1. Ottenere l'elenco delle foreste di Active Directory in cui è stata abilitata la funzionalità Accesso SSO facile
- Scaricare e installare prima l' [Assistente per l'accesso ai Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Scaricare e installare quindi il [modulo di Azure Active Directory a 64 bit per Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
  - In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
  - Eseguire la chiamata a `Get-AzureADSSOStatus`. Il comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.
2. Eliminare manualmente l'account computer `AZUREADSSOACCT` da ogni foresta di AD elencata.

## <a name="next-steps"></a>Passaggi successivi

- [**Guida introduttiva**](active-directory-aadconnect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Approfondimento tecnico**](active-directory-aadconnect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

