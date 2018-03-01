---
title: Rimuovere l'assegnazione di un utente o di un gruppo da un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come rimuovere l'assegnazione di accesso di un utente o gruppo da un'app aziendale in anteprima di Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 084ffcbe473290a8734b1c8b8847b517dac4f6b6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in Azure Active Directory
Rimuovere un utente o un gruppo dalla possibilità di accedere a una delle applicazioni aziendali in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale ed essere amministratore globale della directory.

> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per rimuovere utenti da un'app aziendale.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale nel portale di Azure?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
3. Nella pagina **Azure Active Directory - *nomedirectory***, vale a dire la pagina Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Nella pagina **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Verrà visualizzato un elenco di app che è possibile gestire.
5. Nella pagina **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.
6. Nella pagina ***nomeapp***, ovvero la pagina con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione di utenti o gruppi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Nella pagina ***nomeapp*** di **assegnazione utente e gruppo** selezionare uno o più utenti o gruppi e quindi fare clic sul comando **Rimuovi**. Confermare la decisione al prompt dei comandi.

    ![Selezione del comando Rimuovi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale con PowerShell?
1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

    >[!NOTE] 
    > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

2. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
3. Usare lo script seguente per assegnare un utente e un ruolo a un'applicazione:

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

- [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
- [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)
- [Disabilitare l'accesso degli utenti per un'app aziendale](active-directory-coreapps-disable-app-azure-portal.md)
- [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)
