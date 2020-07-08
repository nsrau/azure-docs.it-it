---
title: Disabilitare gli accessi utente per un'app aziendale in Azure AD
description: Come disabilitare un'applicazione aziendale in modo che gli utenti non possano accedervi in Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763381"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory

È facile disabilitare un'applicazione aziendale in modo che nessun utente possa accedervi in Azure Active Directory (Azure AD). Per gestire l'app aziendale sono necessarie le autorizzazioni appropriate. È necessario essere un amministratore globale per la directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Come è possibile disabilitare l'accesso degli utenti?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.
1. Selezionare **tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **invio**.
1. Nel riquadro **Azure Active Directory**  -   ***DirectoryName*** , ovvero il riquadro Azure ad per la directory che si sta gestendo, selezionare **applicazioni aziendali**.
1. Nel riquadro **applicazioni aziendali-tutte le applicazioni** viene visualizzato un elenco delle app che è possibile gestire. Selezionare un'app.
1. Nel riquadro ***nomeapp***, vale a dire il riquadro con il nome dell'app selezionata nel titolo, selezionare **Proprietà**.
1. Nel riquadro ***appname***  -  **proprietà** AppName selezionare **No** per **abilitata per l'accesso degli utenti?**.
1. Selezionare il comando **Salva** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Usare Azure AD PowerShell per disabilitare un'app non in elenco

Se si conosce l'AppId di un'app che non viene visualizzata nell'elenco delle app aziendali (ad esempio, perché l'app è stata eliminata o l'entità servizio non è ancora stata creata perché l'app è già stata autorizzata da Microsoft), è possibile creare manualmente l'entità servizio per l'app e quindi disabilitarla usando il [cmdlet AzureAD di PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
