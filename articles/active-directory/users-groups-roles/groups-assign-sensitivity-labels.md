---
title: Assegnare etichette di riservatezza ai gruppi-Azure AD | Microsoft Docs
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
ms.openlocfilehash: 51b242a76e1daec7d401d797e8c9887821117246
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656943"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Assegnare etichette di riservatezza ai gruppi di Office 365 in Azure Active Directory (anteprima)

Azure Active Directory (Azure AD) supporta l'applicazione di etichette di riservatezza pubblicate dal [centro di conformità Microsoft 365](https://sip.protection.office.com/homepage) ai gruppi di Office 365. Le etichette di riservatezza si applicano al gruppo tra servizi come Outlook, Microsoft teams e SharePoint. Questa funzionalità è attualmente in anteprima pubblica. Per ulteriori informazioni sul supporto delle app di Office 365, vedere [supporto di office 365 per le etichette di riservatezza](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels).

> [!IMPORTANT]
> Per configurare questa funzionalità, è necessario che sia presente almeno una licenza attiva Azure Active Directory Premium P1 nell'organizzazione Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Abilitare il supporto dell'etichetta di riservatezza in PowerShell

Per applicare le etichette pubblicate ai gruppi, è innanzitutto necessario abilitare la funzionalità. Questa procedura consente di abilitare la funzionalità in Azure AD.

1. Aprire una finestra di Windows PowerShell nel computer in uso. È possibile aprirla senza privilegi elevati.
1. Eseguire i comandi seguenti per preparare l'esecuzione dei cmdlet.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Nella pagina **accedi al tuo account** immettere l'account e la password di amministratore per connettersi al servizio e selezionare **Accedi**.
1. Recuperare le impostazioni di gruppo correnti per l'organizzazione Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se per questa organizzazione Azure AD non sono state create impostazioni di gruppo, è necessario innanzitutto creare le impostazioni. Attenersi alla procedura descritta in [Azure Active Directory cmdlets per configurare le impostazioni di gruppo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) per creare le impostazioni di gruppo per l'organizzazione Azure ad.

1. Successivamente, visualizzare le impostazioni del gruppo corrente.

    ```PowerShell
    $Setting.Values
    ```

1. Abilitare quindi la funzionalità:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Salvare quindi le modifiche e applicare le impostazioni:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

È tutto. È stata abilitata la funzionalità ed è possibile applicare etichette pubblicate ai gruppi.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Assegnare un'etichetta a un nuovo gruppo in portale di Azure

