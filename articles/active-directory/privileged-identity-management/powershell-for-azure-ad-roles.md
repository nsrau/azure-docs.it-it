---
title: PowerShell per i ruoli di Azure AD in PIM-Azure AD | Microsoft Docs
description: Gestire i ruoli Azure AD usando i cmdlet di PowerShell in Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a6c0153105627e272d05af5514a030577431f7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233993"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell per i ruoli di Azure AD in Privileged Identity Management

Questo articolo contiene le istruzioni per l'uso dei cmdlet di PowerShell Azure Active Directory (Azure AD) per gestire i ruoli di Azure AD in Privileged Identity Management (PIM). Offre inoltre informazioni su come configurare il modulo Azure AD PowerShell.

> [!Note]
> Il nostro PowerShell ufficiale è supportato solo se si ha la nuova versione di Azure AD Privileged Identity Management. Passare a Privileged Identity Management e assicurarsi di avere il banner seguente nel pannello avvio rapido.
> [![controllare la versione di Privileged Identity Management](media/pim-how-to-add-role-to-user/pim-new-version.png "Selezionare Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se non si dispone di questo banner, attendere che sia in corso il processo di implementazione di questa esperienza aggiornata nelle prossime settimane.
> I cmdlet di Privileged Identity Management PowerShell sono supportati tramite il modulo Azure AD Preview. Se si usa un modulo diverso e il modulo restituisce un messaggio di errore, iniziare a usare il nuovo modulo. Se sono presenti sistemi di produzione basati su un modulo diverso, contattarepim_preview@microsoft.com

## <a name="installation-and-setup"></a>Installazione e configurazione

1. Installare il modulo Azure AD Preview

        Install-module AzureADPreview

1. Prima di procedere, assicurarsi di disporre delle autorizzazioni necessarie per il ruolo. Se si sta tentando di eseguire attività di gestione, ad esempio l'assegnazione di un ruolo o l'aggiornamento dell'impostazione del ruolo, assicurarsi di disporre del ruolo di amministratore globale o di amministratore del ruolo con privilegi. Se si sta tentando di attivare una propria assegnazione, non sono necessarie autorizzazioni oltre le autorizzazioni utente predefinite.

1. Connettiti ad Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Trovare l'ID tenant per l'organizzazione Azure ad selezionando **Azure Active Directory** > **Properties** > **ID directory**proprietà. Nella sezione cmdlet usare questo ID ogni volta che è necessario specificare il resourceId.

    ![Trovare l'ID organizzazione nelle proprietà dell'organizzazione Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Le sezioni seguenti sono semplici esempi che consentono di diventare operativi. Per una documentazione più dettagliata sui cmdlet seguenti https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management, vedere. Tuttavia, sarà necessario sostituire "azureResources" nel parametro providerID con "aadRoles". Sarà anche necessario ricordare di usare l'ID organizzazione per l'organizzazione Azure AD come parametro resourceId.

## <a name="retrieving-role-definitions"></a>Recupero di definizioni di ruolo

Usare il cmdlet seguente per ottenere tutti i ruoli Azure AD predefiniti e personalizzati nell'organizzazione Azure AD. Questo passaggio importante consente di eseguire il mapping tra il nome del ruolo e il roleDefinitionId. RoleDefinitionId viene utilizzato in tutti i cmdlet per fare riferimento a un ruolo specifico.

Il roleDefinitionId è specifico per l'organizzazione Azure AD ed è diverso dal roleDefinitionId restituito dall'API di gestione dei ruoli.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Risultato:

![Ottenere tutti i ruoli per l'organizzazione Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recupero delle assegnazioni di ruolo

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo nell'organizzazione Azure AD.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per un utente specifico. Questo elenco è noto anche come "ruoli personali" nel portale di Azure AD. L'unica differenza è che è stato aggiunto un filtro per l'ID oggetto. L'ID oggetto in questo contesto è l'ID utente o l'ID gruppo.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per un particolare ruolo. RoleDefinitionId è l'ID restituito dal cmdlet precedente.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

I cmdlet generano un elenco di oggetti di assegnazione di ruolo mostrati di seguito. L'ID oggetto è l'ID utente dell'utente a cui è assegnato il ruolo. Lo stato di assegnazione può essere attivo o idoneo. Se l'utente è attivo ed è presente un ID nel campo LinkedEligibleRoleAssignmentId, significa che il ruolo è attualmente attivato.

Risultato:

![Recuperare tutte le assegnazioni di ruolo per l'organizzazione Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Assegnare un ruolo

Usare il cmdlet seguente per creare un'assegnazione idonea.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

La pianificazione, che definisce l'ora di inizio e di fine dell'assegnazione, è un oggetto che può essere creato come nell'esempio seguente:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Se il valore di endDateTime è impostato su null, viene indicata un'assegnazione permanente.

## <a name="activate-a-role-assignment"></a>Attivare un'assegnazione di ruolo

Usare il cmdlet seguente per attivare un'assegnazione idonea.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Questo cmdlet è quasi identico al cmdlet per la creazione di un'assegnazione di ruolo. La differenza principale tra i cmdlet è che per il parametro-Type, Activation è "userAdd" anziché "adminAdd". L'altra differenza è che il parametro – AssignmentState è "Active" anziché "idoneo".

> [!Note]
> Esistono due scenari di limitazione per l'attivazione del ruolo tramite PowerShell.
> 1. Se nell'impostazione del ruolo è necessario il numero di sistema di ticket/ticket, non è possibile fornire tali numeri come parametro. Non sarebbe quindi possibile attivare il ruolo oltre il portale di Azure. Questa funzionalità viene implementata in PowerShell nei prossimi mesi.
> 1. Se per l'attivazione del ruolo è richiesta l'autenticazione a più fattori, attualmente non è possibile che PowerShell contesti l'utente quando attiva il proprio ruolo. Al contrario, gli utenti dovranno attivare la richiesta di autenticazione a più fattori quando si connettono a Azure AD seguendo [questo post di Blog](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) di uno dei nostri tecnici. Se si sta sviluppando un'app per PIM, una possibile implementazione consiste nel chiedere agli utenti di riconnetterli e riconnetterli al modulo dopo aver ricevuto un errore "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recupero e aggiornamento delle impostazioni dei ruoli

Usare il cmdlet seguente per ottenere tutte le impostazioni del ruolo nell'organizzazione Azure AD.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Sono presenti quattro oggetti principali nell'impostazione. PIM usa attualmente solo tre di questi oggetti. UserMemberSettings sono impostazioni di attivazione, AdminEligibleSettings sono impostazioni di assegnazione per le assegnazioni idonee e le AdminmemberSettings sono impostazioni di assegnazione per le assegnazioni attive.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Per aggiornare l'impostazione del ruolo, è necessario innanzitutto definire un oggetto Setting come indicato di seguito:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

È quindi possibile procedere e applicare l'impostazione a uno degli oggetti per un ruolo specifico, come illustrato di seguito. L'ID è l'ID dell'impostazione del ruolo che può essere recuperato dal risultato del cmdlet list Role Settings.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
