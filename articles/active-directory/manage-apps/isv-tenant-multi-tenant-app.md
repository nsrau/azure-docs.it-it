---
title: Creare un tenant di Azure per un'applicazione multi-tenant
description: Linee guida per i fornitori di software indipendenti per l'integrazione con Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 264d4afb50904be17624f8801b7b79fe4b232030
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386810"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Creare un tenant di Azure per un'applicazione multi-tenant  

Per consentire l'accesso all'applicazione multi-tenant, è necessario creare un tenant di Azure Active Directory per registrare l'applicazione e abilitare la Federazione delle identità del cliente. Vedere [scelta del protocollo federativo appropriato per l'applicazione multi-tenant](isv-choose-multi-tenant-federation.md). Questo tenant consente di testare l'applicazione e la Federazione in un ambiente simile a quello dei clienti Azure AD ambienti.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Costi per l'hosting di un'applicazione multi-tenant

Azure Active Directory è disponibile in più edizioni. [Vedere il confronto dettagliato delle funzionalità](https://azure.microsoft.com/pricing/details/active-directory/).

È possibile creare gratuitamente la sottoscrizione di Azure e Azure Active Directory e usare le funzionalità di base.

## <a name="create-your-tenant"></a>Creare il tenant

1. Creare il tenant. Vedere [configurare un ambiente di sviluppo](../develop/quickstart-create-new-tenant.md).

2. Abilitare e testare Single Sign-On l'accesso all'applicazione,

   a. **Per le applicazioni OIDC o giuramento**, [registrare l'applicazione](../develop/quickstart-register-app.md) come applicazione multi-tenant. Selezionare l'opzione account in qualsiasi directory organizzativa e account Microsoft personali nei tipi di account supportati

   b. **Per le applicazioni basate su SAML e WS-Fed**, si [configurano le applicazioni Single Sign-on basate su SAML](configure-saml-single-sign-on.md) usando un modello SAML generico in Azure ad.

Se necessario, è anche possibile [convertire un'applicazione a tenant singolo in multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Passaggi successivi

[Integrare SSO nell'applicazione](isv-sso-content.md)
