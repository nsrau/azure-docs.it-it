---
title: "Azure AD Connect: Funzionamento dell'accesso Single Sign-On facile | Microsoft Docs"
description: In questo articolo viene descritto il funzionamento dell'accesso Single Sign-On facile di Azure Active Directory.
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
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907abe3b09f9999b30703281f7e4ff286e2bae14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242335"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Accesso Single Sign-On facile di Azure Active Directory: Approfondimento tecnico

Questo articolo riporta i dettagli tecnici del funzionamento dell'accesso Single Sign-On (SSO) facile di Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Come opera la SSO facille?

Questa sezione è composta da tre parti:

1. Installazione della funzionalità di accesso SSO facile.
2. Funzionamento di una transazione di accesso utente singolo in un browser Web con accesso SSO facile.
3. Funzionamento di una transazione di accesso utente singolo in un client nativo con accesso SSO facile.

### <a name="how-does-set-up-work"></a>Installazione

L'accesso SSO facile viene abilitato con Azure AD Connect, come illustrato [qui](how-to-connect-sso-quick-start.md). Durante l'abilitazione della funzionalità, si verificano i passaggi seguenti:

- Un account computer (`AZUREADSSOACC`) viene creato in locale Active Directory (AD) in ogni foresta di Active Directory che si sincronizza con Azure AD (tramite Azure AD Connect).
- Inoltre, viene creato un numero di nomi dell'entità Kerberos servizio (SPN) da utilizzare durante il processo di accesso di Azure AD.
- La chiave di decrittografia Kerberos dell'account computer viene condivisa in modo sicuro con Azure AD. Se sono presenti più foreste Active Directory, ogni account computer avrà la propria chiave di decrittografia Kerberos univoca.

>[!IMPORTANT]
> Il `AZUREADSSOACC` account computer deve essere fortemente protetto per motivi di sicurezza. Solo gli amministratori di dominio devono essere in grado di gestire l'account del computer. Assicurarsi che la delega Kerberos sull'account del computer è disabilitata e che nessun altro account in Active Directory disponga delle autorizzazioni di delega `AZUREADSSOACC` account del computer... Store l'account del computer in un'unità Organizzativa in cui siano sicure da eliminazioni accidentali e solo gli amministratori di dominio hanno accesso. La chiave di decrittografia Kerberos dell'account computer deve essere considerata come sensibili. È consigliabile [rinnovare la chiave di decrittografia di Kerberos](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) dell'account computer `AZUREADSSOACC` almeno ogni 30 giorni.

Una volta completata l'installazione, l'accesso SSO facile funziona esattamente come qualsiasi altro accesso che usa l'autenticazione integrata di Windows (NTLM).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Funzionamento dell'accesso in un browser Web con accesso SSO facile.

Il flusso di accesso in un browser Web è il seguente:

1. L'utente tenta di accedere a un'applicazione Web, ad esempio, l'app Web di Outlook https://outlook.office365.com/owa/) da un dispositivo aziendale appartenente a un dominio all'interno della rete aziendale.
2. Se l'utente non ha ancora eseguito l'accesso, viene reindirizzato alla pagina di accesso di Azure AD.
3. L'utente digita il nome utente nella pagina di accesso di Azure AD.

   >[!NOTE]
   >Per [determinate applicazioni](./how-to-connect-sso-faq.md#what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso) i passaggi 2 e 3 vengono ignorati.

4. Tramite l'uso di JavaScript in background, Azure AD richiede al browser, tramite una risposta di tipo 401 Non autorizzato, di specificare un ticket Kerberos.
5. Il browser, a sua volta, richiede un ticket ad Active Directory per l'account computer `AZUREADSSOACC` che rappresenta Azure AD.
6. Active Directory individua l'account computer e restituisce un ticket Kerberos al browser, crittografato con il segreto dell'account computer.
7. Il browser inoltra il ticket Kerberos acquisito da Active Directory ad Azure AD.
8. Azure AD esegue la decrittografia del ticket Kerberos, che include l'identità dell'utente connesso al dispositivo aziendale, usando la chiave già condivisa.
9. Dopo la valutazione, Azure AD restituisce un token all'applicazione oppure chiede all'utente di eseguire prove aggiuntive, ad esempio l'autenticazione a più fattori.
10. Se l'accesso dell'utente ha esito positivo, l'utente può accedere all'applicazione.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Flusso di accesso Single Sign-On facile in un'app Web](./media/how-to-connect-sso-how-it-works/sso2.png)

L'accesso SSO facile è una funzionalità opportunistica, il che significa che, se ha esito negativo, l'esperienza di accesso dell'utente ritorna al comportamento normale, ovvero l'utente dovrà immettere la propria password per eseguire l'accesso.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Funzionamento dell'accesso in un client nativo con accesso SSO facile.

Il flusso di accesso in un client nativo è il seguente:

1. L'utente tenta di accedere a un'applicazione nativa, ad esempio il client Outlook, da un dispositivo aziendale appartenente a un dominio all'interno della rete aziendale.
2. Se l'utente non ha ancora eseguito l'accesso, l'applicazione nativa recupera il relativo nome utente dalla sessione di Windows del dispositivo.
3. L'app invia il nome utente ad Azure AD e recupera l'endpoint MEX di WS-Trust del tenant. Questo endpoint di WS-Trust viene usato esclusivamente per la funzionalità Seamless SSO e non è un'implementazione generale del protocollo WS-Trust in Azure AD.
4. L'app esegue quindi una query sull'endpoint MEX di WS-Trust per vedere se è disponibile l'endpoint per l'autenticazione integrata. L'endpoint di autenticazione integrata viene usato esclusivamente dalla funzionalità Seamless SSO.
5. Se il passaggio 4 ha esito positivo, viene generata una richiesta di verifica Kerberos.
6. Se l'app è in grado di recuperare il ticket Kerberos, lo inoltra fino all'endpoint per l'autenticazione integrata di Azure AD.
7. Azure AD decrittografa il ticket Kerberos e lo convalida.
8. Azure AD consente l'accesso dell'utente e invia un token SAML all'app.
9. L'app invia quindi il token SAML all'endpoint token OAuth2 di Azure AD.
10. Azure AD convalida il token SAML e invia all'app un token di accesso e un token di aggiornamento per la risorsa specificata, nonché un token ID.
11. L'utente ottiene l'accesso alla risorsa dell'app.

Il diagramma seguente illustra tutti i componenti e i passaggi interessati.

![Flusso di accesso Single Sign-On facile in un'app nativa](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Passaggi successivi

- [**Guida introduttiva**](how-to-connect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Domande frequenti**](how-to-connect-sso-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](tshoot-connect-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
