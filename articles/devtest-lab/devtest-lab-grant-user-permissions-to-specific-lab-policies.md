    <properties
	pageTitle="Grant user permissions to specific DevTest Lab policies | Microsoft Azure"
	description="Learn how to grant user permissions to specific DevTest Lab policies based on each user's needs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="tarcher"/>

# Concedere le autorizzazioni utente per specifici criteri Lab di sviluppo/test

## Panoramica

In questo articolo viene illustrato come usare PowerShell per concedere agli utenti autorizzazioni per un particolare criterio Lab di sviluppo/test di Azure. In questo modo, le autorizzazioni possono essere applicate in base alle esigenze di ciascun utente. Ad esempio, è possibile concedere a un determinato utente la possibilità di modificare le impostazioni dei criteri delle macchine virtuali, ma non i criteri dei costi.

## Criteri come risorse

Come descritto nell'articolo [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md), il controllo degli accessi in base al ruolo consente la gestione specifica degli accessi delle risorse per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

In Lab di sviluppo/test, un criterio è un tipo di risorsa che abilita l'azione del controllo degli accessi in base al ruolo **Microsoft.DevTestLab/labs/policySets/policies/**. Ogni criterio Lab di sviluppo/test è una risorsa del tipo di risorsa Criterio e può essere assegnato come ambito a un ruolo del controllo degli accessi in base al ruolo.

Ad esempio, per concedere le autorizzazioni di lettura/scrittura agli utenti per il criterio **Allowed VM Sizes**, è possibile creare un ruolo personalizzato da usare con l'azione **Microsoft.DevTestLab/labs/policySets/policies/*** e assegnare gli utenti al ruolo nell'ambito di **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Per altre informazioni sui ruoli personalizzati nel controllo degli accessi in base al ruolo, vedere la sezione [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md#custom-roles-in-azure-rbac) dell'articolo [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

##Creazione di un ruolo personalizzato Lab di sviluppo/test tramite PowerShell
Per iniziare, leggere l'articolo seguente che descrive come installare e configurare i cmdlet PowerShell di Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Dopo aver configurato i cmdlet PowerShell di Azure, è possibile eseguire le attività seguenti:

- Elencare tutte le operazioni o azioni di un provider di risorse
- Elencare le azioni di un particolare ruolo:
- Creare un ruolo personalizzato

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

##Assegnazione agli utenti delle autorizzazioni per un criterio specifico tramite ruoli personalizzati
Dopo aver definito i ruoli personalizzati, è possibile assegnarli agli utenti. Per assegnare un ruolo personalizzato a un utente, è innanzitutto necessario ottenere il valore **ObjectId** che rappresenta l'utente. A tale scopo, usare il cmdlet **Get-AzureRmADUser**.

Nell'esempio seguente, il valore **ObjectId** dell'utente *SomeUser* è 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Dopo aver ottenuto il valore **ObjectId** dell'utente e il nome di un ruolo personalizzato, è possibile assegnare il ruolo all'utente usando il cmdlet **New AzureRmRoleAssignment**:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Nell'esempio precedente viene usato il criterio **AllowedVmSizesInLab**. È possibile usare uno dei seguenti criteri:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

## Passaggi successivi

Dopo aver concesso le autorizzazioni utente per specifici criteri Lab di sviluppo/test, considerare i seguenti passaggi successivi:

- [Proteggere l'accesso a un lab DevTest](devtest-lab-add-devtest-user.md).

- [Definire i criteri del lab](devtest-lab-set-lab-policy.md).

- [Creare un modello di lab](devtest-lab-create-template.md).

- [Creare elementi personalizzati per le VM](devtest-lab-artifact-author.md).

- [Aggiungere una VM con elementi a un lab DevTest di Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0413_2016-->