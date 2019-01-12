---
title: Come creare un ruolo di registrazione per Azure Stack
description: Come creare un ruolo personalizzato per evitare di utilizzare Amministratore globale per la registrazione.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.openlocfilehash: 89fe0889e08da2365523b27262e912ff5403f7f1
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233180"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Creare un ruolo di registrazione per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Per gli scenari in cui non si vuole concedere le autorizzazioni di proprietario nella sottoscrizione di Azure, è possibile creare un ruolo personalizzato per assegnare autorizzazioni a un account utente per registrare Azure Stack.

> [!WARNING]
> Ciò non è una funzionalità di comportamento di sicurezza. Utilizzarlo in scenari in cui i vincoli per evitare modifiche accidentali alla sottoscrizione di Azure. Quando un utente è diritti delegati per questo ruolo personalizzato, l'utente dispone di diritti per modificare le autorizzazioni ed elevare i diritti. Assegnare solo agli utenti che attendibili per il ruolo personalizzato.

Durante la registrazione di Azure Stack, l'account di registrazione richiede le seguenti autorizzazioni di Azure Active Directory e sottoscrizione di Azure:

* **Autorizzazioni di registrazione dell'applicazione nel tenant di Azure Active Directory:** Gli amministratori dispongono delle autorizzazioni di registrazione dell'applicazione. L'autorizzazione per gli utenti è un'impostazione globale per tutti gli utenti nel tenant. Per visualizzare o modificare l'impostazione, vedere [creare un'entità applicazione e del servizio che può accedere alle risorse di Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    Il *utenti possono registrare applicazioni* impostazione deve essere impostata su **Yes** per poter abilitare un account utente per registrare Azure Stack. Se l'impostazione registrazioni per l'app è impostato su **No**, è possibile usare un account utente e deve usare un account amministratore globale per Azure Stack di registri.

* **Un set di autorizzazioni sufficienti di sottoscrizione di Azure:** Gli utenti del gruppo di proprietari dispone di autorizzazioni sufficienti. Per altri account, è possibile assegnare il set di autorizzazioni per l'assegnazione di un ruolo personalizzato come descritto nelle sezioni seguenti.

## <a name="create-a-custom-role-using-powershell"></a>Creare un ruolo personalizzato con PowerShell

Per creare un ruolo personalizzato, occorre avere l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` su tutti i `AssignableScopes`, come [Proprietario](../role-based-access-control/built-in-roles.md#owner) o [Amministratore accessi utente](../role-based-access-control/built-in-roles.md#user-access-administrator). Usare il modello JSON seguente per semplificare la definizione di ruolo personalizzato. Il modello crea un ruolo personalizzato che consente la lettura necessaria e accesso in scrittura per la registrazione di Azure Stack.

1. Creare un file JSON. Ad esempio, `C:\CustomRoles\registrationrole.json`
2. Aggiungere il codice JSON seguente al file. Sostituire <SubscriptionID> con l'ID della sottoscrizione di Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. In PowerShell, connettersi ad Azure per usare Azure Resource Manager. Quando richiesto, eseguire l'autenticazione con un account con autorizzazioni sufficienti, ad esempio [Owner](../role-based-access-control/built-in-roles.md#owner) oppure [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Per aggiungere il ruolo alla sottoscrizione, usare **New-AzureRmRoleDefinition** specificando il file modello JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Assegnare un utente al ruolo di registrazione

Dopo aver creato il ruolo personalizzato di registrazione, assegnare gli utenti di ruolo registrazione di Azure Stack.

1. Accedere con l'account con autorizzazioni sufficienti nella sottoscrizione di Azure per delegare, ad esempio i diritti - [Owner](../role-based-access-control/built-in-roles.md#owner) oppure [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. Nelle **abbonamenti**, selezionare **controllo di accesso (IAM) > aggiungere un'assegnazione di ruolo**.
3. Nelle **ruolo**, scegliere il ruolo personalizzato è stato creato *ruolo di Azure Stack registrazione*.
4. Selezionare gli utenti che si desidera assegnare al ruolo.
5. Selezionare **salvare** per assegnare gli utenti selezionati al ruolo.

    ![Selezionare gli utenti da assegnare al ruolo](media/azure-stack-registration-role/assign-role.png)

Per altre informazioni sull'uso di ruoli personalizzati, vedere [gestire l'accesso usando il portale di Azure e RBAC](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

[Registrare Azure Stack con Azure](azure-stack-registration.md)
