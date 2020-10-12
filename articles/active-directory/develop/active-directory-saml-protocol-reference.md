---
title: Modalità di utilizzo del protocollo SAML da parte della piattaforma Microsoft Identity
description: Questo articolo offre una panoramica dei profili SAML Single Sign-On e Single Sign-Out in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552799"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Modalità di utilizzo del protocollo SAML da parte della piattaforma Microsoft Identity

Microsoft Identity Platform usa il protocollo SAML 2,0 per consentire alle applicazioni di fornire agli utenti un'esperienza Single Sign-On. I profili SAML [Single Sign-on](single-sign-on-saml-protocol.md) e [Single Sign-out](single-sign-out-saml-protocol.md) di Azure ad illustrano il modo in cui le asserzioni SAML, i protocolli e le associazioni vengono usati nel servizio del provider di identità.

Il protocollo SAML richiede che il provider di identità (piattaforma di identità Microsoft) e il provider di servizi (l'applicazione) scambino informazioni relative a se stessi.

Quando un'applicazione viene registrata in Azure AD, lo sviluppatore dell'app registra le informazioni relative alla federazione con Azure AD. Tali informazioni includono l'**URI di reindirizzamento** e l'**URI dei metadati** dell'applicazione.

Microsoft Identity Platform usa l' **URI dei metadati** del servizio cloud per recuperare la chiave di firma e l'URI di disconnessione. Il cliente può aprire l'app in **Azure AD -> Registrazione per l'app** e poi in **Impostazioni -> Proprietà** può aggiornare l'URL di disconnessione. In questo modo, la piattaforma Microsoft Identity può inviare la risposta all'URL corretto. 

Azure Active Directory espone endpoint di Single Sign-On e Single Sign-Out specifici del tenant e comuni (indipendenti dal tenant). Questi URL non sono semplici identificatori, ma rappresentano posizioni indirizzabili. È quindi possibile accedere all'endpoint per leggere i metadati.

* L'endpoint specifico del tenant si trova all'indirizzo `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. Il *\<TenantDomainName>* segnaposto rappresenta un nome di dominio registrato o un GUID TenantId di un tenant Azure ad. Ad esempio, i metadati di federazione del tenant contoso.com sono in: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* L'endpoint indipendente dal tenant si trova all'indirizzo `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Questo indirizzo dell'endpoint contiene **common** anziché l'ID o il nome di un domino tenant.

Per informazioni sui documenti di metadati della Federazione che Azure AD pubblica, vedere [metadati federativi](../azuread-dev/azure-ad-federation-metadata.md).
