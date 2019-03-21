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
ms.date: 02/14/2018
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b72ec628e048560fbfb9da63123bbb7461811b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074285"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in Azure Active Directory
Rimuovere un utente o un gruppo dalla possibilità di accedere a una delle applicazioni aziendali in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale ed essere amministratore globale della directory.

> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per rimuovere utenti da un'app aziendale.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale nel portale di Azure?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
3. Nella pagina **Azure Active Directory - \*nomedirectory\*,** vale a dire la pagina Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. Nella pagina **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Verrà visualizzato un elenco di app che è possibile gestire.
5. Nella pagina **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.
6. Nella pagina ***nomeapp***, ovvero la pagina con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione di utenti o gruppi](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. Nella pagina ***nomeapp*** di **assegnazione utente e gruppo** selezionare uno o più utenti o gruppi e quindi fare clic sul comando **Rimuovi**. Confermare la decisione al prompt dei comandi.

    ![Selezione del comando Rimuovi](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale con PowerShell?
1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

    >[!NOTE] 
    > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

2. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
3. Usare lo script seguente per rimuovere un ruolo e l'utente da un'applicazione:

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
