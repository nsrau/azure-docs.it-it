---
title: "Azure AD Connect: funzionamento dell'accesso Single Sign-On facile | Microsoft Docs"
description: In questo articolo viene descritto il funzionamento dell'accesso Single Sign-On facile di Azure Active Directory.
services: active-directory
keywords: che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Accesso Single Sign-On facile di Azure Active Directory: approfondimento tecnico

Questo articolo riporta i dettagli tecnici del funzionamento dell'accesso Single Sign-On (SSO) facile di Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Come opera la SSO facille?

Questa sezione è composta da tre parti:
1. Installazione della funzionalità di accesso SSO facile.
2. Funzionamento di una transazione di accesso utente singolo in un browser Web con accesso SSO facile.
3. Funzionamento di una transazione di accesso utente singolo in un client nativo con accesso SSO facile.

### <a name="how-does-set-up-work"></a>Installazione

L'accesso SSO facile viene abilitato con Azure AD Connect, come illustrato [qui](active-directory-aadconnect-sso-quick-start.md). Durante l'abilitazione della funzionalità, si verificano i passaggi seguenti:
- Un account computer denominato `AZUREADSSOACC`, che rappresenta Azure AD, viene creato in Active Directory (AD) locale.
- La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD.
- Vengono anche creati due nomi dell'entità servizio (SPN, Service Principal Name) Kerberos per rappresentare due URL usati durante l'accesso ad Azure AD.

>[!NOTE]
> L'account computer e gli SPN Kerberos vengono creati in ogni foresta di AD che si sincronizza con Azure AD, tramite Azure AD Connect, e per i cui utenti si vuole abilitare l'accesso SSO facile. Spostare l'account computer `AZUREADSSOACC` in un'unità organizzativa in cui sono archiviati altri account computer per assicurarsi che venga gestito allo stesso modo e non venga eliminato.

>[!IMPORTANT]
>È consigliabile [rinnovare la chiave di decrittografia di Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) dell'account computer `AZUREADSSOACC` almeno ogni 30 giorni.

Una volta completata l'installazione, l'accesso SSO facile funziona esattamente come qualsiasi altro accesso che usa l'autenticazione integrata di Windows (NTLM).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Funzionamento dell'accesso in un browser Web con accesso SSO facile.

Il flusso di accesso in un browser Web è il seguente:

1. L'utente tenta di accedere a un'applicazione Web, ad esempio Outlook Web App, https://outlook.office365.com/owa/, da un dispositivo aziendale appartenente a un dominio all'interno della rete aziendale.
2. Se l'utente non ha ancora eseguito l'accesso, viene reindirizzato alla pagina di accesso di Azure AD.
3. L'utente digita il nome utente nella pagina di accesso di Azure AD.

  >[!NOTE]
  >Per [determinate applicazioni](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso) i passaggi 2 e 3 vengono ignorati.

4. Tramite l'uso di JavaScript in background, Azure AD richiede al browser, tramite una risposta di tipo 401 Non autorizzato, di specificare un ticket Kerberos.
5. Il browser, a sua volta, richiede un ticket ad Active Directory per l'account computer `AZUREADSSOACC` che rappresenta Azure AD.
6. Active Directory individua l'account computer e restituisce un ticket Kerberos al browser, crittografato con il segreto dell'account computer.
7. Il browser inoltra il ticket Kerberos acquisito da Active Directory ad Azure AD.
8. Azure AD esegue la decrittografia del ticket Kerberos, che include l'identità dell'utente connesso al dispositivo aziendale, usando la chiave già condivisa.
9. Dopo la valutazione, Azure AD restituisce un token all'applicazione oppure chiede all'utente di eseguire prove aggiuntive, ad esempio l'autenticazione a più fattori.
10. Se l'accesso dell'utente ha esito positivo, l'utente può accedere all'applicazione.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Flusso di accesso Single Sign-On facile in un'app Web](./media/active-directory-aadconnect-sso/sso2.png)

L'accesso SSO facile è una funzionalità opportunistica, il che significa che, se ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente dovrà immettere la propria password per eseguire l'accesso.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Funzionamento dell'accesso in un client nativo con accesso SSO facile.

Il flusso di accesso in un client nativo è il seguente:

1. L'utente tenta di accedere a un'applicazione nativa, ad esempio il client Outlook, da un dispositivo aziendale appartenente a un dominio all'interno della rete aziendale.
2. Se l'utente non ha ancora eseguito l'accesso, l'applicazione nativa recupera il relativo nome utente dalla sessione di Windows del dispositivo.
3. L'app invia il nome utente ad Azure AD e recupera l'endpoint MEX di WS-Trust del tenant.
4. L'app esegue quindi una query sull'endpoint MEX di WS-Trust per vedere se è disponibile l'endpoint per l'autenticazione integrata.
5. Se il passaggio 4 ha esito positivo, viene generata una richiesta di verifica Kerberos.
6. Se l'app è in grado di recuperare il ticket Kerberos, lo inoltra fino all'endpoint per l'autenticazione integrata di Azure AD.
7. Azure AD decrittografa il ticket Kerberos e lo convalida.
8. Azure AD consente l'accesso dell'utente e invia un token SAML all'app.
9. L'app invia quindi il token SAML all'endpoint token OAuth2 di Azure AD.
10. Azure AD convalida il token SAML e invia all'app un token di accesso e un token di aggiornamento per la risorsa specificata, nonché un token ID.
11. L'utente ottiene l'accesso alla risorsa dell'app.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Flusso di accesso Single Sign-On facile in un'app nativa](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Passaggi successivi

- [**Guida introduttiva**](active-directory-aadconnect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Domande frequenti**](active-directory-aadconnect-sso-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
