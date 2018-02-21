---
title: Informazioni di riferimento sul protocollo SAML in Azure AD | Microsoft Docs
description: Questo articolo offre una panoramica dei profili SAML Single Sign-On e Single Sign-Out in Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Uso del protocollo SAML in Azure Active Directory
Azure Active Directory (Azure AD) usa il protocollo SAML 2.0 per consentire alle applicazioni di offrire agli utenti un'esperienza di accesso Single Sign-On. I profili SAML [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) e [Single Sign-Out](active-directory-single-sign-out-protocol-reference.md) di Azure AD specificano come vengono usati i protocolli, le associazioni e le asserzioni SAML nel servizio del provider di identità.

Il protocollo SAML richiede che il provider di identità (Azure AD) e il provider del servizio (l'applicazione) si scambino informazioni su se stessi.

Quando un'applicazione viene registrata in Azure AD, lo sviluppatore dell'app registra le informazioni relative alla federazione con Azure AD. È incluso l'**URI di reindirizzamento** dell'applicazione.

Azure Active Directory espone endpoint di Single Sign-On e Single Sign-Out specifici del tenant e comuni (indipendenti dal tenant). Questi URL non sono semplici identificatori, ma rappresentano posizioni indirizzabili. È quindi possibile accedere all'endpoint per leggere i metadati.

* L'endpoint specifico del tenant si trova all'indirizzo `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Il segnaposto <TenantDomainName> rappresenta un nome di dominio registrato o un GUID TenantID di un tenant di Azure AD. Ad esempio, i metadati di federazione del tenant contoso.com si trovano all'indirizzo: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* L'endpoint indipendente dal tenant si trova all'indirizzo `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Questo indirizzo dell'endpoint contiene **common** anziché l'ID o il nome di un domino tenant.

Per informazioni sui documenti di metadati della federazione pubblicati da Azure AD, vedere [Metadati della federazione](active-directory-federation-metadata.md).
