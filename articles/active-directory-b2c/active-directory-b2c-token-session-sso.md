---
title: Configurazione di token, sessione e accesso Single Sign-On in Azure Active Directory B2C | Microsoft Docs
description: Configurazione di token, sessione e accesso Single Sign-On in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1acdb8b5d0054f1dffd1014a350540b6de40d75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171508"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configurazione di token, sessione e accesso Single Sign-On in Azure Active Directory B2C

Questa funzionalità offre un controllo dettagliato, in base ai [singoli flussi utente](active-directory-b2c-reference-policies.md), per gli elementi seguenti:

- Durate dei token di sicurezza emessi da Azure Active Directory (Azure AD) B2C.
- Durate delle sessioni delle applicazioni Web gestite da Azure AD B2C.
- Formati di attestazioni importanti nei token di sicurezza emessi da Azure AD B2C.
- Comportamento dell'accesso Single Sign-On (SSO) in più app e flussi utente nel tenant di Azure AD B2C.

Sebbene sia possibile usare questa funzionalità per qualsiasi tipo di criterio, questo esempio descrive come usare la funzionalità con un flusso utente di iscrizione o di accesso. Per i flussi utente, è possibile usare questa funzionalità nella directory Azure AD B2C come segue:

1. Fare clic su **Flussi utente**.
2. Aprire un flusso utente facendo clic su di esso. Ad esempio, fare clic su **B2C_1_SiUpIn**.
3. Fare clic su **Proprietà**.
4. In **Impostazioni di compatibilità del token** apportare le modifiche desiderate. Per informazioni sulle proprietà disponibili, vedere le sezioni successive.
5. Fare clic su **Salva** nella parte superiore del menu.

## <a name="token-lifetimes-configuration"></a>Configurazione delle durate dei token

Azure AD B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md) per abilitare l'accesso sicuro alle risorse protette. Per implementare questo supporto, Azure AD B2C emette diversi [token di sicurezza](active-directory-b2c-reference-tokens.md). 

Le proprietà seguenti vengono usate per gestire le durate dei token di sicurezza emessi da Azure AD B2C:

- **Durate dei token di accesso e ID (minuti)**: durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta.
    - Impostazione predefinita: 60 minuti.
    - Valore minimo (inclusivo): 5 minuti.
    - Valore massimo (inclusivo): 1440 minuti.
- **Durata del token di aggiornamento (giorni)**: periodo di tempo massimo prima che sia possibile usare un token di aggiornamento per acquisire un nuovo token di accesso o ID e, facoltativamente, un nuovo token di aggiornamento, se all'applicazione è stato concesso l'ambito `offline_access`.
    - Impostazione predefinita: 14 giorni.
    - Valore minimo (inclusivo): 1 giorno.
    - Valore massimo (inclusivo): 90 giorni.
- **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)**: allo scadere di questo periodo, l'utente dovrà eseguire di nuovo l'autenticazione, indipendentemente dal periodo di validità del token di accesso più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico.
    - Impostazione predefinita: 90 giorni.
    - Valore minimo (inclusivo): 1 giorno.
    - Valore massimo (inclusivo): 365 giorni.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Consentire a un utente di mantenere la connessione a un'applicazione mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile impostare l'opzione **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)** su **Non vincolato** nel flusso utente di accesso.
- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password. 

## <a name="token-compatibility-settings"></a>Impostazioni di compatibilità dei token

Le proprietà seguenti consentono ai clienti di acconsentire esplicitamente in base alle esigenze:

- **Attestazione autorità di certificazione (iss)**: identifica il tenant di Azure AD B2C che ha emesso il token.
    - `https://<domain>/{B2C tenant GUID}/v2.0/`: valore predefinito.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: valore che include gli ID per il tenant B2C e il flusso utente usato nella richiesta di token. Usare questo valore se l'app o la libreria richiede Azure AD B2C per essere conforme alla [specifica OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html).
- **Attestazione soggetto (sub)**: questa proprietà identifica l'entità per cui il token esegue l'asserzione delle informazioni.
    - **ObjectID**: questa proprietà è il valore predefinito che popola l'ID oggetto dell'utente nella directory dell'attestazione `sub` nel token.
    - **Non supportato**: questa proprietà viene fornita solo per compatibilità con le versioni precedenti ed è consigliabile passare a **ObjectID** non appena possibile.
- **Attestazione che rappresenta l'ID criteri**: questa proprietà identifica il tipo di attestazione in cui viene popolato l'ID criteri usato nella richiesta di token.
    - **tfp**: questa proprietà è il valore predefinito.
    - **acr**: questa proprietà viene fornita solo per la compatibilità con le versioni precedenti.

## <a name="session-behavior"></a>Comportamento della sessione

Azure AD B2C supporta il [protocollo di autenticazione OpenID Connect](active-directory-b2c-reference-oidc.md) per abilitare l'accesso sicuro alle applicazioni Web. È possibile usare le proprietà seguenti per gestire le sessioni delle applicazioni Web:

- **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
    - Impostazione predefinita: 1440 minuti.
    - Valore minimo (inclusivo): 15 minuti.
    - Valore massimo (inclusivo): 1440 minuti.
- **Timeout della sessione dell'app Web**: se questa opzione è impostata su **Assoluto**, l'utente dovrà ripetere l'autenticazione dopo la scadenza del periodo di tempo specificato da **Durata della sessione dell'app Web (minuti)**. Se l'opzione è impostata su **Continuo** (impostazione predefinita), l'utente rimane connesso, purché sia continuamente attivo nell'applicazione Web.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per la sessione dell'applicazione Web.
- Imporre l'autenticazione dopo un periodo di tempo specifico durante l'interazione di un utente con una parte a sicurezza elevata dell'applicazione Web. 

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

## <a name="single-sign-on-sso-configuration"></a>Configurazione dell'accesso Single Sign-On

Se il tenant di B2C include più applicazioni e flussi utente, è possibile gestire le interazioni degli utenti tra di essi usando la proprietà **Configurazione Single Sign-on**. È possibile impostare la proprietà su uno dei valori seguenti:

- **Tenant**: questa è l'impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e flussi utente del tenant di B2C di condividere la stessa sessione utente. Ad esempio, quando un utente accede a un'applicazione, può accedere contemporaneamente anche a un'altra applicazione, Contoso Pharmacy, senza alcun problema.
- **Applicazione**: questa impostazione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, un utente può accedere a Contoso Pharmacy, usando le stesse credenziali, anche se ha già eseguito l'accesso a Contoso Shopping, un'altra applicazione nello stesso tenant di B2C. 
- **Criterio**: questa impostazione consente di mantenere una sessione utente esclusivamente per un flusso utente, indipendentemente dalle applicazioni che lo usano. Ad esempio, se un utente ha già effettuato l'accesso e completato un passaggio di autenticazione a più fattori (MFA), otterrà l'accesso a parti a sicurezza più elevata di più applicazioni, purché la sessione associata al flusso utente non scada.
- **Disabilitato**: questa impostazione impone all'utente di eseguire il percorso utente completo a ogni esecuzione del flusso utente. Ad esempio, ciò consente a più utenti di accedere all'applicazione, in uno scenario di desktop condiviso, anche se un singolo utente rimane connesso per l'intero periodo di tempo.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password. 