1. Accedere al centro di [amministrazione Azure ad](https://aad.portal.azure.com).
1. Selezionare **gruppi**, quindi selezionare **nuovo gruppo**.
1. Nella pagina **nuovo gruppo** selezionare **Office 365**, quindi compilare le informazioni necessarie per il nuovo gruppo e selezionare un'etichetta di riservatezza nell'elenco.

   ![Assegnare un'etichetta di riservatezza nella pagina nuovi gruppi](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Salvare le modifiche e selezionare **Crea**.

Il gruppo viene creato e le impostazioni del sito e del gruppo associate all'etichetta selezionata vengono quindi applicate automaticamente.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Assegnare un'etichetta a un gruppo esistente in portale di Azure

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore di gruppi o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Nella pagina **tutti i gruppi** selezionare il gruppo che si desidera assegnare all'etichetta.
1. Nella pagina del gruppo selezionato selezionare **Proprietà** e selezionare un'etichetta di riservatezza nell'elenco.

   ![Assegnare un'etichetta di riservatezza nella pagina Panoramica di un gruppo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Fare clic su **Salva** per salvare le modifiche.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Rimuovere un'etichetta da un gruppo esistente in portale di Azure

1. Accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore globale o di gruppo gruppi o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Dalla pagina **tutti i gruppi** , selezionare il gruppo da cui si vuole rimuovere l'etichetta.
1. Nella pagina **gruppo** selezionare **Proprietà**.
1. Selezionare **Rimuovi**.
1. Selezionare **Salva** per applicare le modifiche.

## <a name="using-classic-azure-ad-classifications"></a>Uso di classificazioni di Azure AD classiche

Dopo aver abilitato questa funzionalità, le classificazioni "classiche" per i gruppi verranno visualizzate solo per i gruppi e i siti esistenti ed è consigliabile usarle per i nuovi gruppi solo se si creano gruppi nelle app che non supportano le etichette di riservatezza. Se necessario, l'amministratore può convertirli in etichette di riservatezza in un secondo momento. Le classificazioni classiche sono le classificazioni precedenti configurate definendo i valori per l'impostazione di `ClassificationList` in Azure AD PowerShell. Quando questa funzionalità è abilitata, tali classificazioni non verranno applicate ai gruppi.

## <a name="troubleshooting-issues"></a>Risoluzione dei problemi

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Le etichette di riservatezza non sono disponibili per l'assegnazione in un gruppo

L'opzione etichetta riservatezza viene visualizzata solo per i gruppi quando vengono soddisfatte tutte le condizioni seguenti:

1. Le etichette vengono pubblicate nel centro di conformità Microsoft 365 per questo tenant.
1. La funzionalità è abilitata, EnableMIPLabels è impostato su true in PowerShell.
1. Il gruppo è un gruppo di Office 365.
1. Il tenant dispone di una licenza attiva Azure Active Directory Premium P1.
1. L'utente corrente che ha eseguito l'accesso dispone di privilegi sufficienti per assegnare le etichette. È necessario che l'utente sia un amministratore globale, un amministratore del gruppo o il proprietario del gruppo.

Verificare che tutte le condizioni siano soddisfatte per poter assegnare etichette a un gruppo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>L'etichetta che si vuole assegnare non è presente nell'elenco

Se l'etichetta che si sta cercando non è presente nell'elenco, questo potrebbe essere il caso per uno dei motivi seguenti:

- L'etichetta potrebbe non essere pubblicata nel centro di conformità Microsoft 365. Questo può essere applicato anche alle etichette che non sono più pubblicate. Per ulteriori informazioni, rivolgersi all'amministratore.
- L'etichetta può essere pubblicata, tuttavia, non è disponibile per l'utente che ha eseguito l'accesso. Per ulteriori informazioni su come ottenere l'accesso all'etichetta, rivolgersi all'amministratore.

### <a name="how-to-change-the-label-on-a-group"></a>Come modificare l'etichetta in un gruppo

Le etichette possono essere scambiate in qualsiasi momento usando gli stessi passaggi dell'assegnazione di un'etichetta a un gruppo esistente, come indicato di seguito:

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account di amministratore globale o di gruppo o come proprietario del gruppo.
1. Selezionare **Gruppi**.
1. Nella pagina **tutti i gruppi** selezionare il gruppo che si desidera assegnare all'etichetta.
1. Nella pagina del gruppo selezionato selezionare **Proprietà** e selezionare una nuova etichetta di riservatezza nell'elenco.
1. Selezionare **Salva**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Le modifiche alle impostazioni di gruppo apportate alle etichette pubblicate non vengono aggiornate nei gruppi

Come procedura consigliata, non è consigliabile modificare le impostazioni di gruppo per un'etichetta dopo che l'etichetta è stata applicata ai gruppi. Quando si apportano modifiche alle impostazioni di gruppo associate alle etichette pubblicate in [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage), tali modifiche non vengono applicate automaticamente ai gruppi interessati.

Se è necessario apportare una modifica, usare uno [script di Azure ad PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) per applicare manualmente gli aggiornamenti ai gruppi interessati. Questo metodo assicura che tutti i gruppi esistenti applichino la nuova impostazione.

## <a name="next-steps"></a>Passaggi successivi

- [Usare le etichette di riservatezza con Microsoft teams, gruppi di Office 365 e siti di SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Aggiornare i gruppi dopo aver modificato manualmente i criteri di etichetta con Azure AD script di PowerShell](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Modificare le impostazioni del gruppo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Gestire i gruppi con i comandi di PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
