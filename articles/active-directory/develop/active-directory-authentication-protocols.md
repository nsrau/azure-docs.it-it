---
title: Protocolli di autenticazione della piattaforma di identità Microsoft | Microsoft Docs
description: Panoramica dei protocolli di autenticazione supportati dalla piattaforma di identità Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699292"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolli di autenticazione della piattaforma Microsoft Identity

Microsoft Identity Platform supporta diversi protocolli di autenticazione e autorizzazione più diffusi. Negli argomenti di questa sezione vengono descritti i protocolli supportati e la relativa implementazione nella piattaforma di identità Microsoft. Gli argomenti includono una presentazione dei tipi di attestazione supportati, un'introduzione all'uso dei metadati di federazione, la documentazione di riferimento dettagliata dei protocolli OAuth 2.0. e SAML 2.0 e una sezione sulla risoluzione dei problemi.

## <a name="authentication-protocols-articles-and-reference"></a>Articoli e informazioni di riferimento sui protocolli di autenticazione

* [Informazioni importanti sul rollover della chiave di firma nella piattaforma di identità Microsoft](active-directory-signing-key-rollover.md) : informazioni sulla cadenza di rollover della chiave di firma della piattaforma di identità Microsoft, sulle modifiche che è possibile apportare per aggiornare automaticamente la chiave e su come aggiornare gli scenari di applicazione più comuni.
* [Token e tipi di attestazione supportati](id-tokens.md) : informazioni sulle attestazioni nei token che la piattaforma di identità Microsoft rilascia.
* [Oauth 2,0 nella piattaforma di identità Microsoft](v2-oauth2-auth-code-flow.md) : informazioni sull'implementazione di OAuth 2,0 nella piattaforma di identità Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) : Informazioni su come usare OAuth 2.0, un protocollo di autenticazione, per l'autenticazione.
* [Chiamate da servizio a servizio con credenziali client](v2-oauth2-client-creds-grant-flow.md): informazioni su come usare il flusso di concessione delle credenziali client di OAuth 2.0 per le chiamate da servizio a servizio.
* [Chiamate da servizio a servizio con flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md): informazioni su come usare il flusso on-behalf-of di OAuth 2.0 per le chiamate da servizio a servizio.
* Informazioni di [riferimento sul protocollo SAML](active-directory-saml-protocol-reference.md) : informazioni sui profili SAML Single Sign-on e Single Sign-out della piattaforma di identità Microsoft.

## <a name="see-also"></a>Vedi anche

* [Panoramica della piattaforma di identità Microsoft](v2-overview.md)
* [Esempi di codice di Active Directory](sample-v2-code.md)
