---
title: Creare un tenant di Azure per un'applicazione multi-tenantCreate an Azure tenant for a multi-tenant application
description: Linee guida per i fornitori di software indipendenti sull'integrazione con Azure Active Directory
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812623"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Creare un tenant di Azure per un'applicazione multi-tenantCreate an Azure tenant for a multi-tenant application  

Per fornire l'accesso all'applicazione multi-tenant, è necessario creare un tenant di Azure Active Directory per registrare l'applicazione e abilitare la federazione delle identità del cliente. Vedere [Scelta del protocollo di federazione corretto per l'applicazione multi-tenant](isv-choose-multi-tenant-federation.md). Questo tenant consentirà di testare l'applicazione e la federazione in un ambiente simile agli ambienti Azure AD dei clienti.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Costi di hosting di un'applicazione multi-tenant

Azure Active Directory è disponibile in più edizioni. [Vedere il confronto dettagliato delle funzionalità](https://azure.microsoft.com/pricing/details/active-directory/).

È possibile creare gratuitamente la sottoscrizione di Azure e Azure Active Directory e usare le funzionalità di base.

## <a name="create-your-tenant"></a>Creare il tenantCreate your tenant

1. Creare il tenant. Consultate [Configurare un ambiente](../develop/quickstart-create-new-tenant.md)di sviluppo.

2. Abilitare e testare l'accesso Single Sign-On all'applicazione,

   a. Per le **applicazioni OIDC o Oath**, [registrare l'applicazione](../develop/quickstart-register-app.md) come applicazione multi-tenant. Selezionare l'opzione Account in qualsiasi directory organizzativa e account Microsoft personali in Tipi di account supportati

   b. **Per le applicazioni basate su SAML e WS-Fed,** è possibile configurare le applicazioni [Single Sign-On basate su SAML](configure-single-sign-on-non-gallery-applications.md) utilizzando un modello SAML generico in Azure AD.

Se necessario, è anche possibile [convertire un'applicazione single-tenant in multi-tenant.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Passaggi successivi

[Integrare SSO nell'applicazione](isv-sso-content.md)
