---
title: 'Azure Active Directory B2C: configurazione di token, sessione e accesso Single Sign-On | Documentazione Microsoft'
description: Configurazione di token, sessione e accesso Single Sign-On in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 9cc0081588f54f77a69ded336d063651b12c8dd8
ms.openlocfilehash: a185e802a2713c6b6d4101477f0fc61bca0bf29c


---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Configurazione di token, sessione e accesso Single Sign-On
Questa funzionalità offre un controllo dettagliato, in base ai [singoli criteri](active-directory-b2c-reference-policies.md), per gli elementi seguenti:

1. Durate dei token di sicurezza emessi da Azure Active Directory (Azure AD) B2C.
2. Durate delle sessioni delle applicazioni Web gestite da Azure AD B2C.
3. Formati di attestazioni importanti nei token di sicurezza emessi da Azure AD B2C.
4. Comportamento dell'accesso Single Sign-On (SSO) in più app e criteri nel tenant di B2C.

È possibile usare questa funzionalità nel tenant di B2C come indicato di seguito:

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Fare clic su **Criteri di accesso**. *Nota: è possibile usare questa funzionalità su qualsiasi tipo di criterio, non solo per i **Criteri di accesso***.
3. Fare clic su un criterio per aprirlo. Ad esempio, fare clic su **B2C_1_SiIn**.
4. Fare clic su **Modifica** nella parte superiore del pannello.
5. Fare clic su **Token, session & single sign-on config** (Configurazione di token, sessione e accesso Single Sign-On).
6. Apportare le modifiche necessarie. Per informazioni sulle proprietà disponibili, vedere le sezioni successive.
7. Fare clic su **OK**.
8. Fare clic su **Salva** nella parte superiore del pannello.

## <a name="token-lifetimes-configuration"></a>Configurazione delle durate dei token
Azure AD B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md) per abilitare l'accesso sicuro alle risorse protette. Per implementare questo supporto, Azure AD B2C emette diversi [token di sicurezza](active-directory-b2c-reference-tokens.md). Queste sono le proprietà che è possibile usare per gestire le durate dei token di sicurezza emessi da Azure AD B2C:

* **Durate dei token di accesso e ID (minuti)**: durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Azure AD B2C emette attualmente solo token ID. Questo valore verrà applicato anche ai token di accesso, quando verrà aggiunto il supporto per questo tipo di token.
  * Impostazione predefinita: 60 minuti.
  * Valore minimo (inclusivo): 5 minuti.
  * Valore massimo (inclusivo): 1440 minuti.
* **Durata del token di aggiornamento (giorni)**: periodo di tempo massimo prima che sia possibile usare un token di aggiornamento per acquisire un nuovo token di accesso o ID e, facoltativamente, un nuovo token di aggiornamento, se all'applicazione è stato concesso l'ambito `offline_access`.
  * Impostazione predefinita: 14 giorni.
  * Valore minimo (inclusivo): 1 giorno.
  * Valore massimo (inclusivo): 90 giorni.
* **Durata della finestra temporale scorrevole del token di aggiornamento (giorni)**: allo scadere di questo periodo, l'utente dovrà ripetere l'autenticazione, indipendentemente dal periodo di validità del token di accesso più recente acquisito dall'applicazione. Questo valore può essere specificato solo se l'opzione è impostata su **Con vincoli**. Deve essere superiore o uguale al valore **Durata del token di aggiornamento (giorni)** . Se l'opzione è impostata su **Senza vincoli**, non sarà possibile specificare un valore specifico.
  * Impostazione predefinita: 90 giorni.
  * Valore minimo (inclusivo): 1 giorno.
  * Valore massimo (inclusivo): 365 giorni.

Ecco un paio di casi di utilizzo in cui è possibile abilitare l'uso di queste proprietà:

* Consentire a un utente di mantenere la connessione a un dispositivo mobile per un periodo illimitato, purché l'utente sia continuamente attivo sull'applicazione. È possibile ottenere questo risultato impostando l'opzione **Refresh token sliding window lifetime (days)** (Durata della finestra temporale scorrevole del token di aggiornamento (giorni)) su **Unbounded** (Non vincolato) nei criteri di accesso.
* Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per i token di accesso.

