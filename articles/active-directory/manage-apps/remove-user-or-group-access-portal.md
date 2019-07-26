---
title: Rimuovere le assegnazioni di utenti o gruppi da un'app in Azure Active Directory | Microsoft Docs
description: Come rimuovere l'assegnazione di accesso di un utente o gruppo da un'app aziendale in anteprima di Azure Active Directory
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
ms.openlocfilehash: 380816283156969c47f45a9b47435688df91f4ca
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381056"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in Azure Active Directory

È facile rimuovere un utente o un gruppo dall'accesso assegnato a una delle applicazioni aziendali in Azure Active Directory (Azure AD). Per gestire l'app aziendale sono necessarie le autorizzazioni appropriate. È necessario essere un amministratore globale per la directory.

> [!NOTE]
> Per le applicazioni Microsoft, come le app di Office 365, usare PowerShell per rimuovere utenti da un'app aziendale.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale nel portale di Azure?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
1. Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
1. Nella pagina **Azure Active Directory- *DirectoryName***  , ovvero la pagina Azure ad per la directory che si sta gestendo, selezionare **applicazioni aziendali**.
1. Nella pagina **applicazioni aziendali-tutte le applicazioni** verrà visualizzato un elenco delle app che è possibile gestire. Selezionare un'app.
1. Nella pagina Panoramica di ***appname*** , ovvero la pagina con il nome dell'app selezionata nel titolo, selezionare **utenti & gruppi**.
1. Nella pagina ***nomeapp*** di **assegnazione utente e gruppo** selezionare uno o più utenti o gruppi e quindi fare clic sul comando **Rimuovi**. Confermare la decisione al prompt dei comandi.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Come si rimuove un'assegnazione di utente o gruppo a un'app aziendale con PowerShell?

1. Aprire un prompt dei comandi di Windows PowerShell con privilegi elevati.

   > [!NOTE]
   > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

1. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.
1. Usare lo script seguente per rimuovere un utente e un ruolo da un'applicazione:

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
