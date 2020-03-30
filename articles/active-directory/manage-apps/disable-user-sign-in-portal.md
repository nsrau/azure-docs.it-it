---
title: Disabilitare gli accessi utente per un'app aziendale in Azure ADDisable user sign-ins for an enterprise app in Azure AD
description: Come disabilitare un'applicazione aziendale in modo che gli utenti non possano accedervi in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274098"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory

È facile disabilitare un'applicazione aziendale in modo che nessun utente possa accedere in Azure Active Directory (Azure AD). Per gestire l'app aziendale sono necessarie le autorizzazioni appropriate. Inoltre, è necessario essere amministratore globale per la directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Come è possibile disabilitare l'accesso degli utenti?

1. Accedere al [portale](https://portal.azure.com) di Azure con un account amministratore globale per la directory.
1. Selezionare **Tutti i servizi**, immettere Azure Active **Directory** nella casella di testo e quindi selezionare **INVIO**.
1. Nel riquadro***Nome directory*** di Azure **Active Directory,** -  ovvero nel riquadro di Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.
1. Nel riquadro **Applicazioni aziendali - Tutte le applicazioni** viene visualizzato un elenco delle app che è possibile gestire. Selezionare un'app.
1. Nel riquadro ***nomeapp***, vale a dire il riquadro con il nome dell'app selezionata nel titolo, selezionare **Proprietà**.
1. Nel riquadro**Proprietà** ***nomeapp*** - selezionare **No** per Abilitato per consentire agli utenti di **accedere?**.
1. Selezionare il comando **Salva** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Usare Azure AD PowerShell per disabilitare un'app non in elencoUse Azure AD PowerShell to disable an unlisted app

Se si conosce l'AppId di un'app che non viene visualizzata nell'elenco delle app enterprise, ad esempio perché l'app è stata eliminata o l'entità servizio non è ancora stata creata a causa del pre-autorizzato da parte di Microsoft, è possibile creare manualmente l'entità servizio per l'app e quindi disabilitarla utilizzando il [cmdlet AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

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
