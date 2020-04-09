---
title: Protocolli di autenticazione della piattaforma di identità Microsoft
description: Panoramica dei protocolli di autenticazione supportati dalla piattaforma di identità Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884682"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolli di autenticazione della piattaforma di identità Microsoft

La piattaforma di identità Microsoft supporta alcuni dei protocolli di autenticazione e autorizzazione più utilizzati. Negli argomenti di questa sezione vengono descritti i protocolli supportati e la relativa implementazione nella piattaforma di identità Microsoft. Gli argomenti includono una presentazione dei tipi di attestazione supportati, un'introduzione all'uso dei metadati di federazione, la documentazione di riferimento dettagliata dei protocolli OAuth 2.0. e SAML 2.0 e una sezione sulla risoluzione dei problemi.

## <a name="authentication-protocols-articles-and-reference"></a>Articoli e riferimenti sui protocolli di autenticazione

* [Informazioni importanti sulla firma del rollover](active-directory-signing-key-rollover.md) della chiave nella piattaforma di identità Microsoft: informazioni sulla cadenza di rollover della chiave di firma della piattaforma di identità Microsoft, sulle modifiche che è possibile apportare per aggiornare automaticamente la chiave e sulla discussione su come aggiornare gli scenari applicativi più comuni.
* Token supportati e tipi di attestazione: informazioni sulle attestazioni nei token [emastati](id-tokens.md) dalla piattaforma di identità Microsoft.Supported Token and Claim Types - Learn about the claims in the tokens that Microsoft identity platform issues.
* [OAuth 2.0 nella piattaforma](v2-oauth2-auth-code-flow.md) di identità Microsoft : informazioni sull'implementazione di OAuth 2.0 nella piattaforma di identità Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) : Informazioni su come usare OAuth 2.0, un protocollo di autenticazione, per l'autenticazione.
* [Chiamate da servizio a servizio con credenziali client](v2-oauth2-client-creds-grant-flow.md): informazioni su come usare il flusso di concessione delle credenziali client di OAuth 2.0 per le chiamate da servizio a servizio.
* [Chiamate da servizio a servizio con flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md): informazioni su come usare il flusso on-behalf-of di OAuth 2.0 per le chiamate da servizio a servizio.
* [Guida di riferimento al protocollo SAML:](active-directory-saml-protocol-reference.md) informazioni sui profili SAML Single Sign-On e Single Sign-Out della piattaforma di identità Microsoft.

## <a name="see-also"></a>Vedere anche

* [Panoramica della piattaforma di identità Microsoft](v2-overview.md)
* [Esempi di codice di Active Directory](sample-v2-code.md)
