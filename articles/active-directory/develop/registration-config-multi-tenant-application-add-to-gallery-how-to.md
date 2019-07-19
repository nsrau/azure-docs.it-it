---
title: Aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD | Microsoft Docs
description: Viene illustrato come inserire l'applicazione multi-tenant sviluppata personalizzata nella raccolta di applicazioni di AD Azure.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320924"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Definizione della raccolta di applicazioni di Azure AD

Azure Active Directory (Azure AD) è un servizio per la gestione delle identità basato sul cloud. La [raccolta di applicazioni di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) si trova nell'app store Azure Marketplace, in cui vengono pubblicati tutti i connettori di applicazioni per l'accesso Single Sign-On e il provisioning degli utenti. I clienti che usano Azure AD come provider di identità possono trovare i diversi connettori di applicazioni SaaS pubblicati qui. Gli amministratori IT aggiungono i connettori dalla raccolta di app e li configurano e usano per l'accesso Single Sign-On e il provisioning. Azure AD supporta tutti i principali protocolli di federazione per l'accesso Single Sign-On, come SAML 2.0, OpenID Connect, OAuth e WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se l'applicazione supporta SAML o OpenIDConnect
Se si dispone di un'applicazione multi-tenant che si vuole inserire nella raccolta di applicazioni di Azure AD, è prima necessario assicurarsi che l'applicazione supporti una delle tecnologie seguenti Single Sign-On:

- **OpenID Connect**: per includere l'app nell'elenco, creare l'applicazione multi-tenant in Azure AD e implementare il [framework di consenso di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) per l'applicazione. Inviare la richiesta di accesso all'endpoint comune, in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente in base all'ID del tenant e all'UPN dell'utente ricevuti nel token. Inviare l'applicazione usando il processo descritto in [Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: se l'applicazione supporta SAML 2.0, l'app può essere inclusa nella raccolta. Attenersi alle istruzioni descritte in [Inserimento dell'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se l'applicazione non supporta SAML o OpenIDConnect
Le applicazioni che non supportano SAML o OpenIDConnect possono comunque essere integrate in una raccolta di app tramite la tecnologia Single Sign-On basata su password.

La tecnologia Single Sign-On basata su password, definita anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

Per inserire l'applicazione nella raccolta tramite questa tecnologia:
1. Creare un'applicazione Web con una pagina di accesso HTML per configurare l'[accesso Single Sign-On basato su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Inviare la richiesta come descritto in [Inserimento dell'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalation

Per le escalation, inviare un messaggio di posta elettronica al [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) che fornirà assistenza nel più breve tempo possibile.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [inserire l'applicazione nella raccolta di applicazioni Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
