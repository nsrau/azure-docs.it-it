---
title: Assegnare etichette di sensibilità ai gruppi - Azure AD Documenti Microsoft
description: Come creare regole di appartenenza per popolare automaticamente i gruppi e informazioni di riferimento sulle regole.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329733"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Assegnare etichette di riservatezza ai gruppi di Office 365 in Azure Active Directory (anteprima)Assign sensitivity labels to Office 365 groups in Azure Active Directory (preview)

Azure Active Directory (Azure AD) supporta l'applicazione di etichette di riservatezza pubblicate dal [Centro conformità di Microsoft 365](https://sip.protection.office.com/homepage) ai gruppi di Office 365.Azure Active Directory (Azure AD) supports applying sensitivity labels published by the Microsoft 365 compliance center to Office 365 groups. Le etichette di riservatezza si applicano al gruppo tra servizi come Outlook, Microsoft Teams e SharePoint. Questa funzionalità è attualmente in anteprima pubblica. Per ulteriori informazioni sul supporto delle app di Office 365, vedere Supporto di [Office 365 per le etichette](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)di riservatezza .

> [!IMPORTANT]
> Per configurare questa funzionalità, è necessario che nell'organizzazione di Azure AD sia presente almeno una licenza di Azure Active Directory Premium P1 attiva.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Abilitare il supporto delle etichette di sensibilità in PowerShellEnable sensitivity label support in PowerShell

Per applicare le etichette pubblicate ai gruppi, è innanzitutto necessario abilitare la funzionalità. Questi passaggi abilitano la funzionalità in Azure AD.

1. Aprire una finestra di Windows PowerShell nel computer in uso. È possibile aprirla senza privilegi elevati.
1. Eseguire i comandi seguenti per preparare l'esecuzione dei cmdlet.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Nella pagina **Accedi all'account** immettere l'account amministratore e la password per connettersi al servizio e selezionare **Accedi**.
1. Recuperare le impostazioni del gruppo corrente per l'organizzazione di Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se non sono state create impostazioni di gruppo per questa organizzazione di Azure AD, è innanzitutto necessario creare le impostazioni. Seguire i passaggi descritti in [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) per creare le impostazioni di gruppo per questa organizzazione di Azure AD.

1. Successivamente, visualizzare le impostazioni del gruppo corrente.

    ```PowerShell
    $Setting.Values
    ```

1. Quindi attivare la funzione:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Quindi salvare le modifiche e applicare le impostazioni:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

È tutto. La funzionalità è stata abilitata ed è possibile applicare le etichette pubblicate ai gruppi.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Assegnare un'etichetta a un nuovo gruppo nel portale di AzureAssign a label to a new group in Azure portal

