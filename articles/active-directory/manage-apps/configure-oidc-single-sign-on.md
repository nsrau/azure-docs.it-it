---
title: Informazioni sulle Single Sign-On basate su OIDC (SSO) per le app in Azure Active Directory
description: Informazioni sulle Single Sign-On basate su OIDC (SSO) per le app in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.openlocfilehash: 825f79b0a1c132fb7a15d643c3487dfb7d6a9abd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210379"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informazioni Single Sign-On basate su OIDC
Nella [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure ad come provider di identità (IDP) per un'applicazione. Questo articolo illustra in modo più dettagliato le app che usano lo standard OpenID Connect per implementare Single Sign-On. 

## <a name="before-you-begin"></a>Prima di iniziare
Il processo di aggiunta di un'app al tenant di Azure Active Directory dipende dal tipo di Single Sign-On implementata dall'applicazione. Per altre informazioni sulle opzioni di Single Sign-On disponibili per le app che possono usare Azure AD per la gestione delle identità, vedere [Opzioni di Single Sign-on](sso-options.md). Questo articolo illustra le app basate su OIDC.


## <a name="basic-oidc-configuration"></a>Configurazione OIDC di base
Nella [serie di guide introduttive](add-application-portal-setup-oidc-sso.md)è disponibile un articolo sulla configurazione di Single Sign-on. Si apprenderà come aggiungere un'app basata su OIDC al tenant di Azure.

L'aspetto interessante dell'aggiunta di un'app che usa lo standard OIDC per Single Sign-On è che la configurazione è minima. Ecco un breve video che illustra come aggiungere un'app basata su OIDC al tenant.

Aggiunta di un'app basata su OIDC in Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/4kv-upsZCI0]

## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](add-application-portal-setup-oidc-sso.md)
- [Opzioni di accesso Single Sign-On](sso-options.md)
- [Procedura: Consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md)