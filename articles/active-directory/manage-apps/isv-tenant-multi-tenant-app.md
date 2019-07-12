---
title: Creare un tenant di Azure per un'applicazione multi-tenant
description: Linee guida per fornitori di software indipendenti per l'integrazione con Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659580"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Creare un tenant di Azure per un'applicazione multi-tenant  

Per fornire l'accesso all'applicazione multi-tenant è necessario creare un tenant di Azure Active Directory per registrare l'applicazione e abilitare la federazione delle identità del cliente. Visualizzare [scelto il protocollo di federazione corretto per l'applicazione multi-tenant](isv-choose-multi-tenant-federation.md). Questo tenant consentirà di eseguire il test dell'applicazione e la federazione in un ambiente che è simile per gli ambienti di Azure AD ai clienti.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Costi di hosting di un'applicazione multi-tenant

Azure Active Directory è disponibile in tre SKU, gratuito, Basic e Premium. [Vedere il confronto dettagliato delle funzionalità](https://azure.microsoft.com/pricing/details/active-directory/).

È possibile creare gratuitamente la sottoscrizione di Azure e Azure active directory e usare funzionalità di base.

## <a name="create-your-tenant"></a>Creare il tenant

1. Creare il Tenant. Visualizzare [configurare un ambiente di sviluppo](../develop/quickstart-create-new-tenant.md).

2. Abilitare e testare l'accesso single sign-on all'applicazione,

   a. **Per le applicazioni OIDC o Oath**, [registrare l'applicazione](../develop/quickstart-register-app.md) come un'applicazione multi-tenant. Selezionare gli account in qualsiasi directory dell'organizzazione e l'opzione di account Microsoft personale in tipi di Account supportati

   b. **Per le applicazioni e WS-Fed dal basato su SAML**, si [Configura SAML Single sign-on basato](configure-single-sign-on-non-gallery-applications.md) applicazioni usando un modello generico di SAML in Azure AD.

È anche possibile [convertire un'applicazione a tenant singolo per multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md) se necessario.

## <a name="next-steps"></a>Fasi successive

[Integrare l'accesso SSO nell'applicazione](isv-sso-content.md)
