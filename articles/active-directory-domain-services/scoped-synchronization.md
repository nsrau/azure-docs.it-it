---
title: Sincronizzazione con ambito per Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come configurare la sincronizzazione con ambito da Azure AD a un dominio gestito di Servizi di dominio Azure Active Directory
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
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613053"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configurare la sincronizzazione con ambito da Azure AD a Servizi di dominio Azure Active DirectoryConfigure scoped synchronization from Azure AD to Azure Active Directory Domain Services

Per fornire servizi di autenticazione, Servizi di dominio Azure Active Directory (Azure AD DS) sincronizza utenti e gruppi da Azure AD. In un ambiente ibrido, gli utenti e i gruppi di un ambiente Servizi di dominio Active Directory locale possono prima essere sincronizzati con Azure AD tramite Azure AD Connect e quindi sincronizzati con Servizi di dominio Active Directory di Azure.

Per impostazione predefinita, tutti gli utenti e i gruppi da una directory di Azure AD vengono sincronizzati con un dominio gestito di Servizi di dominio Active Directory di Azure.By default, all users and groups from an Azure AD directory are synchronized to an Azure AD DS managed domain. Se si hanno esigenze specifiche, è invece possibile scegliere di sincronizzare solo un set definito di utenti.

Questo articolo illustra come creare un dominio gestito di Servizi di dominio Active Directory di Azure che usa la sincronizzazione con ambito e quindi modificare o disabilitare il set di utenti con ambito.

## <a name="scoped-synchronization-overview"></a>Panoramica della sincronizzazione con ambitoScoped synchronization overview

Per impostazione predefinita, tutti gli utenti e i gruppi da una directory di Azure AD vengono sincronizzati con un dominio gestito di Servizi di dominio Active Directory di Azure.By default, all users and groups from an Azure AD directory are synchronized to an Azure AD DS managed domain. Se solo pochi utenti devono accedere al dominio gestito, è possibile sincronizzare solo tali account utente. Questa sincronizzazione con ambito è basata su gruppo. Quando si configura la sincronizzazione con ambito basata su gruppo, solo gli account utente che appartengono ai gruppi specificati vengono sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure.When you configure group-based scoped synchronization, only the user accounts that belong to the groups you specify are synchronized to the Azure AD DS managed domain.

Nella tabella seguente viene descritto come utilizzare la sincronizzazione con ambito:

| Stato corrente | Stato desiderato | Configurazione necessaria |
| --- | --- | --- |
| Un dominio gestito esistente è configurato per sincronizzare tutti gli account utente e i gruppi. | Si desidera sincronizzare solo gli account utente che appartengono a gruppi specifici. | Non è possibile passare dalla sincronizzazione di tutti gli utenti all'utilizzo della sincronizzazione con ambito. [Eliminare il dominio gestito esistente](delete-aadds.md), quindi seguire i passaggi descritti in questo articolo per ricreare un dominio gestito di Servizi di dominio Active Directory di Azure con la sincronizzazione con ambito configurata. |
| Nessun dominio gestito esistente. | Si desidera creare un nuovo dominio gestito e sincronizzare solo gli account utente appartenenti a gruppi specifici. | Seguire i passaggi descritti in questo articolo per creare un dominio gestito di Servizi di dominio Active Directory di Azure con la sincronizzazione con ambito configurata. |
| Un dominio gestito esistente è configurato per sincronizzare solo gli account appartenenti a gruppi specifici. | Si desidera modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure.You want to modify the list of groups whose users should be synchronized to the Azure AD DS managed domain. | Seguire i passaggi descritti in questo articolo per modificare la sincronizzazione con ambito. |

Utilizzare il portale di Azure o PowerShell per configurare le impostazioni di sincronizzazione con ambito:You use the Azure portal or PowerShell to configure the scoped synchronization settings:

