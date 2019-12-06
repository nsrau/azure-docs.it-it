---
title: Protocolli di autenticazione di Azure Active Directory | Microsoft Docs
description: Panoramica dei protocolli di autenticazione supportati da Azure Active Directory (Azure AD).
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845417"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolli di autenticazione di Azure Active Directory

Azure Active Directory (Azure AD) supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. Gli argomenti in questa sezione descrivono i protocolli supportati e l'implementazione di ciascuno in Azure AD. Gli argomenti includono una presentazione dei tipi di attestazione supportati, un'introduzione all'uso dei metadati di federazione, la documentazione di riferimento dettagliata dei protocolli OAuth 2.0. e SAML 2.0 e una sezione sulla risoluzione dei problemi.

## <a name="authentication-protocols-articles-and-reference"></a>Articoli e informazioni di riferimento sui protocolli di autenticazione

* [Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md) : informazioni sulla cadenza di rollover della chiave di firma di Azure AD, sulle modifiche che è possibile apportare per aggiornare automaticamente la chiave e su come aggiornare gli scenari più comuni per le applicazioni.
* [Token e tipi di attestazione supportati](v1-id-and-access-tokens.md): informazioni sulle attestazioni nei token rilasciati da Azure AD.
* [Metadati di federazione](azure-ad-federation-metadata.md) : informazioni su come trovare e interpretare i documenti dei metadati generati da Azure AD.
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md) : informazioni sull'implementazione di OAuth 2.0 in Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) : Informazioni su come usare OAuth 2.0, un protocollo di autenticazione, per l'autenticazione.
* [Chiamate da servizio a servizio con credenziali client](v1-oauth2-client-creds-grant-flow.md): informazioni su come usare il flusso di concessione delle credenziali client di OAuth 2.0 per le chiamate da servizio a servizio.
* [Chiamate da servizio a servizio con flusso on-behalf-of](v1-oauth2-on-behalf-of-flow.md): informazioni su come usare il flusso on-behalf-of di OAuth 2.0 per le chiamate da servizio a servizio.
* [Informazioni di riferimento sul protocollo SAML](active-directory-saml-protocol-reference.md) : informazioni sui profili SAML Single Sign-On e Single Sign-Out di Azure AD.

## <a name="see-also"></a>Vedi anche

* [Guida per gli sviluppatori di Azure Active Directory](v1-overview.md)
* [Esempi di codice di Active Directory](sample-v1-code.md)
