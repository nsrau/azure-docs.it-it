---
title: Scenari comuni di gestione dei diritti Azure AD-Azure Active Directory
description: Informazioni sulle procedure di alto livello da seguire per gli scenari comuni di gestione dei diritti Azure Active Directory.
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
ms.openlocfilehash: 24d2dc82d0e81c9ac374d96387677774dc916b8c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173494"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Scenari comuni di gestione dei diritti Azure AD

Esistono diversi modi in cui è possibile configurare la gestione dei diritti per l'organizzazione. Tuttavia, se si sta iniziando, è utile comprendere gli scenari comuni per gli amministratori, i proprietari del catalogo, i gestori dei pacchetti di accesso, i responsabili approvazione e i richiedenti.

## <a name="delegate"></a>Delegato

### <a name="administrator-delegate-management-of-resources"></a>Amministratore: delegare la gestione delle risorse

1. [Guarda il video: delega da IT a Department Manager](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegare gli utenti al ruolo di autore del catalogo](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Creatore del catalogo: delegare la gestione delle risorse

- [Crea un nuovo catalogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietario del catalogo: delegare la gestione delle risorse

1. [Aggiungere i comproprietari al catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Aggiungere risorse al catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietario del catalogo: delegare la gestione dei pacchetti di accesso

1. [Guarda il video: delega dal proprietario del catalogo per accedere a gestione pacchetti](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delega utenti per l'accesso al ruolo di gestione pacchetti](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Governare l'accesso per gli utenti dell'organizzazione

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gestione pacchetti di Access: consente ai dipendenti dell'organizzazione di richiedere l'accesso alle risorse

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere i gruppi, i team, le applicazioni o i siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-create.md#resource-roles)
1. [Aggiungere un criterio di richiesta per consentire agli utenti della directory di richiedere l'accesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Richiedente: richiedere l'accesso alle risorse

1. [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Trova pacchetto di accesso
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile approvazione: approva le richieste alle risorse

1. [Apri la richiesta nel portale di accesso personale](entitlement-management-request-approve.md#open-request)
1. [Approva o nega la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Richiedente: visualizzare le risorse a cui si ha già accesso

1. [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivo

## <a name="govern-access-for-users-outside-your-organization"></a>Governare l'accesso per gli utenti esterni all'organizzazione

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Amministratore: collaborare con un'organizzazione partner esterna

1. [Informazioni su come funziona l'accesso per gli utenti esterni](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Verificare le impostazioni per gli utenti esterni](entitlement-management-external-users.md#settings-for-external-users)
1. [Aggiungere una connessione all'organizzazione esterna](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Gestione pacchetti di Access: collaborare con un'organizzazione partner esterna

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere i gruppi, i team, le applicazioni o i siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Aggiungere un criterio di richiesta per consentire agli utenti non presenti nella directory di richiedere l'accesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)
1. [Copiare il collegamento per richiedere il pacchetto di accesso](entitlement-management-access-package-settings.md)
1. Invia il collegamento al partner di contatto esterno per condividerlo con gli utenti

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Richiedente: richiedere l'accesso alle risorse come utente esterno

1. Trovare il collegamento al pacchetto di accesso ricevuto dal contatto
1. [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile approvazione: approva le richieste alle risorse

1. [Apri la richiesta nel portale di accesso personale](entitlement-management-request-approve.md#open-request)
1. [Approva o nega la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Richiedente: visualizzare le risorse a cui è già possibile accedere

1. [Accedere al portale di accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivo

## <a name="day-to-day-management"></a>Gestione quotidiana

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Gestione pacchetti di accesso: aggiornare le risorse per un progetto

1. [Guarda il video: gestione quotidiana: gli elementi sono stati modificati](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aggiungere o rimuovere gruppi, team, applicazioni o siti di SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Gestione pacchetti di accesso: aggiornare la durata di un progetto

1. [Guarda il video: gestione quotidiana: gli elementi sono stati modificati](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aprire le impostazioni del ciclo di vita](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aggiornare le impostazioni di scadenza](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gestione pacchetti di accesso: aggiornare l'approvazione dell'accesso per un progetto

1. [Guarda il video: gestione quotidiana: gli elementi sono stati modificati](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Apre un criterio esistente per le impostazioni di richiesta e approvazione](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aggiornare le impostazioni di approvazione](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Gestione pacchetti di accesso: aggiornare le persone per un progetto

1. [Guarda il video: gestione quotidiana: gli elementi sono stati modificati](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Rimuovere gli utenti che non necessitano più di accesso](entitlement-management-access-package-assignments.md)
1. [Apre un criterio esistente per le impostazioni di richiesta e approvazione](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aggiungere utenti che richiedono l'accesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Gestione pacchetti di accesso: assegnare direttamente utenti specifici a un pacchetto di accesso

1. [Se gli utenti necessitano di impostazioni del ciclo di vita diverse, aggiungere un nuovo criterio al pacchetto di accesso](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Assegnare direttamente utenti specifici al pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Assegnazioni e report

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrator: Visualizza le assegnazioni a un pacchetto di accesso

1. Aprire un pacchetto di accesso
1. [Visualizza assegnazioni](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)

### <a name="administrator-view-resources-assigned-to-users"></a>Amministratore: Visualizza le risorse assegnate agli utenti

1. [Visualizzare i pacchetti di accesso per un utente](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visualizzare le assegnazioni di risorse per un utente](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="next-steps"></a>Passaggi successivi

- [Delega e ruoli](entitlement-management-delegate.md)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
