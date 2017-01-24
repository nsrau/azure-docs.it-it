---
title: Protocolli di autenticazione di Azure Active Directory | Documentazione Microsoft
description: Panoramica dei protocolli di autenticazione supportati da Azure Active Directory (Azure AD).
documentationcenter: dev-center-name
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0b035ad1505e45c8c0820c825ff609df6e6100f0
ms.openlocfilehash: 20642b0c1864cede5326e98f6ce3145314acc49a


---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolli di autenticazione di Azure Active Directory
Azure Active Directory (Azure AD) supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. Gli argomenti in questa sezione descrivono i protocolli supportati e l'implementazione di ciascuno in Azure AD. Gli argomenti includono una presentazione dei tipi di attestazione supportati, un'introduzione all'uso dei metadati di federazione, la documentazione di riferimento dettagliata dei protocolli OAuth 2.0. e SAML 2.0 e una sezione sulla risoluzione dei problemi.

## <a name="authentication-protocols-articles-and-reference"></a>Articoli e riferimenti sui protocolli di autenticazione
* [Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md) : informazioni sulla cadenza di rollover della chiave di firma di Azure AD, sulle modifiche che è possibile apportare per aggiornare automaticamente la chiave e su come aggiornare gli scenari più comuni per le applicazioni.
* [Token e tipi di attestazione supportati](active-directory-token-and-claims.md) : informazioni sulle attestazioni nei token rilasciati da Azure AD.
* [Metadati di federazione](https://msdn.microsoft.com/library/azure/dn195592.aspx) : informazioni su come trovare e interpretare i documenti dei metadati generati da Azure AD.
* [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) : informazioni sull'implementazione di OAuth 2.0 in Azure AD.
* [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) : Informazioni su come usare OAuth 2.0, un protocollo di autenticazione, per l'autenticazione.
* [Informazioni di riferimento sul protocollo SAML](https://msdn.microsoft.com/library/azure/dn195591.aspx) : informazioni sui profili SAML Single Sign-On e Single Sign-Out di Azure AD.
* [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) : informazioni su WS-Federation 1.2 in Azure AD.
* [Risoluzione dei problemi dei protocolli di autenticazione](https://msdn.microsoft.com/library/azure/dn195584.aspx) : informazioni su come prevenire i problemi e interpretare e risolvere gli errori quando si usa Azure AD.

## <a name="see-also"></a>Vedere anche
[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Uso di Azure AD per l'autenticazione](../../app-service-web/web-sites-authentication-authorization.md)

[Esempi di codice di Active Directory](active-directory-code-samples.md)




<!--HONumber=Jan17_HO3-->


