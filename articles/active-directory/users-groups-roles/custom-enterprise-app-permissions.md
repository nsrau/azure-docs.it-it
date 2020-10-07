---
title: Autorizzazioni dell'app per i ruoli personalizzati in Azure Active Directory | Microsoft Docs
description: Anteprima delle autorizzazioni dell'app per i ruoli di Azure AD personalizzati nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 2ebba2bb957a1c05fd591181f0a784ed778c6c57
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91333376"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Autorizzazioni delle applicazioni aziendali per i ruoli personalizzati in Azure Active Directory

Questo articolo elenca le autorizzazioni delle applicazioni aziendali attualmente disponibili per le definizioni di ruolo personalizzate in Azure Active Directory (Azure AD). L'articolo contiene elenchi di autorizzazioni per alcuni scenari comuni e l'elenco completo delle autorizzazioni delle applicazioni aziendali. Le autorizzazioni del proxy di applicazione non sono attualmente implementate in questa versione.

## <a name="required-license-plan"></a>Piano di licenza necessario

Per usare questa funzionalità è necessario disporre di una licenza di Azure AD Premium P1 per l'organizzazione di Azure AD. Per trovare la licenza corretta per le proprie esigenze, vedere [Caratteristiche e funzionalità di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Autorizzazioni delle applicazioni aziendali

