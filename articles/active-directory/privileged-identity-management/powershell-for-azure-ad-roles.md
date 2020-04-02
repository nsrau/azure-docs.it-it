---
title: Ruoli di PowerShell per Azure AD in PIM - Azure AD Documenti Microsoft
description: Gestire i ruoli di Azure AD usando i cmdlet di PowerShell in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519658"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell for Azure AD roles in Privileged Identity Management

Questo articolo contiene istruzioni per l'uso dei cmdlet di PowerShell di Azure Active Directory (Azure AD) per gestire i ruoli di Azure AD in Privileged Identity Management (PIM). Offre inoltre informazioni su come configurare il modulo Azure AD PowerShell.

> [!Note]
> Il nostro PowerShell ufficiale è supportato solo se si utilizza la nuova versione di Azure AD Privileged Identity Management. Passare a Gestione identità con privilegi e assicurarsi di avere il banner seguente nel pannello di avvio rapido.
> [![controllare la versione di Privileged Identity Management in uso](media/pim-how-to-add-role-to-user/pim-new-version.png "Selezionare Azure AD > la gestione delle identità con privilegiSelect Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se non hai questo banner, attendi che siamo attualmente in procinto di implementare questa esperienza aggiornata nelle prossime settimane.
> I cmdlet di PowerShell per la gestione delle identità con privilegi sono supportati tramite il modulo Azure AD Preview. Se hai utilizzato un modulo diverso e quel modulo sta restituendo un messaggio di errore, inizia a utilizzare questo nuovo modulo. Se si dispone di sistemi di produzione costruiti su un modulo diverso, contattarepim_preview@microsoft.com

## <a name="installation-and-setup"></a>Installazione e configurazione

1. Installare il modulo di anteprima di Azure ADInstall the Azure AD Preview module

        Install-module AzureADPreview

1. Assicurarsi di disporre delle autorizzazioni di ruolo necessarie prima di procedere. Se si sta tentando di eseguire attività di gestione, ad esempio l'assegnazione di un ruolo o l'aggiornamento dell'impostazione del ruolo, assicurarsi di disporre del ruolo Amministratore globale o Amministratore di ruoli con privilegi. Se si sta solo tentando di attivare la propria assegnazione, non sono necessarie autorizzazioni oltre alle autorizzazioni utente predefinite.

1. Connettiti ad Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Trovare l'ID tenant accedendo all'ID**directory****delle proprietà** > di Azure **Active Directory** > . Nella sezione cmdlet usare questo ID ogni volta che è necessario fornire resourceId.

    ![Trovare l'ID tenant nelle proprietà per l'organizzazione di Azure ADFind the tenant ID in the properties for the Azure AD organization](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Le sezioni seguenti sono semplici esempi che consentono di iniziare a utilizzare l'utente. È possibile trovare una documentazione più https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementdettagliata relativa ai cmdlet seguenti all'indirizzo . Tuttavia, sarà necessario sostituire "azureResources" nel parametro providerID con "aadRoles". Sarà inoltre necessario ricordare di usare l'ID tenant per l'organizzazione di Azure AD come parametro resourceId.You will also need to remember to use the tenant ID for your Azure AD organization as the resourceId parameter.

## <a name="retrieving-role-definitions"></a>Recupero delle definizioni di ruoloRetrieving role definitions

Usare il cmdlet seguente per ottenere tutti i ruoli di Azure AD predefiniti e personalizzati nell'organizzazione di Azure AD (tenant). Questo passaggio importante offre il mapping tra il nome del ruolo e il roleDefinitionId. RoleDefinitionId viene utilizzato in questi cmdlet per fare riferimento a un ruolo specifico.

RoleDefinitionId è specifico dell'organizzazione di Azure AD ed è diverso dal roleDefinitionId restituito dall'API di gestione dei ruoli.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Risultato:

![Ottenere tutti i ruoli per l'organizzazione di Azure ADGet all roles for the Azure AD organization](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recupero delle assegnazioni di ruoloRetrieving role assignments

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo nell'organizzazione di Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Utilizzare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per un utente specifico. Questo elenco è noto anche come "Ruoli personali" nel portale di Azure AD. L'unica differenza è che è stato aggiunto un filtro per l'ID soggetto. L'ID soggetto in questo contesto è l'ID utente o l'ID gruppo.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Utilizzare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per un determinato ruolo. RoleDefinitionId qui è l'ID restituito dal cmdlet precedente.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

I cmdlet generano un elenco di oggetti assegnazione di ruolo illustrato di seguito. L'ID soggetto è l'ID utente dell'utente a cui è assegnato il ruolo. Lo stato dell'assegnazione può essere attivo o idoneo. Se l'utente è attivo ed è presente un ID nel campo LinkedEligibleRoleAssignmentId, significa che il ruolo è attualmente attivato.

Risultato:

![Recuperare tutte le assegnazioni di ruolo per l'organizzazione di Azure ADRetrieve all role assignments for the Azure AD organization](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Assegnare un ruolo

Utilizzare il cmdlet seguente per creare un'assegnazione idonea.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

La pianificazione, che definisce l'ora di inizio e di fine dell'assegnazione, è un oggetto che può essere creato come nell'esempio seguente:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Se il valore di endDateTime è impostato su null, indica un'assegnazione permanente.

## <a name="activate-a-role-assignment"></a>Attivare un'assegnazione di ruolo

Utilizzare il cmdlet seguente per attivare un'assegnazione idonea.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Questo cmdlet è quasi identico al cmdlet per la creazione di un'assegnazione di ruolo. La differenza principale tra i cmdlet è che per il parametro –Type, l'attivazione è "userAdd" anziché "adminAdd". L'altra differenza è che il parametro –AssignmentState è "Active" anziché "Eligible".

> [!Note]
> Esistono due scenari di limitazione per l'attivazione dei ruoli tramite PowerShell.There are two limiting scenarios for role activation through PowerShell.
> 1. Se hai bisogno di un sistema di biglietti / numero di biglietto nella tua impostazione di ruolo, non c'è modo di fornire quelli come parametro. Pertanto, non sarebbe possibile attivare il ruolo oltre il portale di Azure.So, it would not be possible to activate the role beyond the Azure portal. Questa funzionalità è in fase di implementazione in PowerShell nei prossimi mesi.
> 1. Se è necessaria l'autenticazione a più fattori per l'attivazione del ruolo, non è attualmente possibile per PowerShell contestare l'utente quando attiva il proprio ruolo. Al contrario, gli utenti dovranno attivare la richiesta di autenticazione a più fattori quando si connettono ad Azure AD seguendo [questo post](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) di blog da uno dei nostri tecnici. Se si sviluppa un'app per PIM, una possibile implementazione consiste nel contestare gli utenti e riconnetterli al modulo dopo aver ricevuto un errore "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recupero e aggiornamento delle impostazioni dei ruoli

Usare il cmdlet seguente per ottenere tutte le impostazioni dei ruoli nell'organizzazione di Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Ci sono quattro oggetti principali nell'impostazione. Solo tre di questi oggetti sono attualmente utilizzati da PIM. Le impostazioni UserMemberSettings sono impostazioni di attivazione, AdminEligibleSettings sono impostazioni di assegnazione per le assegnazioni idonee e AdminmemberSettings sono impostazioni di assegnazione per le assegnazioni attive.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Per aggiornare l'impostazione del ruolo, è necessario innanzitutto definire un oggetto impostazione come segue:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

È quindi possibile procedere e applicare l'impostazione a uno degli oggetti per un ruolo specifico, come illustrato di seguito. L'ID qui è l'ID dell'impostazione del ruolo che può essere recuperato dal risultato del cmdlet list role settings.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
