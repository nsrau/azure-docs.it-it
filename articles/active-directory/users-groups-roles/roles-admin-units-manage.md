---
title: Aggiunta e rimozione di unità amministrative (anteprima) - Azure Active Directory Documenti Microsoft
description: Usare le unità amministrative per limitare l'ambito delle autorizzazioni per i ruoli in Azure Active Directory.Use administrative units to restrict the scope of role permissions in Azure Active Directory.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684906"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gestire le unità amministrative in Azure Active DirectoryManage administrative units in Azure Active Directory

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare gli utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative.

## <a name="get-started"></a>Introduzione

1. Per eseguire query dalle istruzioni seguenti tramite [Graph Explorer](https://aka.ms/ge), eseguire le operazioni seguenti:

    a. Nel portale di Azure passare ad Azure AD. Nell'elenco delle applicazioni selezionare **Graph Explorer**, quindi **selezionare Grant admin consent to Graph Explorer**.

    ![Screenshot che mostra il collegamento a "Concedere il consenso dell'amministratore"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. In Graph Explorer, selezionare la versione **beta.**

    ![Schermata che mostra la versione beta selezionata](./media/roles-admin-units-manage/select-beta-version.png)

1. Usare la versione di anteprima di Azure AD PowerShell.Use the preview version of Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Aggiungere un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare ad Azure AD e quindi nel riquadro sinistro selezionare **Unità amministrative**.

    ![Screenshot del collegamento Unità amministrative (anteprima) in Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selezionare **Aggiungi** e quindi immettere il nome dell'unità amministrativa. Facoltativamente, aggiungere una descrizione dell'unità amministrativa.

    ![Screenshot del pulsante Aggiungi e della casella di testo per l'immissione del nome dell'unità amministrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selezionare **Aggiungi** per finalizzare l'unità amministrativa.

### <a name="use-powershell"></a>Usare PowerShell

Installare Azure AD PowerShell (anteprima) prima di provare a eseguire i comandi seguenti:Install Azure AD PowerShell (preview) before you try to run the following commands:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

È possibile modificare i valori racchiusi tra virgolette, in base alle esigenze.

### <a name="use-microsoft-graph"></a>Utilizzare Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Rimuovere un'unità amministrativa

In Azure AD è possibile rimuovere un'unità amministrativa non più necessaria come unità di ambito per i ruoli amministrativi.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare a**Unità amministrative** **di Azure AD.** >  
1. Selezionare l'unità amministrativa da eliminare, quindi scegliere **Elimina**. 
1. Per confermare l'eliminazione dell'unità amministrativa, selezionare **Sì**. L'unità amministrativa viene eliminata.

![Screenshot del pulsante Elimina dell'unità amministrativa e della finestra di conferma](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Usare PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

È possibile modificare i valori racchiusi tra virgolette, in base alle esigenze dell'ambiente specifico.

### <a name="use-the-graph-api"></a>Usare l'API GraphUse the Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli utenti in un'unità amministrativa](roles-admin-units-add-manage-users.md)
* [Gestire i gruppi in un'unità amministrativa](roles-admin-units-add-manage-groups.md)
