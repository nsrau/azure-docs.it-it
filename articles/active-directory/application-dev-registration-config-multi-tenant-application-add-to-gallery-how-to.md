---
title: Come aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD | Microsoft Docs
description: Viene illustrato come inserire l'applicazione multi-tenant sviluppata personalizzata nella raccolta di applicazioni di AD Azure
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Come aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Definizione della raccolta di applicazioni di Azure AD

Azure AD è un servizio di identità basato sul cloud. La [raccolta di app di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) è un archivio comune in cui vengono pubblicati tutti i connettori di applicazioni per l'accesso Single Sign-On e il provisioning degli utenti. I clienti che usano Azure AD come provider di identità hanno bisogno di connettori di applicazioni SaaS diversi, pubblicati in questa raccolta. L'amministratore IT aggiunge il connettore dalla raccolta di app e lo configura e usa per l'accesso Single Sign-On e il provisioning. Azure AD supporta tutti i principali protocolli di federazione come SAML 2.0, OpenID Connect, OAuth e WS-Fed per l'accesso Single Sign-On. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se l'applicazione supporta SAML o OpenIDConnect
Se si dispone di un'applicazione multi-tenant che si vuole inserire nella raccolta di applicazioni di Azure AD, è prima necessario assicurarsi che l'applicazione supporti una delle tecnologie seguenti Single Sign-On:

1. **OpenID Connect**: creare l'applicazione multi-tenant in Azure AD e implementare il [framework di consenso di Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) per l'applicazione. Inviare la richiesta di accesso all'endpoint comune in modo che qualsiasi cliente possa fornire il consenso all'applicazione. È possibile controllare l'accesso utente dei clienti in base all'ID del tenant e all'UPN dell'utente ricevuti nel token. Inviare l'applicazione come descritto in questo [articolo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML**: se l'applicazione supporta SAML 2.0, può essere inclusa nella raccolta, come descritto nelle istruzioni disponibili [qui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

Se l'applicazione supporta una di queste modalità Single Sign-On e si intende inserirla nella raccolta di applicazioni multi-tenant di Azure AD, è possibile eseguire la procedura indicata in [questo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) articolo. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se l'applicazione non supporta SAML o OpenIDConnect
Anche se l'applicazione non supporta una di queste modalità, è comunque possibile integrarla nella raccolta usando la tecnologia Single Sign-On tramite password.

**SSO password**: creare un'applicazione Web con una pagina di accesso HTML per configurare l[accesso Single Sign-On basato su password](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. Risulta utile anche negli scenari in cui più utenti devono condividere un unico account, ad esempio agli account di app di social media dell'organizzazione. 

Se si vuole che l'applicazione venga inclusa con questa tecnologia, inviare la richiesta come descritto in [questo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) articolo.

## <a name="escalations"></a>Escalation

Per eventuali escalation, inviare un messaggio di posta elettronica al [team di integrazione SSO di Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) per essere contattati il più presto possibile.

## <a name="next-steps"></a>Passaggi successivi
[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
