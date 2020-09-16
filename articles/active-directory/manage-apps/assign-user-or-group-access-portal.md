---
title: Gestire l'assegnazione utente per un'app in Azure Active Directory
description: Informazioni su come assegnare e annullare l'assegnazione di utenti e gruppi per un'app usando Azure Active Directory per la gestione delle identità.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: f49377743521e27c2312e95491762ca48d8448c4
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604326"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Gestire l'assegnazione utente per un'app in Azure Active Directory

Questo articolo illustra come assegnare utenti e gruppi alle applicazioni aziendali in Azure Active Directory (Azure AD), dall'interno del portale di Azure o tramite PowerShell. Quando si assegna un utente a un'applicazione, l'applicazione viene visualizzata nelle [app personali](https://myapps.microsoft.com/) dell'utente per facilitarne l'accesso. Se l'applicazione espone ruoli, è anche possibile assegnare un ruolo specifico all'utente.

Per un maggiore controllo, è possibile configurare alcuni tipi di applicazioni aziendali per [richiedere l'assegnazione utente](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Quando si assegna un gruppo a un'applicazione, solo gli utenti del gruppo avranno accesso. L'assegnazione non si estende ai gruppi annidati.

> [!NOTE]
> L'assegnazione basata su gruppo richiede Azure Active Directory Premium, edizione P1 o P2. L'assegnazione basata su gruppo è supportata solo per i gruppi di sicurezza. Le appartenenze ai gruppi annidati e i gruppi di Microsoft 365 non sono attualmente supportati. Per altri requisiti di licenza per le funzionalità descritte in questo articolo, vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configurare un'applicazione per richiedere l'assegnazione utente

Con i tipi di applicazioni seguenti, è possibile scegliere di richiedere che gli utenti vengano assegnati all'applicazione prima che possano accedervi:

- Applicazioni configurate per l'accesso Single Sign-On (SSO) federato con autenticazione basata su SAML
- Applicazioni proxy di applicazione che usano la preautenticazione di Azure Active Directory
- Applicazioni create nella piattaforma applicativa Azure AD che usano l'autenticazione OAuth 2.0/OpenID Connect dopo che un utente o un amministratore ha fornito il consenso per tale applicazione.

Quando è richiesta l'assegnazione utente, solo gli utenti assegnati in modo esplicito all'applicazione (tramite assegnazione utente diretta o in base all'appartenenza al gruppo) potranno eseguire l'accesso. Possono accedere all'app dalla pagina App personali o usando un collegamento diretto. 

Quando l'assegnazione *non è richiesta*, perché questa opzione è stata impostata su **No** o perché l'applicazione usa un'altra modalità di accesso SSO, qualsiasi utente potrà accedere all'applicazione se dispone di un collegamento diretto all'applicazione o dell'**URL accesso utente** nella pagina **Proprietà** dell'applicazione. 

Questa impostazione non ha effetto se un'applicazione viene visualizzata o meno nelle app personali. Le applicazioni vengono visualizzate nei pannelli di accesso App personali degli utenti dopo aver assegnato un utente o un gruppo all'applicazione. Per informazioni di base, vedere [Gestione dell'accesso alle app](what-is-access-management.md).

Per richiedere l'assegnazione utente per un'applicazione:
1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore o come proprietario dell'applicazione.
2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **Applicazioni aziendali**.
3. Selezionare l'applicazione nell'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, usare i controlli del filtro per selezionare il tipo di applicazione, lo stato o la visibilità e quindi selezionare **Applica**.
4. Nel menu di spostamento a sinistra selezionare **Proprietà**.
5. Assicurarsi che l'interruttore **Assegnazione utenti obbligatoria** sia impostato su **Sì**.
   > [!NOTE]
   > Se l'interruttore **Assegnazione utenti obbligatoria** non è disponibile, è possibile usare PowerShell per impostare la proprietà appRoleAssignmentRequired nell'entità servizio.
6. Fare clic sul pulsante **Salva** nella parte superiore della schermata.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Assegnare o annullare l'assegnazione di utenti e gruppi per un'app usando il portale di Azure
Per informazioni su come assegnare o annullare l'assegnazione di un utente o un gruppo usando il portale di Azure, vedere la [serie di guide introduttive sulla gestione delle applicazioni](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Assegnare o annullare l'assegnazione di utenti e gruppi per un'app usando il API Graph
È possibile usare la API Graph per assegnare o annullare l'assegnazione di utenti e gruppi per un'app. Per altre informazioni, vedere [assegnazioni di ruolo app](https://docs.microsoft.com/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Assegnare utenti e gruppi a un'app tramite PowerShell
1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.
   > [!NOTE]
   > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.
2. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
3. Usare lo script seguente per assegnare un utente e un ruolo a un'applicazione:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Per altre informazioni su come assegnare un utente a un ruolo applicazione, vedere la documentazione per [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Per assegnare un gruppo a un'app aziendale, è necessario sostituire `Get-AzureADUser` con `Get-AzureADGroup` e `New-AzureADUserAppRoleAssignment` con `New-AzureADGroupAppRoleAssignment`.

Per altre informazioni su come assegnare un gruppo a un ruolo applicazione, vedere la documentazione per [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0).

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
   ![Mostra i ruoli disponibili per un utente con il ruolo Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
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

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Annullare l'assegnazione di utenti e gruppi da un'app tramite PowerShell

1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.
   > [!NOTE]
   > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.
2. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
3. Usare lo script seguente per rimuovere un utente e un ruolo da un'applicazione:

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


## <a name="related-articles"></a>Articoli correlati

- [Altre informazioni sull'accesso degli utenti finali alle applicazioni](end-user-experiences.md)
- [Pianificare un Azure AD la distribuzione di app personali](access-panel-deployment-plan.md)
- [Gestione dell'accesso alle app](what-is-access-management.md)
 
## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
- [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
- [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