## <a name="token-compatibility-settings"></a>Impostazioni di compatibilità dei token
Sono state apportate modifiche alla formattazione di attestazioni importanti nei token di sicurezza emessi da Azure AD B2C, allo scopo di migliorare il supporto del protocollo Standard e l'interoperabilità con librerie di identità di terze parti. Tuttavia, per evitare di interferire con le app esistenti, sono state create le proprietà seguenti per consentire ai clienti di dare il consenso esplicito in base alle esigenze:

* **Attestazione dell'autorità di certificazione (iss)**: identifica il tenant Azure AD B2C che ha emesso il token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: valore predefinito.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: valore che include gli ID per il tenant B2C e i criteri usati nella richiesta di token. Usare questo valore se l'app o la libreria richiede Azure AD B2C per essere conforme alla [specifica OpenID Connect Discovery 1.0](http://openid.net/specs/openid-connect-discovery-1_0.html).
* **Attestazione dell'oggetto (sub)**: identifica l'entità, ossia l'utente, per cui il token rilascia informazioni.
  * **ObjectID**: valore predefinito. che popola l'ID oggetto dell'utente nella directory dell'attestazione `sub` nel token.
  * **Non supportato**: viene fornito solo per compatibilità con le versioni precedenti ed è consigliabile passare a **ObjectID** non appena possibile.
* **Attestazione che rappresenta l'ID criteri**: identifica il tipo di attestazione in cui viene popolato l'ID criteri usato nella richiesta di token.
  * **tfp**: valore predefinito.
  * **acr**: viene fornito solo per compatibilità con le versioni precedenti ed è consigliabile passare a `tfp` non appena possibile.

## <a name="session-behavior"></a>Comportamento della sessione
Azure AD B2C supporta il [protocollo di autenticazione OpenID Connect](active-directory-b2c-reference-oidc.md) per abilitare l'accesso sicuro alle applicazioni Web. Ecco le proprietà che è possibile usare per gestire le sessioni delle applicazioni Web:

* **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
  * Impostazione predefinita: 1440 minuti.
  * Valore minimo (inclusivo): 15 minuti.
  * Valore massimo (inclusivo): 1440 minuti.
* **Web app session timeout** (Timeout della sessione dell'app Web): se questa opzione è impostata su **Absolute** (Assoluto), l'utente dovrà ripetere l'autenticazione dopo la scadenza del periodo di tempo specificato da **Web app session lifetime (minutes)** (Durata della sessione dell'app Web (minuti)). Se l'opzione è impostata su **Continuo** (impostazione predefinita), l'utente rimane connesso, purché sia continuamente attivo nell'applicazione Web.

Ecco un paio di casi di utilizzo in cui è possibile abilitare l'uso di queste proprietà:

* Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per la sessione dell'applicazione Web.
* Imporre la ripetizione dell'autenticazione dopo un periodo di tempo specifico durante l'interazione di un utente con una parte a sicurezza elevata dell'applicazione Web. 

## <a name="single-sign-on-sso-configuration"></a>Configurazione dell'accesso Single Sign-On
Se il tenant di B2C include più applicazioni e criteri, è possibile gestire le interazioni degli utenti tra di essi usando la proprietà **Configurazione Single Sign-on** . È possibile impostare la proprietà su uno dei valori seguenti:

* **Tenant**: impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e criteri del tenant di B2C di condividere la stessa sessione utente. Ad esempio, quando un utente accede a un'applicazione, Contoso Shopping, può accedere contemporaneamente anche a un'altra applicazione, Contoso Pharmacy, senza alcun problema.
* **Applicazione**: consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, un utente può accedere a Contoso Pharmacy, usando le stesse credenziali, anche se ha già eseguito l'accesso a Contoso Shopping, un'altra applicazione nello stesso tenant di B2C. 
* **Criterio**: consente di mantenere una sessione utente esclusivamente per un criterio, indipendentemente dalle applicazioni che lo usano. Ad esempio, se un utente ha già effettuato l'accesso e completato un passaggio di Multi Factor Authentication, otterrà l'accesso a parti a sicurezza più elevata di più applicazioni, purché la sessione associata al criterio non scada.
* **Disabilitato**: impone all'utente di eseguire il percorso utente completo a ogni esecuzione del criterio. Ad esempio, ciò consente a più utenti di accedere all'applicazione, in uno scenario di desktop condiviso, anche se un singolo utente rimane connesso per l'intero periodo di tempo.




<!--HONumber=Jan17_HO1-->


