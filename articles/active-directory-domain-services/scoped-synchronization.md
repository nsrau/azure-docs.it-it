---
title: Sincronizzazione con ambito per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come configurare la sincronizzazione con ambito da Azure AD a un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 525ea421eb0fa0131fa91078b0619b8463f6fbb0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546236"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configurare la sincronizzazione con ambito da Azure AD a Azure Active Directory Domain Services

Per fornire servizi di autenticazione, Azure Active Directory Domain Services (Azure AD DS) sincronizza gli utenti e i gruppi da Azure AD. In un ambiente ibrido, è possibile sincronizzare gli utenti e i gruppi di un ambiente di Active Directory Domain Services locale (AD DS) con Azure AD utilizzando Azure AD Connect, quindi sincronizzati con Azure AD DS.

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD vengono sincronizzati con un dominio gestito Azure AD DS. Se si hanno esigenze specifiche, è invece possibile scegliere di sincronizzare solo un set di utenti definito.

Questo articolo illustra come creare un dominio gestito di Azure AD DS che usa la sincronizzazione con ambito e quindi modificare o disabilitare il set di utenti con ambito.

## <a name="scoped-synchronization-overview"></a>Panoramica della sincronizzazione con ambito

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD vengono sincronizzati con un dominio gestito Azure AD DS. Se solo alcuni utenti devono accedere al dominio gestito, è possibile sincronizzare solo gli account utente. Questa sincronizzazione con ambito è basata sul gruppo. Quando si configura la sincronizzazione con ambito basata su gruppo, solo gli account utente che appartengono ai gruppi specificati vengono sincronizzati con il dominio gestito di Azure AD DS.

La tabella seguente illustra come usare la sincronizzazione con ambito:

| Stato corrente | Stato desiderato | Configurazione necessaria |
| --- | --- | --- |
| Un dominio gestito esistente è configurato per sincronizzare tutti gli account utente e i gruppi. | Si desidera sincronizzare solo gli account utente che appartengono a gruppi specifici. | Non è possibile modificare la sincronizzazione di tutti gli utenti con la sincronizzazione con ambito. [Eliminare il dominio gestito esistente](delete-aadds.md), quindi seguire la procedura descritta in questo articolo per ricreare un dominio gestito Azure AD DS con la sincronizzazione con ambito configurata. |
| Nessun dominio gestito esistente. | Si desidera creare un nuovo dominio gestito e sincronizzare solo gli account utente appartenenti a gruppi specifici. | Eseguire la procedura descritta in questo articolo per creare un dominio gestito di Azure AD DS con la sincronizzazione con ambito configurata. |
| Un dominio gestito esistente è configurato per sincronizzare solo gli account che appartengono a gruppi specifici. | Si desidera modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Azure AD DS. | Eseguire la procedura descritta in questo articolo per modificare la sincronizzazione con ambito. |

Usare il portale di Azure o PowerShell per configurare le impostazioni di sincronizzazione con ambito:

