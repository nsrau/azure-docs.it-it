---
title: Concedere le autorizzazioni utente per specifici criteri di lab | Documentazione Microsoft
description: Informazioni su come concedere le autorizzazioni utente per criteri di lab specifici nei laboratori di sviluppo/test in base alle esigenze di ogni utente
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328471"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Concedere le autorizzazioni utente per specifici criteri di lab
## <a name="overview"></a>Panoramica
In questo articolo viene illustrato come usare PowerShell per concedere agli utenti autorizzazioni per un particolare criterio di lab. In questo modo, le autorizzazioni possono essere applicate in base alle esigenze di ciascun utente. Ad esempio, è possibile concedere a un determinato utente la possibilità di modificare le impostazioni dei criteri delle macchine virtuali, ma non i criteri dei costi.

## <a name="policies-as-resources"></a>Criteri come risorse
Come illustrato nell'articolo [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) di Azure, il controllo degli accessi in base al ruolo di Azure consente la gestione degli accessi con granularità fine delle risorse per Azure. Con il controllo degli accessi in base al ruolo di Azure, è possibile separare i compiti all'interno del team di DevOps e concedere solo la quantità di accesso agli utenti necessari per svolgere i propri processi.

In DevTest Labs, un criterio è un tipo di risorsa che Abilita l'azione RBAC di Azure **Microsoft. DevTestLab/Labs/policySets/Policies/**. Ogni criterio Lab è una risorsa nel tipo di risorsa criteri e può essere assegnato come ambito a un ruolo di Azure.

Ad esempio, per concedere agli utenti l'autorizzazione di lettura/scrittura per i criteri di **dimensioni delle VM consentite** , è necessario creare un ruolo personalizzato che funzioni con **Microsoft. DevTestLab/Labs/policySets/Policies/** Action, quindi assegnare gli utenti appropriati a questo ruolo personalizzato nell'ambito di **Microsoft. DevTestLab/Labs/PolicySets/Policies/AllowedVmSizesInLab**.

Per altre informazioni sui ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Creazione di un ruolo personalizzato lab tramite PowerShell
Per iniziare, è necessario [installare Azure PowerShell](/powershell/azure/install-az-ps). 

Dopo aver configurato i cmdlet PowerShell di Azure, è possibile eseguire le attività seguenti:

* Elencare tutte le operazioni o azioni di un provider di risorse
* Elencare le azioni di un particolare ruolo:
* Creare un ruolo personalizzato

Lo script di PowerShell seguente offre alcuni esempi di esecuzione di queste attività:

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Assegnazione agli utenti delle autorizzazioni per un criterio specifico tramite ruoli personalizzati
Dopo aver definito i ruoli personalizzati, è possibile assegnarli agli utenti. Per assegnare un ruolo personalizzato a un utente, è necessario prima ottenere il valore **ObjectId** che rappresenta l'utente. A tale scopo, usare il cmdlet **Get-AzADUser** .

Nell'esempio seguente il valore **ObjectId** dell'utente *SomeUser* è 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Una volta ottenuto il valore **ObjectID** per l'utente e un nome di ruolo personalizzato, è possibile assegnare tale ruolo all'utente con il cmdlet **New-AzRoleAssignment** :

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

Nell'esempio precedente viene usato il criterio **AllowedVmSizesInLab** . È possibile usare uno dei seguenti criteri:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver concesso le autorizzazioni utente per specifici criteri di lab, considerare i seguenti passaggi successivi:

* [Proteggere l'accesso a un Lab](devtest-lab-add-devtest-user.md)
* [Imposta criteri Lab](devtest-lab-set-lab-policy.md)
* [Creare un modello Lab](devtest-lab-create-template.md)
* [Creare elementi personalizzati per le macchine virtuali](devtest-lab-artifact-author.md)
* [Aggiungere una VM a un lab](devtest-lab-add-vm.md)
