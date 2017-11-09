---
title: Concedere le autorizzazioni utente per specifici criteri di lab | Documentazione Microsoft
description: Informazioni su come concedere le autorizzazioni utente per criteri di lab specifici nei laboratori di sviluppo/test in base alle esigenze di ogni utente
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: fa7c1e4435106d0fc25cb7f77f195eb48705772d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Concedere le autorizzazioni utente per specifici criteri di lab
## <a name="overview"></a>Overview
In questo articolo viene illustrato come usare PowerShell per concedere agli utenti autorizzazioni per un particolare criterio di lab. In questo modo, le autorizzazioni possono essere applicate in base alle esigenze di ciascun utente. Ad esempio, è possibile concedere a un determinato utente la possibilità di modificare le impostazioni dei criteri delle macchine virtuali, ma non i criteri dei costi.

## <a name="policies-as-resources"></a>Criteri come risorse
Come descritto nell'articolo [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md) , il controllo degli accessi in base al ruolo consente la gestione specifica degli accessi delle risorse per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

Nei lab di sviluppo/test un criterio è un tipo di risorsa che abilita l'azione del controllo degli accessi in base al ruolo **Microsoft.DevTestLab/labs/policySets/policies/**. Ogni criterio di lab è una risorsa del tipo di risorsa Criterio e può essere assegnato come ambito a un ruolo del controllo degli accessi in base al ruolo.

Per concedere ad esempio le autorizzazioni di lettura/scrittura agli utenti per il criterio **Allowed VM Sizes**, è possibile creare un ruolo personalizzato da usare con l'azione **Microsoft.DevTestLab/labs/policySets/policies/*** e assegnare gli utenti al ruolo nell'ambito di **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Per altre informazioni sui ruoli personalizzati in RBAC, vedere il [controllo di accesso ai ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Creazione di un ruolo personalizzato lab tramite PowerShell
Per iniziare, leggere l'articolo seguente che descrive come installare e configurare i cmdlet PowerShell di Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Dopo aver configurato i cmdlet PowerShell di Azure, è possibile eseguire le attività seguenti:

* Elencare tutte le operazioni o azioni di un provider di risorse
* Elencare le azioni di un particolare ruolo:
* Creare un ruolo personalizzato

Lo script di PowerShell seguente offre alcuni esempi di esecuzione di queste attività:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Assegnazione agli utenti delle autorizzazioni per un criterio specifico tramite ruoli personalizzati
Dopo aver definito i ruoli personalizzati, è possibile assegnarli agli utenti. Per assegnare un ruolo personalizzato a un utente, è necessario prima ottenere il valore **ObjectId** che rappresenta l'utente. A tale scopo, usare il cmdlet **Get-AzureRmADUser** .

Nell'esempio seguente il valore **ObjectId** dell'utente *SomeUser* è 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Dopo avere ottenuto il valore **ObjectId** dell'utente e il nome di un ruolo personalizzato, è possibile assegnare il ruolo all'utente usando il cmdlet **New-AzureRmRoleAssignment**:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Nell'esempio precedente viene usato il criterio **AllowedVmSizesInLab** . È possibile usare uno dei seguenti criteri:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver concesso le autorizzazioni utente per specifici criteri di lab, considerare i seguenti passaggi successivi:

* [Proteggere l'accesso a un lab](devtest-lab-add-devtest-user.md).
* [Definire i criteri del lab](devtest-lab-set-lab-policy.md).
* [Creare un modello di lab](devtest-lab-create-template.md).
* [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md).
* [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md).

