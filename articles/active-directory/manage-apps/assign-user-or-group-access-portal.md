---
title: Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come selezionare un'app aziendale a cui assegnare un utente o gruppo in anteprima di Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15f7e830079c224e9e15dd45d14c1741376f8762
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851704"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Assegnare un utente o un gruppo a un'app aziendale in Azure Active Directory

Per assegnare un utente o un gruppo a un'app aziendale, è necessario avere le autorizzazioni appropriate per gestire l'app aziendale ed essere l'amministratore globale per la directory. Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per assegnare utenti a un'app aziendale.

> [!NOTE]
> Per i requisiti di licenza per le funzionalità descritte in questo articolo, vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Assegnare un utente a un'app - Portale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
1. Selezionare **Tutti i servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
1. Selezionare **Applicazioni aziendali**.
1. Nel riquadro **applicazioni aziendali-tutte le applicazioni** viene visualizzato un elenco delle app che è possibile gestire. Selezionare un'app.
1. Nel riquadro ***appname*** , ovvero il riquadro con il nome dell'app selezionata nel titolo, selezionare **Users & Groups**.
1. Nel riquadro ***appname*** **-utenti e gruppi** Selezionare **Aggiungi utente**.
1. Nel riquadro **Aggiungi assegnazione** selezionare **utenti e gruppi**.

   ![Assegnare un utente o gruppo all'app](./media/assign-user-or-group-access-portal/assign-users.png)

1. Nel riquadro **utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco, quindi scegliere il pulsante **Seleziona** nella parte inferiore del riquadro.
1. Nel riquadro **Aggiungi assegnazione** selezionare **Role**. Quindi, nel riquadro **Selezione ruolo** selezionare un ruolo da applicare agli utenti o ai gruppi selezionati, quindi fare clic su **OK** nella parte inferiore del riquadro.
1. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **assegna** nella parte inferiore del riquadro. Gli utenti o i gruppi assegnati avranno le autorizzazioni definite dal ruolo selezionato per questa app aziendale.

## <a name="allow-all-users-to-access-an-app---portal"></a>Consentire agli utenti di accedere a un'app - Portale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
1. Selezionare **Tutti i servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
1. Selezionare **Applicazioni aziendali**.
1. Nel riquadro **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco delle app che è possibile gestire.
1. Nel riquadro **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.
1. Nel riquadro ***appname*** selezionare **Proprietà**.
1. Nel riquadro  ***appname* -Properties** impostare l' **assegnazione utente obbligatoria?** su **No**.

L'opzione **Assegnazione utenti obbligatoria**:

- Se questa opzione è impostata su Sì, gli utenti devono prima essere assegnati a questa applicazione prima di potervi accedere.
- Se questa opzione è impostata su No, a tutti gli utenti che accedono all'URL di collegamento diretto o all'URL dell'applicazione verrà concesso l'accesso
- Non influisce sull'eventuale visualizzazione di un'applicazione nel pannello di accesso dell'applicazione. Per visualizzare l'applicazione nel pannello di accesso, assegnare un utente o un gruppo appropriato all'applicazione.
- Solo le funzioni con le applicazioni cloud configurate per l'accesso Single Sign-On SAML, le applicazioni proxy di applicazione che usano Azure Active Directory la pre-autenticazione o le applicazioni compilate direttamente nella piattaforma di applicazione Azure AD che usano OAuth 2,0/ Autenticazione OpenID Connect dopo che un utente o un amministratore ha acconsentito a tale applicazione. Vedere [Accesso Single Sign-On alle applicazioni](what-is-single-sign-on.md). Vedere [Configurare il consenso utente in un'applicazione](configure-user-consent.md).
- Questa opzione non ha alcun effetto quando un'applicazione viene configurata per le altre modalità Single Sign-on.

## <a name="assign-a-user-to-an-app---powershell"></a>Assegnare un utente a un'app - PowerShell

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

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
- [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
- [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
