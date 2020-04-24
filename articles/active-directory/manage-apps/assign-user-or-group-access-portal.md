---
title: Assegnare un utente o un gruppo a un'app aziendale in Azure AD
description: Come selezionare un'app aziendale a cui assegnare un utente o gruppo in anteprima di Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409193"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory

Questo articolo illustra come assegnare utenti o gruppi ad applicazioni aziendali in Azure Active Directory (Azure AD), dall'interno del portale di Azure o tramite PowerShell. Quando si assegna un utente a un'applicazione, l'applicazione viene visualizzata nel pannello di [accesso app personali](https://myapps.microsoft.com/) dell'utente per facilitarne l'accesso. Se l'applicazione espone ruoli, è anche possibile assegnare un ruolo specifico all'utente.

Per un maggiore controllo, è possibile configurare determinati tipi di applicazioni aziendali in modo da [richiedere l'assegnazione dell'utente](#configure-an-application-to-require-user-assignment). 

Per [assegnare un utente o un gruppo a un'app aziendale](#assign-users-or-groups-to-an-app-via-the-azure-portal), è necessario effettuare l'accesso come amministratore globale, amministratore dell'applicazione, amministratore di applicazioni cloud o il proprietario assegnato dell'app aziendale.

> [!NOTE]
> Per l'assegnazione basata su gruppo è necessario Azure Active Directory Premium P1 o P2 Edition. L'assegnazione basata su gruppi è supportata solo per i gruppi di sicurezza. Le appartenenze ai gruppi annidati e i gruppi di Office 365 non sono attualmente supportati. Per ulteriori requisiti di licenza per le funzionalità descritte in questo articolo, vedere la pagina relativa ai [prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Configurare un'applicazione per richiedere l'assegnazione di utenti

Con i seguenti tipi di applicazioni, è possibile scegliere di richiedere agli utenti di essere assegnati all'applicazione prima di potervi accedere:

- Applicazioni configurate per il Single Sign-On federato (SSO) con autenticazione basata su SAML
- Applicazioni proxy di applicazione che usano Azure Active Directory pre-autenticazione
- Le applicazioni basate sulla piattaforma Azure AD applicazione che usano l'autenticazione OAuth 2,0/OpenID Connect dopo che un utente o un amministratore ha acconsentito a tale applicazione.

Quando è richiesta l'assegnazione dell'utente, solo gli utenti assegnati in modo esplicito all'applicazione saranno in grado di eseguire l'accesso. Possono accedere all'app nella pagina App personali o usando un collegamento diretto. 

Quando l'assegnazione *non è richiesta*, perché è stata impostata questa opzione su **No** o perché l'applicazione usa un'altra modalità SSO, qualsiasi utente potrà accedere all'applicazione se dispone di un collegamento diretto all'applicazione o all' **URL di accesso utente** nella pagina delle **Proprietà** dell'applicazione. 

Questa impostazione non influisce sul fatto che un'applicazione venga visualizzata nel pannello di accesso app personali. Le applicazioni vengono visualizzate nei pannelli di accesso delle app personali degli utenti dopo aver assegnato un utente o un gruppo all'applicazione. Per informazioni di base, vedere [gestione dell'accesso alle app](what-is-access-management.md).


Per richiedere l'assegnazione dell'utente per un'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore o come proprietario dell'applicazione.

2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **applicazioni aziendali**.

3. Selezionare l'applicazione dall'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, usare i controlli filtro per selezionare il tipo di applicazione, lo stato o la visibilità, quindi selezionare **applica**.

4. Nel menu di spostamento a sinistra selezionare **Proprietà**.

5. Assicurarsi che l' **assegnazione dell'utente sia obbligatoria?** l'interruttore sia impostato su **Sì**.

   > [!NOTE]
   > Se l' **assegnazione utente è obbligatoria?** l'interruttore non è disponibile, è possibile usare PowerShell per impostare la proprietà della approleassignmentrequired nell'entità servizio.

6. Selezionare il pulsante **Salva** nella parte superiore della schermata.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Assegnare utenti o gruppi a un'app tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un amministratore globale, un amministratore dell'applicazione o un account amministratore dell'applicazione cloud oppure come proprietario assegnato dell'app aziendale.
2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **applicazioni aziendali**.
3. Selezionare l'applicazione dall'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, usare i controlli filtro per selezionare il tipo di applicazione, lo stato o la visibilità, quindi selezionare **applica**.
4. Nel menu di spostamento a sinistra selezionare **utenti e gruppi**.
   > [!NOTE]
   > Per assegnare gli utenti alle applicazioni Microsoft, ad esempio le app di Office 365, alcune di queste app usano PowerShell. 
5. Selezionare il pulsante **Aggiungi utente** .
6. Nel riquadro **Aggiungi assegnazione** selezionare **utenti e gruppi**.
7. Selezionare l'utente o il gruppo che si desidera assegnare all'applicazione oppure iniziare a digitare il nome dell'utente o del gruppo nella casella di ricerca. È possibile scegliere più utenti e gruppi e le selezioni verranno visualizzate in **elementi selezionati**.
8. Al termine, fare clic su **Seleziona**.

   ![Assegnare un utente o gruppo all'app](./media/assign-user-or-group-access-portal/assign-users.png)

9. Nel riquadro **utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco, quindi scegliere il pulsante **Seleziona** nella parte inferiore del riquadro.
10. Se l'applicazione lo supporta, è possibile assegnare un ruolo all'utente o al gruppo. Nel riquadro **Aggiungi assegnazione** scegliere **Seleziona ruolo**. Quindi, nel riquadro **Seleziona ruolo** , scegliere un ruolo da applicare agli utenti o ai gruppi selezionati, quindi fare clic su **OK** nella parte inferiore del riquadro. 

    > [!NOTE]
    > Se l'applicazione non supporta la selezione dei ruoli, viene assegnato il ruolo di accesso predefinito. In questo caso, l'applicazione gestisce il livello di accesso degli utenti.

2. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **assegna** nella parte inferiore del riquadro.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Assegnare utenti o gruppi a un'app tramite PowerShell

1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

   > [!NOTE]
   > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

1. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
1. Usare lo script seguente per assegnare un utente e un ruolo a un'applicazione:

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

1. In questo esempio il nome esatto del ruolo applicazione da assegnare a Britta Simon non è noto. Eseguire i comandi seguenti per ottenere l'utente ($user) e l'entità servizio ($sp) usando i nomi visualizzati di UPN utente ed entità servizio.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Eseguire il comando `$sp.AppRoles` per visualizzare i ruoli disponibili per l'applicazione Workplace Analytics. In questo esempio si vuole assegnare a Britta Simon il ruolo di analista (accesso limitato).

   ![Mostra i ruoli disponibili per un utente con il ruolo di analisi dell'area di lavoro](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Assegnare il nome del ruolo alla variabile `$app_role_name`.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Eseguire il comando seguente per assegnare l'utente al ruolo applicazione:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Articoli correlati

- [Altre informazioni sull'accesso degli utenti finali alle applicazioni](end-user-experiences.md)
- [Pianificare una distribuzione del pannello di accesso Azure AD](access-panel-deployment-plan.md)
- [Gestione dell'accesso alle app](what-is-access-management.md)
 
## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
- [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
- [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