1. Accedere all'interfaccia di amministrazione di [Azure AD.](https://aad.portal.azure.com)
1. Selezionare **Gruppi**, quindi **Nuovo gruppo**.
1. Nella pagina **Nuovo gruppo** selezionare **Office 365**, quindi immettere le informazioni necessarie per il nuovo gruppo e selezionare un'etichetta di riservatezza dall'elenco.

   ![Assegnare un'etichetta di riservatezza nella pagina Nuovi gruppi](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Salvare le modifiche e selezionare **Crea**.

Il gruppo viene creato e le impostazioni del sito e del gruppo associate all'etichetta selezionata vengono quindi applicate automaticamente.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Assegnare un'etichetta a un gruppo esistente nel portale di AzureAssign a label to an existing group in Azure portal

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account amministratore di Gruppi o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Nella pagina **Tutti i gruppi** selezionare il gruppo a cui si desidera assegnare un'etichetta.
1. Nella pagina del gruppo selezionato, selezionare **Proprietà** e selezionare un'etichetta di riservatezza dall'elenco.

   ![Assegnare un'etichetta di riservatezza nella pagina di panoramica di un gruppo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selezionare **Salva** per salvare le modifiche.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Rimuovere un'etichetta da un gruppo esistente nel portale di AzureRemove a label from an existing group in Azure portal

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account amministratore globale o amministratore di gruppo o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Nella pagina **Tutti i gruppi** selezionare il gruppo da cui si desidera rimuovere l'etichetta.
1. Nella pagina **Gruppo** selezionare **Proprietà**.
1. Selezionare **Rimuovi**.
1. Selezionare **Salva** per applicare le modifiche.

## <a name="using-classic-azure-ad-classifications"></a>Uso delle classificazioni classiche di Azure ADUsing classic Azure AD classifications

Dopo aver abilitato questa funzionalità, le classificazioni "classiche" per i gruppi verranno visualizzate solo per i gruppi e i siti esistenti e sarà consigliabile utilizzarle per i nuovi gruppi solo se si creano gruppi in app che non supportano le etichette di riservatezza. L'amministratore può convertirli in etichette di sensibilità in un secondo momento, se necessario. Le classificazioni classiche sono le classificazioni precedenti `ClassificationList` configurate definendo i valori per l'impostazione in Azure AD PowerShell.Classic classifications are the old classifications you set up by defining values for the setting in Azure AD PowerShell. Quando questa funzionalità è abilitata, tali classificazioni non verranno applicate ai gruppi.

## <a name="troubleshooting-issues"></a>Risoluzione dei problemi

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Le etichette di sensibilità non sono disponibili per l'assegnazione in un gruppo

L'opzione dell'etichetta di sensibilità viene visualizzata solo per i gruppi quando vengono soddisfatte tutte le condizioni seguenti:The sensitivity label option is displayed for groups only when all the following conditions are met:

1. Le etichette vengono pubblicate nel Centro conformità Microsoft 365 per questo tenant.
1. La funzionalità è abilitata, EnableMIPLabels è impostata su True in PowerShell.The feature is enabled, EnableMIPLabels is set to True in PowerShell.
1. Il gruppo è un gruppo di Office 365.The group is an Office 365 group.
1. Il tenant dispone di una licenza active di Azure Active Directory Premium P1.
1. L'utente attualmente connesso dispone di privilegi sufficienti per assegnare etichette. L'utente deve essere un amministratore globale, un amministratore di gruppo o il proprietario del gruppo.

Assicurati che tutte le condizioni siano soddisfatte per assegnare etichette a un gruppo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>L'etichetta che si desidera assegnare non è presente nell'elenco

Se l'etichetta che si sta cercando non è nell'elenco, questo potrebbe essere il caso per uno dei seguenti motivi:

- L'etichetta potrebbe non essere pubblicata nel Centro conformità Microsoft 365. Ciò potrebbe valere anche per le etichette non più pubblicate. Per ulteriori informazioni, rivolgersi all'amministratore.
- L'etichetta può essere pubblicata, tuttavia, non è disponibile per l'utente che ha effettuato l'accesso. Rivolgersi all'amministratore per ulteriori informazioni su come ottenere l'accesso all'etichetta.

### <a name="how-to-change-the-label-on-a-group"></a>Come modificare l'etichetta in un gruppo

Le etichette possono essere scambiate in qualsiasi momento utilizzando la stessa procedura dell'assegnazione di un'etichetta a un gruppo esistente, come indicato di seguito:Labels can be swapped at any time using the same steps as assigning a label to an existing group, as follows:

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account amministratore globale o di gruppo o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Nella pagina **Tutti i gruppi** selezionare il gruppo a cui si desidera assegnare un'etichetta.
1. Nella pagina del gruppo selezionato, selezionare **Proprietà** e selezionare una nuova etichetta di riservatezza dall'elenco.
1. Selezionare **Salva**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Le modifiche alle impostazioni dei gruppi alle etichette pubblicate non vengono aggiornate nei gruppi

Come procedura consigliata, non è consigliabile modificare le impostazioni di gruppo per un'etichetta dopo l'applicazione dell'etichetta ai gruppi. Quando si apportano modifiche alle impostazioni dei gruppi associate alle etichette pubblicate nel [Centro conformità di Microsoft 365,](https://sip.protection.office.com/homepage)tali modifiche ai criteri non vengono applicate automaticamente ai gruppi interessati.

Se è necessario apportare una modifica, usare [uno script di Azure AD PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) per applicare manualmente gli aggiornamenti ai gruppi interessati. Questo metodo assicura che tutti i gruppi esistenti applichino la nuova impostazione.

## <a name="next-steps"></a>Passaggi successivi

- [Usare etichette di riservatezza con Microsoft Teams, gruppi di Office 365 e siti di SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Aggiornare manualmente i gruppi dopo la modifica dei criteri delle etichette con lo script di Azure AD PowerShellUpdate groups after label policy change manually with Azure AD PowerShell script](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Modificare le impostazioni del gruppo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Gestire i gruppi con i comandi di PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
