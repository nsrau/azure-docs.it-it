---
title: Scenari comuni nella gestione dei diritti - Azure ADCommon scenarios in entitlement management - Azure AD
description: Informazioni sui passaggi di alto livello da seguire per gli scenari comuni nella gestione dei diritti di Azure Active Directory.Learn the high-level steps you should follow for common scenarios in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190552"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Scenari comuni nella gestione dei diritti di Azure ADCommon scenarios in Azure AD entitlement management

Esistono diversi modi per configurare la gestione dei diritti per l'organizzazione. Tuttavia, se hai appena iniziato, è utile comprendere gli scenari comuni per amministratori, proprietari di cataloghi, gestori di pacchetti di accesso, approvatori e richiedenti.

## <a name="delegate"></a>Delegato

### <a name="administrator-delegate-management-of-resources"></a>Amministratore: Delegare la gestione delle risorse

1. [Video: Delega dall'IT al responsabile di reparto](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegare gli utenti al ruolo di creatore del catalogo](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Creatore del catalogo: delegare la gestione delle risorse

- [Crea un nuovo catalogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietario del catalogo: delegare la gestione delle risorse

1. [Aggiungere comproprietari al catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Aggiungere risorse al catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietario del catalogo: gestione delegata dei pacchetti di accesso

1. [Video: Delega dal proprietario del catalogo per accedere al gestore dei pacchetti](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegare gli utenti all'accesso al ruolo di gestione pacchettiDelegate users to access package manager role](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Gestire l'accesso per gli utenti dell'organizzazione

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Accesso al gestore dei pacchetti: consente ai dipendenti dell'organizzazione di richiedere l'accesso alle risorseAccess package manager: Allow employees in your organization to request access to resources

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere gruppi, team, applicazioni o siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-create.md#resource-roles)
1. [Aggiungere un criterio di richiesta per consentire agli utenti nella directory di richiedere l'accessoAdd a request policy to allow users in your directory to request access](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Richiedente: richiedi l'accesso alle risorse

1. [Accedere al portale My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Trova pacchetto di accesso
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile: Approvare le richieste alle risorse

1. [Richiesta aperta nel portale My Access](entitlement-management-request-approve.md#open-request)
1. [Approvare o rifiutare la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Richiedente: visualizza le risorse a cui hai già accesso

1. [Accedere al portale My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivi

## <a name="govern-access-for-users-outside-your-organization"></a>Gestire l'accesso per gli utenti esterni all'organizzazione

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Amministratore: collaborare con un'organizzazione partner esterna

1. [Leggere il funzionamento dell'accesso per gli utenti esterniRead how access works for external users](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Esaminare le impostazioni per gli utenti esterniReview settings for external users](entitlement-management-external-users.md#settings-for-external-users)
1. [Aggiungere una connessione all'organizzazione esterna](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gestore di pacchetti di accesso: collaborare con un'organizzazione partner esternaAccess package manager: Collaborate with an external partner organization

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere gruppi, team, applicazioni o siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Aggiungere un criterio di richiesta per consentire agli utenti non nella directory di richiedere l'accessoAdd a request policy to allow users not in your directory to request access](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)
1. [Copiare il collegamento per richiedere il pacchetto di accesso](entitlement-management-access-package-settings.md)
1. Inviare il collegamento al partner di contatto partner esterno per condividerlo con gli utenti

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Requestor: richiedi l'accesso alle risorse come utente esterno

1. Trova il link del pacchetto di accesso che hai ricevuto dal tuo contatto
1. [Accedere al portale My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile: Approvare le richieste alle risorse

1. [Richiesta aperta nel portale My Access](entitlement-management-request-approve.md#open-request)
1. [Approvare o rifiutare la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Richiedente: visualizza le risorse a cui hai già accesso

1. [Accedere al portale My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivi

## <a name="day-to-day-management"></a>Gestione quotidiana

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gestione pacchetti di Access: aggiornare le risorse per un progettoAccess package manager: Update the resources for a project

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aggiungere o rimuovere gruppi, team, applicazioni o siti di SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gestione pacchetti di Access: aggiornare la durata di un progettoAccess package manager: Update the duration for a project

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aprire le impostazioni del ciclo di vita](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aggiornare le impostazioni di scadenza](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gestore di pacchetti di accesso: aggiornare la modalità di approvazione dell'accesso per un progettoAccess package manager: Update how access is approved for a project

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Aprire un criterio esistente di impostazioni di richiesta e approvazione](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aggiornare le impostazioni di approvazione](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gestore di pacchetti di Access: aggiornare le persone per un progettoAccess package manager: Update the people for a project

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Rimuovere gli utenti che non necessitano più dell'accesso](entitlement-management-access-package-assignments.md)
1. [Aprire un criterio esistente di impostazioni di richiesta e approvazione](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aggiungere utenti che devono essere accostati](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gestione pacchetti di accesso: assegnare direttamente utenti specifici a un pacchetto di accessoAccess package manager: Directly assign specific users to an access package

1. [Se gli utenti hanno bisogno di impostazioni del ciclo di vita diverse, aggiungere un nuovo criterio al pacchetto di accesso](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Assegnare direttamente utenti specifici al pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Assegnazioni e report

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Amministratore: visualizzare gli utenti con assegnazioni a un pacchetto di accesso

1. Aprire un pacchetto di accessoOpen an access package
1. [Visualizzare le assegnazioni](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiviare report e registri](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Amministratore: visualizzare le risorse assegnate agli utenti

1. [Visualizzare i pacchetti di accesso per un utenteView access packages for a user](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visualizzare le assegnazioni delle risorse per un utente](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Amministrazione programmatica

È inoltre possibile gestire pacchetti di accesso, cataloghi, criteri, richieste e assegnazioni utilizzando Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione con l'autorizzazione delegata può chiamare l'API di gestione dei [diritti](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

## <a name="next-steps"></a>Passaggi successivi

- [Delega e ruoli](entitlement-management-delegate.md)
- [Processo di richiesta e notifiche e-mail](entitlement-management-process.md)