Per altre informazioni su come usare queste autorizzazioni, vedere [Assegnare ruoli personalizzati per gestire le app aziendali in Azure Active Directory](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Assegnazione di utenti o gruppi a un'applicazione

Delegare l'assegnazione di utenti e gruppi che possono accedere ad applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>Creazione di applicazioni della raccolta

Delegare la creazione di applicazioni della raccolta di Azure AD come, ad esempio, ServiceNow, F5 e Salesforce. Autorizzazioni necessarie:

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>Configurazione di URL SAML di base

Delegare l'aggiornamento e la lettura delle configurazioni SAML di base per le applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>Rollover o creazione di certificati di firma

Delegare la gestione dei certificati di firma per le applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie.

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Aggiornare l'indirizzo di posta elettronica di notifica per i certificati di firma in scadenza

Delegare l'aggiornamento degli indirizzi di posta elettronica di notifica dei certificati di firma in scadenza per le applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Gestire l'algoritmo di accesso e firma del token SAML

Delegare l'aggiornamento dell'algoritmo di accesso e firma del token SAML per le applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>Gestire attributi e attestazioni utente

Delegare le operazioni di creazione, eliminazione e aggiornamento delle attestazioni e degli attributi utente per le applicazioni Single Sign-On basate su SAML. Autorizzazioni necessarie:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>Autorizzazioni di provisioning delle app

Per eseguire qualsiasi operazione di scrittura, come la gestione del processo, dello schema o delle credenziali tramite l'interfaccia utente, sono necessarie anche le autorizzazioni di lettura per visualizzare la pagina di provisioning.

Per impostare l'ambito su tutti gli utenti e i gruppi o su utenti e gruppi assegnati sono attualmente necessarie le autorizzazioni synchronizationJob e synchronizationCredentials.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Attivare o riavviare i processi di provisioning

Delegare la capacità di attivare, disattivare e riavviare i processi di provisioning. Autorizzazioni necessarie:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>Configurare lo schema di provisioning  

Delegare gli aggiornamenti al mapping degli attributi. Autorizzazioni necessarie:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Leggere le impostazioni di provisioning associate all'oggetto applicazione

Delegare la capacità di leggere le impostazioni di provisioning associate all'oggetto applicazione. Autorizzazioni necessarie:

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Leggere le impostazioni di provisioning associate all'entità servizio

Delegare la capacità di leggere le impostazioni di provisioning associate all'entità servizio. Autorizzazioni necessarie:

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>Autorizzare l'accesso alle applicazioni per il provisioning  

Delegare la capacità di autorizzare l'accesso alle applicazioni per il provisioning. Esempio di input del token di connessione OAuth. Autorizzazioni necessarie:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>Elenco completo delle autorizzazioni

Autorizzazione | Descrizione
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | Lettura di tutte le proprietà dei criteri dell'applicazione.
microsoft.directory/applicationPolicies/allProperties/update | Aggiornamento di tutte le proprietà dei criteri dell'applicazione.
microsoft.directory/applicationPolicies/basic/update | Aggiornamento delle proprietà standard dei criteri dell'applicazione.
microsoft.directory/applicationPolicies/create | Creazione dei criteri dell'applicazione.
microsoft.directory/applicationPolicies/createAsOwner | Creazione dei criteri dell'applicazione. L'autore viene aggiunto come primo proprietario.
microsoft.directory/applicationPolicies/delete | Eliminazione di criteri dell'applicazione.
microsoft.directory/applicationPolicies/owners/read | Lettura dei proprietari nei criteri dell'applicazione.
microsoft.directory/applicationPolicies/owners/update | Aggiornamento della proprietà relativa al proprietario dei criteri dell'applicazione.
microsoft.directory/applicationPolicies/policyAppliedTo/read | Lettura dei criteri dell'applicazione applicati all'elenco di oggetti.
microsoft.directory/applicationPolicies/standard/read | Lettura delle proprietà standard dei criteri dell'applicazione.
microsoft.directory/servicePrincipals/allProperties/allTasks | Creare ed eliminare servicePrincipals, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory.
microsoft.directory/servicePrincipals/allProperties/read | Lettura di tutte le proprietà delle entità servizio.
microsoft.directory/servicePrincipals/allProperties/update | Aggiornamento di tutte le proprietà delle entità servizio.
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lettura delle assegnazioni di ruolo delle entità servizio.
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornamento delle assegnazioni di ruolo delle entità servizio.
microsoft.directory/servicePrincipals/appRoleAssignments/read | Lettura delle assegnazioni di ruolo assegnate a entità servizio.
microsoft.directory/servicePrincipals/audience/update | Aggiornamento delle proprietà relative ai destinatari nelle entità servizio.
microsoft.directory/servicePrincipals/authentication/update | Aggiornamento delle proprietà di autenticazione nelle entità servizio.
microsoft.directory/servicePrincipals/basic/update | Aggiornamento delle proprietà di base nelle entità servizio.
microsoft.directory/servicePrincipals/create | Creazione di entità servizio.
microsoft.directory/servicePrincipals/createAsOwner | Creazione di entità servizio. L'autore viene aggiunto come primo proprietario.
microsoft.directory/servicePrincipals/credentials/update | Aggiornamento delle proprietà relative alle credenziali nelle entità servizio.
microsoft.directory/servicePrincipals/delete | Eliminazione di entità servizio.
microsoft.directory/servicePrincipals/disable | Disabilitazione di entità servizio.
microsoft.directory/servicePrincipals/enable | Abilitazione di entità servizio.
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Lettura delle credenziali per l'accesso Single Sign-On per la password nelle entità servizio.
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Gestione delle credenziali per l'accesso Single Sign-On per la password nelle entità servizio.
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lettura delle concessioni di autorizzazioni delegate nelle entità servizio.
microsoft.directory/servicePrincipals/owners/read | Lettura dei proprietari nelle entità servizio.
microsoft.directory/servicePrincipals/owners/update | Aggiornamento dei proprietari nelle entità servizio.
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | Lettura dei criteri nelle entità servizio.
microsoft.directory/servicePrincipals/policies/update | Aggiornamento dei criteri nelle entità servizio.
microsoft.directory/servicePrincipals/standard/read | Lettura delle proprietà standard delle entità servizio.
microsoft.directory/servicePrincipals/synchronization/standard/read | Lettura delle impostazioni di provisioning associate all'entità servizio.
microsoft.directory/servicePrincipals/tag/update | Aggiornamento della proprietà relativa ai tag nelle entità servizio.
microsoft.directory/applicationTemplates/instantiate | Creare un'istanza delle applicazioni della raccolta dai modelli di applicazioni.
microsoft.directory/auditLogs/allProperties/read | Lettura dei log di controllo.
microsoft.directory/signInReports/allProperties/read | Lettura dei report di accesso.
microsoft.directory/applications/synchronization/standard/read | Lettura delle impostazioni di provisioning associate all'oggetto applicazione.
microsoft.directory/servicePrincipals/synchronizationJobs/manage | Gestione di tutti gli aspetti della sincronizzazione dei processi per le risorse dell'entità servizio.
microsoft.directory/servicePrincipals/synchronization/standard/read | Lettura delle impostazioni di provisioning associate alle entità servizio.
microsoft.directory/servicePrincipals/synchronizationSchema/manage | Gestione di tutti gli aspetti della sincronizzazione degli schemi per le risorse dell'entità servizio.
microsoft.directory/provisioningLogs/allProperties/read | Lettura di tutte le proprietà dei log di provisioning.

## <a name="next-steps"></a>Passaggi successivi

- [Creare ruoli personalizzati usando il portale di Azure, Azure AD PowerShell e l'API Graph](roles-create-custom.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](roles-view-assignments.md)