| Azione | | |
|--|--|--|
| Creare un dominio gestito di Azure AD DS e configurare la sincronizzazione con ambito | [Portale di Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modificare la sincronizzazione con ambito | [Portale di Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Disabilitare la sincronizzazione con ambito | [Portale di Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Se si modifica l'ambito di sincronizzazione, il dominio gestito Azure AD DS Risincronizza tutti i dati.
> 
>  * Quando si modifica l'ambito di sincronizzazione per un dominio gestito Azure AD DS, viene eseguita una risincronizzazione completa.
>  * Gli oggetti che non sono più necessari nel dominio gestito Azure AD DS vengono eliminati. Vengono creati nuovi oggetti nel dominio gestito.
>  * Il completamento della risincronizzazione può richiedere molto tempo. Il tempo di sincronizzazione dipende dal numero di oggetti quali utenti, gruppi e appartenenze a gruppi nel dominio gestito Azure AD DS e nella directory Azure AD. Per le directory di grandi dimensioni con molte centinaia di migliaia di oggetti, la risincronizzazione potrebbe richiedere alcuni giorni.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Abilitare la sincronizzazione con ambito usando il portale di Azure

1. Seguire l' [esercitazione per creare e configurare un'istanza di Azure AD DS](tutorial-create-instance-advanced.md). Completare tutti i prerequisiti e i passaggi di distribuzione diversi da per l'ambito di sincronizzazione.
1. Scegliere **ambito** nel passaggio di sincronizzazione, quindi selezionare i gruppi di Azure ad da sincronizzare con l'istanza di Azure AD DS.

Il dominio gestito di Azure AD DS può richiedere fino a un'ora per completare la distribuzione. Nella portale di Azure la pagina **Panoramica** per il dominio gestito di Azure AD DS Mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure indica che il dominio gestito da Azure AD DS ha completato il provisioning, è necessario completare le attività seguenti:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare DNS, selezionare il dominio gestito di Azure AD DS nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* [Abilitare la sincronizzazione password per Azure ad Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificare la sincronizzazione con ambito usando il portale di Azure

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Azure AD DS, completare i passaggi seguenti:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere l'istanza, ad esempio *contoso.com*.
1. Selezionare **sincronizzazione** dal menu sul lato sinistro.
1. Per aggiungere un gruppo, scegliere **+ Seleziona gruppi** nella parte superiore, quindi scegliere i gruppi da aggiungere.
1. Per rimuovere un gruppo dall'ambito di sincronizzazione, selezionarlo dall'elenco dei gruppi attualmente sincronizzati e scegliere **Rimuovi gruppi**.
1. Quando vengono apportate tutte le modifiche, selezionare **Salva ambito di sincronizzazione**.

Se si modifica l'ambito di sincronizzazione, il dominio gestito Azure AD DS Risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito Azure AD DS vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Disabilitare la sincronizzazione con ambito usando il portale di Azure

Per disabilitare la sincronizzazione con ambito gruppo per un dominio gestito di Azure AD DS, completare i passaggi seguenti:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere l'istanza, ad esempio *contoso.com*.
1. Selezionare **sincronizzazione** dal menu sul lato sinistro.
1. Impostare l'ambito di sincronizzazione dall' **ambito** a **tutti**, quindi selezionare **Salva ambito di sincronizzazione**.

Se si modifica l'ambito di sincronizzazione, il dominio gestito Azure AD DS Risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito Azure AD DS vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="powershell-script-for-scoped-synchronization"></a>Script di PowerShell per la sincronizzazione con ambito

Per configurare la sincronizzazione con ambito usando PowerShell, salvare prima di tutto lo script seguente in un file denominato `Select-GroupsToSync.ps1`. Questo script configura Azure AD DS per sincronizzare i gruppi selezionati da Azure AD. Tutti gli account utente che fanno parte dei gruppi specificati vengono sincronizzati con il dominio gestito di Azure AD DS.

Questo script viene usato nei passaggi aggiuntivi di questo articolo.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization-using-powershell"></a>Abilitare la sincronizzazione con ambito usando PowerShell

Usare PowerShell per completare questa serie di passaggi. Consultare le istruzioni in [Abilitare Azure Active Directory Domain Services con PowerShell](powershell-create-instance.md). In questo articolo un paio di passaggi sono stati leggermente modificati per configurare la sincronizzazione con ambito.

1. Completare le attività seguenti dall'articolo per abilitare Azure AD DS usando PowerShell. Arrestare il passaggio per creare effettivamente il dominio gestito. Per configurare la sincronizzazione con ambito si crea il dominio gestito Azure AD DS.

   * [Installare i moduli di PowerShell necessari](powershell-create-instance.md#prerequisites).
   * [Creare l'entità servizio e il gruppo di Azure ad necessari per l'accesso amministrativo](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Creare risorse di supporto di Azure come una rete virtuale e le subnet](powershell-create-instance.md#create-supporting-azure-resources).

1. Determinare i gruppi e gli utenti che contengono che si desidera sincronizzare da Azure AD. Creare un elenco dei nomi visualizzati dei gruppi da sincronizzare con Azure AD DS.

1. Eseguire lo [script della sezione precedente](#powershell-script-for-scoped-synchronization) e utilizzare il parametro *-groupsToAdd* per passare l'elenco di gruppi da sincronizzare.

   > [!WARNING]
   > È necessario includere il gruppo di *amministratori di AAD DC* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito Azure AD DS è inutilizzabile.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Creare ora il dominio gestito di Azure AD DS e abilitare la sincronizzazione con ambito basata sui gruppi. Includere *"filteredSync" = "Enabled"* nel parametro *-Properties* .

    Impostare l'ID sottoscrizione di Azure e quindi specificare un nome per il dominio gestito, ad esempio *contoso.com*. È possibile ottenere l'ID sottoscrizione usando il cmdlet [Get-AzSubscription][Get-AzSubscription] . Impostare il nome del gruppo di risorse, il nome della rete virtuale e l'area sui valori usati nei passaggi precedenti per creare le risorse di Azure di supporto:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Sono necessari alcuni minuti per creare la risorsa e restituire il controllo al prompt di PowerShell. Il provisioning del dominio gestito di Azure AD DS continua a essere eseguito in background e può richiedere fino a un'ora per completare la distribuzione. Nella portale di Azure la pagina **Panoramica** per il dominio gestito di Azure AD DS Mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure indica che il dominio gestito da Azure AD DS ha completato il provisioning, è necessario completare le attività seguenti:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare DNS, selezionare il dominio gestito di Azure AD DS nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Se è stato creato un dominio gestito Azure AD DS in un'area che supporta zone di disponibilità, creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito di Azure AD DS. Viene creato un servizio di bilanciamento del carico standard di Azure che richiede che queste regole siano inserite. Questo gruppo di sicurezza di rete protegge Azure AD DS ed è necessario affinché il dominio gestito funzioni correttamente.
    * Per creare il gruppo di sicurezza di rete e le regole necessarie, selezionare il dominio gestito di Azure AD DS nel portale. Nella finestra **Panoramica** viene richiesto di creare e configurare automaticamente il gruppo di sicurezza di rete.
* [Abilitare la sincronizzazione password per Azure ad Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificare la sincronizzazione con ambito usando PowerShell

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Azure AD DS, eseguire nuovamente lo [script di PowerShell](#powershell-script-for-scoped-synchronization) e specificare il nuovo elenco di gruppi. Nell'esempio seguente i gruppi da sincronizzare non includono più *GroupName2*e ora includono *GroupName3*.

> [!WARNING]
> È necessario includere il gruppo di *amministratori di AAD DC* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito Azure AD DS è inutilizzabile.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Se si modifica l'ambito di sincronizzazione, il dominio gestito Azure AD DS Risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito Azure AD DS vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="disable-scoped-synchronization-using-powershell"></a>Disabilitare la sincronizzazione con ambito usando PowerShell

Per disabilitare la sincronizzazione con ambito gruppo per un dominio gestito di Azure AD DS, impostare *"filteredSync" = "disabled"* sulla risorsa Azure AD DS, quindi aggiornare il dominio gestito. Al termine, tutti gli utenti e i gruppi vengono impostati per la sincronizzazione da Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Se si modifica l'ambito di sincronizzazione, il dominio gestito Azure AD DS Risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito Azure AD DS vengono eliminati e il completamento della risincronizzazione può richiedere molto tempo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sincronizzazione, vedere informazioni sulla [sincronizzazione in Azure ad Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
