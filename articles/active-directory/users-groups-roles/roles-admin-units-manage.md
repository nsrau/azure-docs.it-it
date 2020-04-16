---
title: Aggiunta e rimozione di unità amministrative (anteprima) - Azure Active Directory Documenti Microsoft
description: Usare le unità amministrative per limitare l'ambito delle autorizzazioni per i ruoli in Azure Active DirectoryUse administrative units to restrict scope of role permissions in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428160"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gestire le unità amministrative in Azure Active DirectoryManage administrative units in Azure Active Directory

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare gli utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative.For more granular administrative control in Azure Active Directory (Azure AD), you can assign users to an Azure AD role with a scope limited to one or more administrative units (AUs).

## <a name="getting-started"></a>Introduzione

1. Per eseguire query dalle seguenti istruzioni tramite [Graph Explorer](https://aka.ms/ge), verificare quanto segue:

    1. Passare ad Azure AD nel portale e quindi nelle applicazioni selezionare Graph Explorer e fornire il consenso dell'amministratore a Graph Explorer.Go to Azure AD in the portal, and then in the applications select Graph Explorer and provide admin consent to Graph Explorer.

        ![seleziona Graph Explorer e fornisci il consenso dell'amministratore in questa pagina](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. In Graph Explorer, assicurarsi di selezionare la versione beta.

        ![selezionare la versione beta prima dell'operazione POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Usare la versione di anteprima di Azure AD PowerShell.Please use the preview version of Azure AD PowerShell. Istruzioni dettagliate sono qui.

## <a name="add-an-administrative-unit"></a>Aggiungere un'unità amministrativa

### <a name="azure-portal"></a>Portale di Azure

1. Passare ad Active Directory nel portale e selezionare Unità amministrative nel riquadro sinistro.

    ![passare alle unità amministrative in Azure Active Directory](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selezionare **Aggiungi**e specificare il nome dell'unità amministrativa e, facoltativamente, aggiungere una descrizione per l'unità amministrativa.

    ![Selezionare Aggiungi, quindi immettere un nome per l'unità amministrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selezionare **Aggiungi** per finalizzare l'unità amministrativa.

### <a name="powershell"></a>PowerShell

Installare Azure AD PowerShell (versione di anteprima) prima di provare a eseguire le azioni seguenti:Install Azure AD PowerShell (preview version) before trying to perform the actions below:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

I valori sopra evidenziati possono essere modificati in base alle esigenze.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Rimuovere un'unità amministrativa

In Azure Active Directory (Azure AD) è possibile rimuovere un'unità di amministrazione non più necessaria come unità di ambito per i ruoli amministrativi.

### <a name="azure-portal"></a>Portale di Azure

Passare ad **Azure AD > unità amministrative** nel portale. Selezionare l'unità amministrativa da eliminare, quindi scegliere **Elimina**. Dopo aver confermato **Sì**, l'unità amministrativa verrà eliminata.

![Selezionare un'unità amministrativa da eliminare](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="graph-api"></a>API Graph

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Passaggi successivi

[Gestione degli utenti nell'unità amministrativa](roles-admin-units-add-manage-users.md)
[Gestione dei gruppi nell'unità amministrativa](roles-admin-units-add-manage-groups.md)
