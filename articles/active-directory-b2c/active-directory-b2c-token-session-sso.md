---
title: Configurazione di sessione e single sign-on - Azure Active Directory B2C | Microsoft Docs
description: Sessione e configurazione single sign-on in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 472e494d445ae4a22c988076994649543ec1f7f8
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508126"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configurazione di sessione e single sign-on in Azure Active Directory B2C

Questa funzionalità offre un controllo dettagliato, in base ai [singoli flussi utente](active-directory-b2c-reference-policies.md), per gli elementi seguenti:

- Durate delle sessioni delle applicazioni Web gestite da Azure AD B2C.
- Comportamento dell'accesso Single Sign-On (SSO) in più app e flussi utente nel tenant di Azure AD B2C.

## <a name="session-behavior"></a>Comportamento della sessione

Azure AD B2C supporta il [protocollo di autenticazione OpenID Connect](active-directory-b2c-reference-oidc.md) per abilitare l'accesso sicuro alle applicazioni Web. È possibile usare le proprietà seguenti per gestire le sessioni delle applicazioni Web:

- **Durata della sessione dell'app Web (minuti)** : durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
    - Impostazione predefinita: 1440 minuti.
    - Valore minimo (inclusivo): 15 minuti.
    - Valore massimo (inclusivo): 1440 minuti.
- **Timeout della sessione dell'app Web**: se questa opzione è impostata su **Assoluto**, l'utente dovrà ripetere l'autenticazione dopo la scadenza del periodo di tempo specificato da **Durata della sessione dell'app Web (minuti)** . Se l'opzione è impostata su **Continuo** (impostazione predefinita), l'utente rimane connesso, purché sia continuamente attivo nell'applicazione Web.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per la sessione dell'applicazione Web.
- Imporre l'autenticazione dopo un periodo di tempo specifico durante l'interazione di un utente con una parte a sicurezza elevata dell'applicazione Web. 

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

## <a name="single-sign-on-sso-configuration"></a>Configurazione dell'accesso Single Sign-On

Se il tenant di B2C include più applicazioni e flussi utente, è possibile gestire le interazioni degli utenti tra di essi usando la proprietà **Configurazione Single Sign-on**. È possibile impostare la proprietà su uno dei valori seguenti:

- **Tenant**: questa è l'impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e flussi utente del tenant di B2C di condividere la stessa sessione utente. Ad esempio, quando un utente accede a un'applicazione, può accedere contemporaneamente anche a un'altra applicazione, Contoso Pharmacy, senza alcun problema.
- **Applicazione**: questa impostazione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, un utente può accedere a Contoso Pharmacy, usando le stesse credenziali, anche se ha già eseguito l'accesso a Contoso Shopping, un'altra applicazione nello stesso tenant di B2C. 
- **Criterio**: questa impostazione consente di mantenere una sessione utente esclusivamente per un flusso utente, indipendentemente dalle applicazioni che lo usano. Ad esempio, se un utente ha già effettuato l'accesso e completato un passaggio di autenticazione a più fattori (MFA), otterrà l'accesso a parti a sicurezza più elevata di più applicazioni, purché la sessione associata al flusso utente non scada.
- **Disabilitato**: questa impostazione impone all'utente di eseguire il percorso utente completo a ogni esecuzione del flusso utente.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password. 

