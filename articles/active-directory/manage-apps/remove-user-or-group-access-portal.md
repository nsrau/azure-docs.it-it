---
title: Rimuovere l'assegnazione di un utente o di un gruppo da un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come rimuovere l'assegnazione di accesso di un utente o gruppo da un'app aziendale in anteprima di Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97759ae992ebe38aa85e9b4724edeebb5285db4b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565700"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in Azure Active Directory
È facile rimuovere un utente o un gruppo di accesso assegnato a una delle applicazioni aziendali in Azure Active Directory (Azure AD). Sono necessarie le autorizzazioni appropriate per gestire l'app aziendale. E, è necessario essere amministratore globale della directory.

> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per rimuovere utenti da un'app aziendale.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale nel portale di Azure?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
1. Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
1. Nel **Azure Active Directory - *nomedirectory***  pagina (vale a dire, pagina di Azure AD per la directory che si sta gestendo), selezionare **applicazioni aziendali**.
1. Nel **applicazioni aziendali - tutte le applicazioni** pagina, si verrà visualizzato un elenco di App che è possibile gestire. Selezionare un'app.
1. Nel ***appname*** pagina di panoramica (vale a dire, la pagina con il nome dell'app selezionata nel titolo), selezionare **utenti e gruppi**.
1. Nella pagina ***nomeapp*** di **assegnazione utente e gruppo** selezionare uno o più utenti o gruppi e quindi fare clic sul comando **Rimuovi**. Confermare la decisione al prompt dei comandi.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale con PowerShell?
1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

    >[!NOTE] 
    > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

1. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
1. Usare lo script seguente per rimuovere un ruolo e l'utente da un'applicazione:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
   ## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
- [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
- [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
