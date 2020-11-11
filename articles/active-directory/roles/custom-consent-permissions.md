---
title: Autorizzazioni per il consenso alle app per ruoli personalizzati in Azure Active Directory | Microsoft Docs
description: Anteprima delle autorizzazioni per il consenso alle app per ruoli personalizzati di Azure AD nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: 358a458698ec1fd8443e15f71a84e057f33af527
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376732"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Autorizzazioni per il consenso alle app per ruoli personalizzati in Azure Active Directory

Questo articolo elenca le autorizzazioni attualmente disponibili per il consenso alle app per le definizioni di ruoli personalizzati in Azure Active Directory (Azure AD). In questo articolo sono disponibili le autorizzazioni necessarie per alcuni scenari comuni relativi al consenso e alle autorizzazioni per le app.

## <a name="required-license-plan"></a>Piano di licenza necessario

Per usare questa funzionalità è necessario disporre di una licenza di Azure AD Premium P1 per l'organizzazione di Azure AD. Per trovare la licenza corretta per le proprie esigenze, vedere [Caratteristiche e funzionalità di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Autorizzazioni per il consenso alle app

Usare le autorizzazioni elencate in questo articolo per gestire i criteri di consenso alle app, nonché l'autorizzazione per concedere il consenso alle app.

> [!NOTE]
> Il portale di amministrazione di Azure AD non supporta ancora l'aggiunta delle autorizzazioni elencate in questo articolo a definizioni di ruoli della directory personalizzati. È necessario [usare Azure AD PowerShell per creare un ruolo della directory personalizzato](custom-create.md#create-a-role-using-powershell) con le autorizzazioni riportate in questo articolo.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Concessione di autorizzazioni delegate alle app per conto dell'utente (consenso dell'utente)

Per consentire agli utenti di concedere il consenso alle app per loro conto (consenso utente), soggetto a un criterio di consenso per le app.

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

Dove `{id}` viene sostituito con l'ID di un [criterio di consenso per le app](../manage-apps/manage-app-consent-policies.md) che imposterà le condizioni che è necessario soddisfare affinché questa autorizzazione sia attiva.

Ad esempio, per consentire agli utenti di concedere il consenso per loro conto, soggetto al criterio di consenso per le app predefinito con ID `microsoft-user-default-low`, usare l'autorizzazione `...managePermissionGrantsForSelf.microsoft-user-default-low`.

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Concessione di autorizzazioni alle app per conto di tutti (consenso amministratore)

Per delegare il consenso amministratore a livello di tenant alle app, sia per le autorizzazioni delegate sia per le autorizzazioni dell'applicazione (ruoli app):

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

Dove `{id}` viene sostituito con l'ID di un [criterio di consenso per le app](../manage-apps/manage-app-consent-policies.md) che imposterà le condizioni che è necessario soddisfare affinché questa autorizzazione sia utilizzabile.

Ad esempio, per consentire agli assegnatari del ruolo di concedere il consenso amministratore a livello di tenant alle app, soggetto al [criterio di consenso per l'app](../manage-apps/manage-app-consent-policies.md) con ID `low-risk-any-app`, usare l'autorizzazione `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app`.

### <a name="managing-app-consent-policies"></a>Gestione dei criteri di consenso per le app

Per delegare la creazione, l'aggiornamento e l'eliminazione dei [criteri di consenso per le app](../manage-apps/manage-app-consent-policies.md).

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>Elenco completo delle autorizzazioni

Autorizzazione | Descrizione
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | Concede la possibilità di dare il consenso alle app per conto dell'utente (consenso utente), soggetto al criterio di consenso per le app `{id}`.
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | Concede la possibilità di dare il consenso alle app per conto di tutti (consenso amministratore a livello di tenant), soggetto al criterio di consenso per le app `{id}`.
microsoft.directory/permissionGrantPolicies/standard/read | Concede la possibilità di leggere i criteri di consenso per le app.
microsoft.directory/permissionGrantPolicies/basic/update | Concede la possibilità di aggiornare le proprietà di base nei criteri di consenso per le app esistenti.
microsoft.directory/permissionGrantPolicies/create | Concede la possibilità di creare criteri di consenso per le app.
microsoft.directory/permissionGrantPolicies/delete | Concede la possibilità di eliminare criteri di consenso per le app.

## <a name="next-steps"></a>Passaggi successivi

- [Creare ruoli personalizzati usando il portale di Azure, Azure AD PowerShell e l'API Graph](custom-create.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](../roles/view-assignments.md)
