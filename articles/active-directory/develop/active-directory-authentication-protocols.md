---
title: Protocolli di autenticazione di Azure Active Directory | Microsoft Docs
description: Panoramica dei protocolli di autenticazione supportati da Azure Active Directory (Azure AD).
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mtillman
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 1387ce5f3f16301786cf4111a081a5486788fa77
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolli di autenticazione di Azure Active Directory
Azure Active Directory (Azure AD) supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. Gli argomenti in questa sezione descrivono i protocolli supportati e l'implementazione di ciascuno in Azure AD. Gli argomenti includono una presentazione dei tipi di attestazione supportati, un'introduzione all'uso dei metadati di federazione, la documentazione di riferimento dettagliata dei protocolli OAuth 2.0. e SAML 2.0 e una sezione sulla risoluzione dei problemi.

## <a name="authentication-protocols-articles-and-reference"></a>Articoli e riferimenti sui protocolli di autenticazione
* [Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md) : informazioni sulla cadenza di rollover della chiave di firma di Azure AD, sulle modifiche che è possibile apportare per aggiornare automaticamente la chiave e su come aggiornare gli scenari più comuni per le applicazioni.
* [Token e tipi di attestazione supportati](active-directory-token-and-claims.md): informazioni sulle attestazioni nei token rilasciati da Azure AD.
* [Metadati di federazione](active-directory-federation-metadata.md) : informazioni su come trovare e interpretare i documenti dei metadati generati da Azure AD.
* [OAuth 2.0 in Azure AD](active-directory-protocols-oauth-code.md) : informazioni sull'implementazione di OAuth 2.0 in Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) : Informazioni su come usare OAuth 2.0, un protocollo di autenticazione, per l'autenticazione.
* [Chiamate da servizio a servizio con credenziali client](active-directory-protocols-oauth-service-to-service.md): informazioni su come usare il flusso di concessione delle credenziali client di OAuth 2.0 per le chiamate da servizio a servizio.
* [Chiamate da servizio a servizio con flusso on-behalf-of](active-directory-protocols-oauth-on-behalf-of.md): informazioni su come usare il flusso on-behalf-of di OAuth 2.0 per le chiamate da servizio a servizio.
* [Informazioni di riferimento sul protocollo SAML](active-directory-saml-protocol-reference.md) : informazioni sui profili SAML Single Sign-On e Single Sign-Out di Azure AD.

## <a name="see-also"></a>Vedere anche
[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Esempi di codice di Active Directory](active-directory-code-samples.md)
