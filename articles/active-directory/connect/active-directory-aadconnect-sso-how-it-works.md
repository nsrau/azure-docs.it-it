---
title: 'Azure AD Connect: funzionamento dell''accesso Single Sign-On facile | Microsoft Docs'
description: In questo articolo viene descritto il funzionamento dell'accesso Single Sign-On facile di Azure Active Directory.
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
ms.date: 08/02/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: f0bcbdb03fbb70ff91ac3a56974a88eb1b26c245
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Accesso Single Sign-On facile di Azure Active Directory: approfondimento tecnico

Questo articolo riporta i dettagli tecnici del funzionamento dell'accesso Single Sign-On (SSO) facile di Azure Active Directory.

>[!IMPORTANT]
>La funzionalità Accesso Single Sign-On facile è attualmente in fase di anteprima.

## <a name="how-does-seamless-sso-work"></a>Come opera la SSO facille?

Questa sezione è composta da due parti:
1. Installazione della funzionalità di accesso SSO facile.
2. Funzionamento di una transazione di accesso utente singolo con SSO facile.

### <a name="how-does-set-up-work"></a>Installazione

L'accesso SSO facile viene abilitato con Azure AD Connect, come illustrato [qui](active-directory-aadconnect-sso-quick-start.md). Durante l'abilitazione della funzionalità, si verificano i passaggi seguenti:
- Un account computer denominato `AZUREADSSOACCT`, che rappresenta Azure AD, viene creato in Active Directory (AD) locale.
- La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
- Vengono anche creati due nomi dell'entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

>[!NOTE]
> L'account computer e gli SPN Kerberos vengono creati in ogni foresta di AD che si sincronizza con Azure AD, tramite Azure AD Connect, e per i cui utenti si vuole abilitare l'accesso SSO facile. Spostare l'account computer `AZUREADSSOACCT` in un'unità organizzativa in cui sono archiviati altri account computer per assicurarsi che venga gestito allo stesso modo e non venga eliminato.

>[!IMPORTANT]
>È consigliabile [rinnovare la chiave di decrittografia di Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacct-computer-account) dell'account computer `AZUREADSSOACCT` almeno ogni 30 giorni.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>Funzionamento dell'accesso SSO facile

Una volta completata l'installazione, l'accesso SSO facile funziona esattamente come qualsiasi altro accesso che usa l'autenticazione integrata di Windows (NTLM). Il flusso è il seguente:

1. L'utente tenta di accedere a un'applicazione, ad esempio Outlook Web App, https://outlook.office365.com/owa/, da un dispositivo aziendale appartenente a un dominio all'interno della rete aziendale.
2. Se l'utente non ha ancora eseguito l'accesso, viene reindirizzato alla pagina di accesso di Azure AD.

  >[!NOTE]
  >Se la richiesta di accesso ad Azure AD include il parametro `domain_hint` che identifica il tenant, ad esempio contoso.onmicrosoft.com, o il parametro `login_hint` che identifica l'utente, ad esempio user@contoso.onmicrosoft.com o user@contoso.com, il passaggio 2 viene saltato.

3. L'utente digita il nome utente nella pagina di accesso di Azure AD.
4. Tramite l'uso di JavaScript in background, Azure AD richiede al browser, tramite una risposta di tipo 401 Non autorizzato, di specificare un ticket Kerberos.
5. Il browser, a sua volta, richiede un ticket ad Active Directory per l'account computer `AZUREADSSOACCT` che rappresenta Azure AD.
6. Active Directory individua l'account computer e restituisce un ticket Kerberos al browser, crittografato con il segreto dell'account computer.
7. Il browser inoltra il ticket Kerberos acquisito da Active Directory ad Azure AD in uno degli [URL di Azure AD aggiunti in precedenza nelle impostazioni dell'area Intranet del browser](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature).
8. Azure AD esegue la decrittografia del ticket Kerberos, che include l'identità dell'utente connesso al dispositivo aziendale, usando la chiave già condivisa.
9. Dopo la valutazione, Azure AD restituisce un token all'applicazione oppure chiede all'utente di eseguire prove aggiuntive, ad esempio l'autenticazione a più fattori.
10. Se l'accesso dell'utente ha esito positivo, l'utente può accedere all'applicazione.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso2.png)

L'accesso SSO facile è una funzionalità opportunistica, il che significa che, se ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente dovrà immettere la propria password per eseguire l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [**Guida introduttiva**](active-directory-aadconnect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Domande frequenti**](active-directory-aadconnect-sso-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

