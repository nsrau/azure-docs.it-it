---
title: Modalità di utilizzo del protocollo SAML da parte di Azure AD
description: Questo articolo offre una panoramica dei profili SAML Single Sign-On e Single Sign-Out in Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 44d06030d8015d2df9499ce903eb9cb06e1ef27a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885651"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Modalità di utilizzo del protocollo SAML da parte di Azure AD

Azure Active Directory (Azure AD) usa il protocollo SAML 2.0 per consentire alle applicazioni di offrire agli utenti un'esperienza di accesso Single Sign-On. I profili SAML [Single Sign-on](single-sign-on-saml-protocol.md) e [Single Sign-out](single-sign-out-saml-protocol.md) di Azure ad illustrano il modo in cui le asserzioni SAML, i protocolli e le associazioni vengono usati nel servizio del provider di identità.

Il protocollo SAML richiede che il provider di identità (Azure AD) e il provider del servizio (l'applicazione) si scambino informazioni su se stessi.

Quando un'applicazione viene registrata in Azure AD, lo sviluppatore dell'app registra le informazioni relative alla federazione con Azure AD. Tali informazioni includono l'**URI di reindirizzamento** e l'**URI dei metadati** dell'applicazione.

Azure AD usa l'**URI dei metadati** del servizio cloud per recuperare la chiave di firma e l'URI di disconnessione. Il cliente può aprire l'app in **Azure AD -> Registrazione per l'app** e poi in **Impostazioni -> Proprietà** può aggiornare l'URL di disconnessione. In questo modo Azure AD può inviare la risposta all'URL corretto. 

Azure Active Directory espone endpoint di Single Sign-On e Single Sign-Out specifici del tenant e comuni (indipendenti dal tenant). Questi URL non sono semplici identificatori, ma rappresentano posizioni indirizzabili. È quindi possibile accedere all'endpoint per leggere i metadati.

* L'endpoint specifico del tenant si trova all'indirizzo `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. Il segnaposto * \<>TenantDomainName* rappresenta un nome di dominio registrato o un GUID TenantId di un tenant di Azure ad. Ad esempio, i metadati di federazione del tenant contoso.com sono in: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* L'endpoint indipendente dal tenant si trova all'indirizzo `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Questo indirizzo dell'endpoint contiene **common** anziché l'ID o il nome di un domino tenant.

Per informazioni sui documenti di metadati della Federazione che Azure AD pubblica, vedere [metadati federativi](../azuread-dev/azure-ad-federation-metadata.md).
