---
title: Uso di Azure AD per le applicazioni non elencate nella raccolta di app
description: Informazioni su come integrare le app non elencate nella raccolta di Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3483701d34b8fef5b40a411e22b33a73db2de44b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658741"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Uso di Azure AD per le applicazioni non elencate nella raccolta di app

Nella Guida introduttiva per l' [aggiunta di un'app](add-application-portal.md) si apprenderà come aggiungere un'app al tenant di Azure ad.

Oltre alle opzioni disponibili nella [raccolta di applicazioni di Azure AD](../saas-apps/tutorial-list.md), è possibile aggiungere un'**applicazione non della raccolta**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Funzionalità per le app non elencate nella raccolta di Azure AD

È possibile aggiungere qualsiasi applicazione che esiste già nell'organizzazione o qualsiasi applicazione di terze parti di un fornitore che non fa parte della raccolta di Azure AD. A seconda del [contratto di licenza](https://azure.microsoft.com/pricing/details/active-directory/) stipulato, sono disponibili le funzionalità seguenti:

- Integrazione self-service di qualsiasi applicazione che supporta i provider di identità [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) (avviata dal provider di servizi o dal provider di identità)
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](sso-options.md#password-based-sso)
- Connessione self-service di applicazioni che usano il [protocollo SCIM (System for Cross-domain Identity Management) per il provisioning utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell' [utilità di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) o [app personali](sso-options.md#linked-sign-on)

Per informazioni aggiuntive per gli sviluppatori su come integrare app personalizzate con Azure AD, vedere [scenari di autenticazione per Azure ad](../develop/authentication-vs-authorization.md). Quando si sviluppa un'app che usa un protocollo moderno, ad esempio [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) per autenticare gli utenti, è possibile registrarlo con Microsoft Identity Platform usando l'esperienza [Registrazioni app](../develop/quickstart-register-app.md) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide introduttive sulla gestione delle app](view-applications-portal.md)