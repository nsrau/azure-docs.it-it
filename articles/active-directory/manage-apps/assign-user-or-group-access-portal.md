---
title: Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come selezionare un'app aziendale a cui assegnare un utente o gruppo in anteprima di Azure Active Directory
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 6596a814841619d7b42599d381d79c98f672ec85
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196481"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory
Per assegnare un utente o un gruppo a un'app aziendale, è necessario avere le autorizzazioni appropriate per gestire l'app aziendale ed essere l'amministratore globale per la directory.

> [!NOTE]
> Per i requisiti di licenza per le funzionalità descritte in questo articolo, vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per assegnare utenti a un'app aziendale.


## <a name="assign-a-user-to-an-app---portal"></a>Assegnare un utente a un'app - Portale
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Tutti i servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
3. Selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Nel pannello **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco delle app che è possibile gestire.
5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.
6. Nel pannello ***nome app***, ovvero il pannello con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione del comando All applications (Tutte le applicazioni)](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Nel pannello ***nome app*** **- Assegnazione di utenti e gruppi** selezionare il comando **Aggiungi**.
8. Nel pannello **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Assegnare un utente o gruppo all'app](./media/assign-user-or-group-access-portal/assign-users.png)
9. Nel pannello **Utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello.
10. Nel pannello **Aggiungi assegnazione** selezionare **Ruolo**. Nel pannello **Selezionare un ruolo** scegliere il ruolo da applicare ai gruppi o utenti selezionati, quindi fare clic su **OK** nella parte inferiore del pannello.
11. Nella parte inferiore del pannello **Aggiungi assegnazione** fare clic sul pulsante **Assegna**. Gli utenti o i gruppi assegnati avranno le autorizzazioni definite dal ruolo selezionato per questa app aziendale.

## <a name="allow-all-users-to-access-an-app---portal"></a>Consentire agli utenti di accedere a un'app - Portale
Per consentire agli utenti di accedere a un'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Tutti i servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
3. Selezionare **Applicazioni aziendali**.
4. Nel pannello **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco delle app che è possibile gestire.
5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.
6. Nel pannello ***nome app*** selezionare **Proprietà**.
7. Nel pannello ***nome app* - Proprietà** impostare l'opzione **Assegnazione utenti obbligatoria** su **No**. 

L'opzione **Assegnazione utenti obbligatoria**:

- Non influisce sulla presenza di un'applicazione nel pannello di accesso alle applicazioni. Per visualizzare l'applicazione nel pannello di accesso, assegnare un utente o un gruppo appropriato all'applicazione.
- Funziona solo con le applicazioni cloud configurate per l'accesso Single Sign-On SAML e le applicazioni locali configurate con il proxy di applicazione. Vedere [Accesso Single Sign-On alle applicazioni](what-is-single-sign-on.md).
- Richiede il consenso degli utenti per un'applicazione. Un amministratore può concedere il consenso per tutti gli utenti.  Vedere [Configurare il consenso utente in un'applicazione](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Assegnare un utente a un'app - PowerShell

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

Per altre informazioni su come assegnare un utente a un ruolo applicazione, vedere la documentazione per [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Per assegnare un gruppo a un'app aziendale, è necessario sostituire `Get-AzureADUser` con `Get-AzureADGroup`.

### <a name="example"></a>Esempio

Questo esempio assegna l'utente Britta Simon all'applicazione [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) usando PowerShell.

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
    
    ![Ruolo di Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Assegnare il nome del ruolo alla variabile `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Eseguire il comando seguente per assegnare l'utente al ruolo applicazione:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
* [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
