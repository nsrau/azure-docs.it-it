---
title: PowerShell per i ruoli di Azure AD in PIM - Azure AD | Microsoft Docs
description: Gestire i ruoli di Azure AD usando i cmdlet di PowerShell in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a3c5274a1b63d486bb7eb48b89560d5684db2e2
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316998"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell per i ruoli di Azure AD in Privileged Identity Management

Questo articolo contiene le istruzioni per l'uso di cmdlet PowerShell in Azure Active Directory (Azure AD) per gestire i ruoli di Azure AD in Privileged Identity Management (PIM). Offre inoltre informazioni su come configurare il modulo Azure AD PowerShell.

> [!Note]
> La versione di PowerShell ufficiale è supportata solo se si dispone della nuova versione di Azure AD Privileged Identity Management. Passare a Privileged Identity Management e assicurarsi di avere il banner seguente nel pannello di avvio rapido.
> [![verificare la versione di Privileged Identity Management installata](media/pim-how-to-add-role-to-user/pim-new-version.png "Selezionare Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se non si visualizza il banner, attendere: l'implementazione di questa esperienza aggiornata è in corso e avverrà nelle prossime settimane.
> I cmdlet PowerShell di Privileged Identity Management sono supportati tramite il modulo Anteprima di Azure AD. Nel caso sia stato usato un modulo diverso che restituisce ora un messaggio di errore, iniziare a usare il nuovo modulo. Se sono presenti sistemi di produzione basati su un modulo diverso, rivolgersi a [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Installazione e configurazione

1. Installare il modulo Anteprima di Azure AD

    ```powershell
    Install-module AzureADPreview
    ```

1. Prima di procedere, verificare di disporre delle autorizzazioni del ruolo necessarie. Se si sta tentando di svolgere attività di gestione come ad esempio l'assegnazione di un ruolo o l'aggiornamento dell'impostazione del ruolo, assicurarsi di disporre del ruolo di amministratore globale o di amministratore ruolo con privilegi. Se si sta semplicemente tentando di attivare un'assegnazione personale, non sono necessarie autorizzazioni oltre quelle predefinite dell'utente.

1. Collegarsi ad Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Trovare l'ID tenant per l'organizzazione Azure ad selezionando **Azure Active Directory**  >  **Properties**  >  **ID directory**proprietà. Nella sezione cmdlet usare questo ID ogni volta che è necessario specificare il resourceId.

    ![Individuare l'ID dell'organizzazione nelle proprietà dell'organizzazione di Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Le sezioni seguenti offrono semplici esempi per la configurazione e l'esecuzione. Per una documentazione più dettagliata sui cmdlet seguenti [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true](/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true) , vedere. Tuttavia, è necessario sostituire "azureResources" nel parametro providerID con "aadRoles". Sarà anche necessario ricordare di usare l'ID tenant per l'organizzazione Azure AD come parametro resourceId.

## <a name="retrieving-role-definitions"></a>Recupero delle definizioni dei ruoli

Usare il cmdlet seguente per ottenere tutti i ruoli di Azure AD predefiniti e personalizzati nell'organizzazione Azure AD. Questo passaggio importante consente di eseguire il mapping tra il nome del ruolo e roleDefinitionId. Il parametro roleDefinitionId viene usato in questi cmdlets per fare riferimento a un ruolo specifico.

Il parametro roleDefinitionId è specifico dell'organizzazione di Azure AD ed è diverso dal roleDefinitionId restituito dall'API di gestione dei ruoli.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Risultato:

![Ottenere tutti i ruoli per l'organizzazione di Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recupero delle assegnazioni di ruolo

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo nell'organizzazione di Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per uno specifico utente. Questo elenco è noto anche come "Ruoli personali" nel portale di Azure AD. L'unica differenza è che qui è stato aggiunto un filtro per l'ID oggetto. In questo contesto, l'ID oggetto corrisponde all'ID utente o all'ID gruppo.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Usare il cmdlet seguente per recuperare tutte le assegnazioni di ruolo per uno specifico ruolo. Qui roleDefinitionId corrisponde all'ID restituito dal cmdlet precedente.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

I cmdlet generano l'elenco di oggetti di assegnazione di ruolo mostrati di seguito. L'ID oggetto corrisponde all'ID utente dell'utente a cui è assegnato il ruolo. Lo stato dell'assegnazione può essere attivo o idoneo. Se l'utente è attivo ed è presente un ID nel campo LinkedEligibleRoleAssignmentId, significa che il ruolo è attualmente attivato.

Risultato:

![Recuperare tutte le assegnazioni di ruolo per l'organizzazione di Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Assegnare un ruolo

Usare il cmdlet seguente per creare un'assegnazione idonea.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

La pianificazione, che definisce l'ora di inizio e di fine dell'assegnazione, è un oggetto che può essere creato come nell'esempio seguente:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Se il valore di endDateTime è impostato su null, indica un'assegnazione permanente.

## <a name="activate-a-role-assignment"></a>Attivare un'assegnazione di ruolo

Usare il cmdlet seguente per attivare un'assegnazione idonea.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Questo cmdlet è quasi identico al cmdlet per la creazione di un'assegnazione di ruolo. La differenza principale tra i due cmdlet consiste nel fatto che, per il parametro di tipo, l'attivazione è "userAdd" anziché "adminAdd". Un'altra differenza è il fatto che il parametro AssignmentState è "Attivo" anziché "Idoneo".

> [!Note]
> Esistono due scenari di limitazione per l'attivazione del ruolo tramite PowerShell.
> 1. Se nell'impostazione del ruolo è necessario un sistema di ticket/numero di ticket, non è possibile fornirli come parametro. Di conseguenza, non sarebbe possibile attivare il ruolo al di là del portale di Azure. Questa funzionalità verrà implementata in PowerShell nei prossimi mesi.
> 1. Se per l'attivazione del ruolo è richiesta l'autenticazione a più fattori, attualmente PowerShell non può richiedere la verifica all'utente quando questi attiva il proprio ruolo. Al contrario, gli utenti dovranno attivare la richiesta di autenticazione a più fattori quando si connettono ad Azure AD seguendo [questo post di Blog](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) da uno dei nostri tecnici. Se si sta sviluppando un'applicazione per PIM, una possibile implementazione consiste nel richiedere verifica agli utenti e nel riconnetterli al modulo dopo che hanno ricevuto un errore "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recupero e aggiornamento delle impostazioni del ruolo

Usare il cmdlet seguente per accedere a tutte le impostazioni del ruolo nell'organizzazione Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Nell'impostazione sono presenti quattro oggetti principali. Attualmente, solo tre di questi oggetti vengono usati da PIM. UserMemberSettings sono impostazioni di attivazione, AdminEligibleSettings sono impostazioni di assegnazione per le assegnazioni idonee e AdminmemberSettings sono impostazioni di assegnazione per le assegnazioni attive.

[![Ottenere e aggiornare le impostazioni del ruolo.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Per aggiornare l'impostazione del ruolo è necessario ottenere l'oggetto impostazione esistente per un ruolo specifico e apportare le modifiche seguenti:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

A questo punto è possibile procedere e applicare l'impostazione a uno degli oggetti per uno specifico ruolo, come illustrato di seguito. Qui l'ID corrisponde all'ID dell'impostazione del ruolo, che può essere recuperato dal risultato dell'elenco cmdlet delle impostazioni del ruolo.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo personalizzato di Azure AD](azure-ad-custom-roles-assign.md)
- [Rimuovere o aggiornare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurare un'assegnazione di ruolo personalizzato di Azure AD](azure-ad-custom-roles-configure.md)
- [Definizioni dei ruoli in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)