| Azione | | |
|--|--|--|
| Creare un dominio gestito di Servizi di dominio Active Directory di Azure e configurare la sincronizzazione con ambitoCreate an Azure AD DS managed domain and configure scoped synchronization | [Portale di Azure](#enable-scoped-synchronization-using-the-azure-portal) | [Powershell](#enable-scoped-synchronization-using-powershell) |
| Modificare la sincronizzazione con ambito | [Portale di Azure](#modify-scoped-synchronization-using-the-azure-portal) | [Powershell](#modify-scoped-synchronization-using-powershell) |
| Disabilitare la sincronizzazione con ambito | [Portale di Azure](#disable-scoped-synchronization-using-the-azure-portal) | [Powershell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> La modifica dell'ambito della sincronizzazione fa sì che il dominio gestito di Servizi di dominio Active Directory di Azure risincronizza tutti i dati.
> 
>  * Quando si modifica l'ambito di sincronizzazione per un dominio gestito di Azure AD DS, si verifica una risincronizzazione completa.
>  * Gli oggetti non più necessari nel dominio gestito di Servizi di dominio Active Directory di Azure vengono eliminati. Vengono creati nuovi oggetti nel dominio gestito.
>  * Il completamento della risincronizzazione potrebbe richiedere molto tempo. Il tempo di sincronizzazione dipende dal numero di oggetti, ad esempio utenti, gruppi e appartenenze ai gruppi, nel dominio gestito di Servizi di dominio Active Directory di Azure e nella directory di Azure AD. Per le directory di grandi dimensioni con molte centinaia di migliaia di oggetti, la risincronizzazione potrebbe richiedere alcuni giorni.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Abilitare la sincronizzazione con ambito tramite il portale di AzureEnable scoped synchronization using the Azure portal

1. Seguire [l'esercitazione per creare e configurare un'istanza](tutorial-create-instance-advanced.md)di Servizi di dominio Active Directory di Azure. Completare tutti i prerequisiti e i passaggi di distribuzione diversi dall'ambito di sincronizzazione.
1. Scegliere Ambito nel passaggio di sincronizzazione, quindi selezionare i gruppi di Azure AD da sincronizzare con l'istanza di Servizi di dominio Active Directory di Azure.Choose **Scoped** at the synchronization step, then select the Azure AD groups to synchronize to the Azure AD DS instance.

Il dominio gestito di Servizi di dominio Active Directory di Azure può richiedere fino a un'ora per completare la distribuzione. Nel portale di Azure, la pagina **Panoramica** per il dominio gestito di Servizi di dominio Active Directory di Azure mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure mostra che il dominio gestito di Azure AD DS ha terminato il provisioning, è necessario completare le attività seguenti:When the Azure portal shows that the Azure AD DS managed domain has finished provisioning, the following tasks need to be completed:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* [Abilitare](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) la sincronizzazione delle password con Servizi di dominio Azure AD in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificare la sincronizzazione con ambito tramite il portale di AzureModify scoped synchronization using the Azure portal

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti:To modify the list of groups whose users should be synchronized to the Azure AD DS managed domain, complete the following steps:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD.** Scegliere l'istanza, ad esempio *aaddscontoso.com*.
1. Selezionare **Sincronizzazione** dal menu sul lato sinistro.
1. Per aggiungere un gruppo, scegli **: seleziona** i gruppi nella parte superiore, quindi scegli i gruppi da aggiungere.
1. Per rimuovere un gruppo dall'ambito di sincronizzazione, selezionarlo dall'elenco dei gruppi attualmente sincronizzati e scegliere **Rimuovi gruppi**.
1. Una volta apportate tutte le modifiche, selezionare **Salva ambito di sincronizzazione**.

La modifica dell'ambito della sincronizzazione fa sì che il dominio gestito di Servizi di dominio Active Directory di Azure risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito di Servizi di dominio Active Directory di Azure vengono eliminati e il completamento della risincronizzazione potrebbe richiedere molto tempo.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Disabilitare la sincronizzazione con ambito tramite il portale di AzureDisable scoped synchronization using the Azure portal

Per disabilitare la sincronizzazione con ambito basata su gruppo per un dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti:To disable group-based scoped synchronization for an Azure AD DS managed domain, complete the following steps:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD.** Scegliere l'istanza, ad esempio *aaddscontoso.com*.
1. Selezionare **Sincronizzazione** dal menu sul lato sinistro.
1. Impostare l'ambito di sincronizzazione da **Ambito** a **Tutti**, quindi selezionare **Salva ambito di sincronizzazione**.

La modifica dell'ambito della sincronizzazione fa sì che il dominio gestito di Servizi di dominio Active Directory di Azure risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito di Servizi di dominio Active Directory di Azure vengono eliminati e il completamento della risincronizzazione potrebbe richiedere molto tempo.

## <a name="powershell-script-for-scoped-synchronization"></a>Script di PowerShell per la sincronizzazione con ambito

Per configurare la sincronizzazione con ambito tramite PowerShell, salvare innanzitutto lo script seguente in un file denominato `Select-GroupsToSync.ps1`. Questo script configura Servizi di dominio Active Directory per sincronizzare i gruppi selezionati da Azure AD. Tutti gli account utente che fanno parte dei gruppi specificati vengono sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure.All user accounts that are part of the specified groups are synchronized to the Azure AD DS managed domain.

Questo script viene utilizzato nei passaggi aggiuntivi di questo articolo.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Abilitare la sincronizzazione con ambito tramite PowerShellEnable scoped synchronization using PowerShell

Usare PowerShell per completare questa serie di passaggi. Consultare le istruzioni in [Abilitare Azure Active Directory Domain Services con PowerShell](powershell-create-instance.md). In questo articolo un paio di passaggi sono stati leggermente modificati per configurare la sincronizzazione con ambito.

1. Completare le attività seguenti dall'articolo per abilitare Servizi di dominio Active Directory di Azure tramite PowerShell.Complete the following tasks from the article to enable Azure AD DS using PowerShell. Interrompere il passaggio per creare effettivamente il dominio gestito. Configurare la sincronizzazione con ambito creare il dominio gestito di Servizi di dominio Active Directory di Azure.You configure the scoped synchronization you create the Azure AD DS managed domain.

   * Installare i moduli di [PowerShell necessari.](powershell-create-instance.md#prerequisites)
   * [Creare l'entità servizio richiesta e il gruppo di Azure AD per l'accesso amministrativo.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [Creare risorse di Azure di supporto, ad esempio una rete virtuale e subnet.](powershell-create-instance.md#create-supporting-azure-resources)

1. Determinare i gruppi e gli utenti che contengono che si desidera sincronizzare da Azure AD. Creare un elenco dei nomi visualizzati dei gruppi da sincronizzare con Servizi di dominio Active Directory di Azure.

1. Eseguire lo [script dalla sezione precedente](#powershell-script-for-scoped-synchronization) e utilizzare il parametro *-groupsToAdd* per passare l'elenco dei gruppi da sincronizzare.

   > [!WARNING]
   > È necessario includere il gruppo *AAD DC Administrators* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito di Azure AD DS è inutilizzabile.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Creare ora il dominio gestito di Servizi di dominio Active Directory di Azure e abilitare la sincronizzazione con ambito basata su gruppo. Includere *"filteredSync" - "Enabled"* nel parametro *-Properties.*

    Impostare l'ID sottoscrizione di Azure e quindi specificare un nome per il dominio gestito, ad esempio *aaddscontoso.com*. È possibile ottenere l'ID sottoscrizione utilizzando il cmdlet [Get-AzSubscription.][Get-AzSubscription] Impostare il nome del gruppo di risorse, il nome della rete virtuale e l'area sui valori usati nei passaggi precedenti per creare le risorse di Azure di supporto:Set the resource group name, virtual network name, to the values used in the previous steps to create the supporting Azure resources:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
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

La creazione della risorsa e la restituzione del controllo richiedono alcuni minuti al prompt di PowerShell.It takes a few minutes to create the resource and return control to the PowerShell prompt. Il dominio gestito di Servizi di dominio Active Directory di Azure continua a essere sottoposto a provisioning in background e può richiedere fino a un'ora per completare la distribuzione. Nel portale di Azure, la pagina **Panoramica** per il dominio gestito di Servizi di dominio Active Directory di Azure mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure mostra che il dominio gestito di Azure AD DS ha terminato il provisioning, è necessario completare le attività seguenti:When the Azure portal shows that the Azure AD DS managed domain has finished provisioning, the following tasks need to be completed:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Se è stato creato un dominio gestito di Servizi di dominio Active Directory di Azure in un'area che supporta le zone di disponibilità, creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito di Servizi di dominio Active Directory di Azure.If you created an Azure AD DS managed domain in a region that supports Availability zones, create a network security group to restrict traffic in the virtual network for the Azure AD DS managed domain. Viene creato un servizio di bilanciamento del carico standard di Azure che richiede il seposto di queste regole. Questo gruppo di sicurezza di rete protegge Servizi di dominio Active Directory ed è necessario per il corretto funzionamento del dominio gestito.
    * Per creare il gruppo di sicurezza di rete e le regole obbligatorie, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di creare e configurare automaticamente il gruppo di sicurezza di rete.
* [Abilitare](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) la sincronizzazione delle password con Servizi di dominio Azure AD in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificare la sincronizzazione con ambito tramite PowerShellModify scoped synchronization using PowerShell

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito di Servizi di dominio Active Directory di Azure, eseguire nuovamente lo script di [PowerShell](#powershell-script-for-scoped-synchronization) e specificare il nuovo elenco di gruppi. Nell'esempio riportato di seguito i gruppi da sincronizzare non includono più *NomeGruppo2*e ora *Include NomeGruppo3*.

> [!WARNING]
> È necessario includere il gruppo *AAD DC Administrators* nell'elenco dei gruppi per la sincronizzazione con ambito. Se non si include questo gruppo, il dominio gestito di Azure AD DS è inutilizzabile.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

La modifica dell'ambito della sincronizzazione fa sì che il dominio gestito di Servizi di dominio Active Directory di Azure risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito di Servizi di dominio Active Directory di Azure vengono eliminati e il completamento della risincronizzazione potrebbe richiedere molto tempo.

## <a name="disable-scoped-synchronization-using-powershell"></a>Disabilitare la sincronizzazione con ambito tramite PowerShellDisable scoped synchronization using PowerShell

Per disabilitare la sincronizzazione con ambito basata su gruppo per un dominio gestito di Servizi di dominio Active Directory di Azure, impostare *"filteredSync" su "Disabled"* nella risorsa di Servizi di dominio Azure, quindi aggiornare il dominio gestito. Al termine, tutti gli utenti e i gruppi sono impostati per la sincronizzazione da Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

La modifica dell'ambito della sincronizzazione fa sì che il dominio gestito di Servizi di dominio Active Directory di Azure risincronizza tutti i dati. Gli oggetti che non sono più necessari nel dominio gestito di Servizi di dominio Active Directory di Azure vengono eliminati e il completamento della risincronizzazione potrebbe richiedere molto tempo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul processo di sincronizzazione, vedere [Informazioni sulla sincronizzazione in Servizi](synchronization.md)di dominio Azure AD.

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
