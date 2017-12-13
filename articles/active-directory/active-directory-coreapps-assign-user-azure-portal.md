---
title: Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come selezionare un'app aziendale a cui assegnare un utente o gruppo in anteprima di Azure Active Directory
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: daveba
ms.reviewer: luleon
ms.openlocfilehash: 65727ee9330a1a6650eb54595ebc93a7a693923c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory
Per assegnare un utente o un gruppo a un'app aziendale, è necessario avere le autorizzazioni appropriate per gestire l'app aziendale ed essere l'amministratore globale per la directory.
> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per assegnare utenti a un'app aziendale.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Come si assegna l'accesso utente a un'app aziendale nel portale di Azure?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
3. Nel pannello **Azure Active Directory - *nomedirectory*** vale a dire il pannello Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Nel pannello **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco delle app che è possibile gestire.
5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.
6. Nel pannello ***nome app***, ovvero il pannello con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione del comando All applications (Tutte le applicazioni)](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Nel pannello ***nome app*** **- Assegnazione di utenti e gruppi** selezionare il comando **Aggiungi**.
8. Nel pannello **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Assegnare un utente o gruppo all'app](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Nel pannello **Utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello.
10. Nel pannello **Aggiungi assegnazione** selezionare **Ruolo**. Nel pannello **Selezionare un ruolo** scegliere il ruolo da applicare ai gruppi o utenti selezionati, quindi fare clic su **OK** nella parte inferiore del pannello.
11. Nella parte inferiore del pannello **Aggiungi assegnazione** fare clic sul pulsante **Assegna**. Gli utenti o i gruppi assegnati avranno le autorizzazioni definite dal ruolo selezionato per questa app aziendale.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Come si assegna un utente a un'app aziendale con PowerShell?

1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

    >[!NOTE] 
    > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

2. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
3. Usare lo script seguente per assegnare un utente e un ruolo a un'applicazione:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Per altre informazioni su come assegnare un utente a un ruolo applicazione, vedere la documentazione per [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

### <a name="example"></a>Esempio

Questo esempio assegna l'utente Britta Simon all'applicazione [Microsoft Workplace Analytics](https://products.office.com/en-us/business/workplace-analytics) usando PowerShell.

1. In PowerShell assegnare i valori corrispondenti alle variabili $username, $app_name e $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. In questo esempio il nome esatto del ruolo applicazione da assegnare a Britta Simon non è noto. Eseguire i comandi seguenti per ottenere l'utente ($user) e l'entità servizio ($sp) usando i nomi visualizzati di UPN utente ed entità servizio.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Eseguire il comando `$sp.AppRoles` per visualizzare i ruoli disponibili per l'applicazione Workplace Analytics. In questo esempio si vuole assegnare a Britta Simon il ruolo di analista (accesso limitato).
    
    ![Ruolo di Workplace Analytics](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. Assegnare il nome del ruolo alla variabile `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    ```

5. Eseguire il comando seguente per assegnare l'utente al ruolo applicazione:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](active-directory-coreapps-disable-app-azure-portal.md)
* [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)
