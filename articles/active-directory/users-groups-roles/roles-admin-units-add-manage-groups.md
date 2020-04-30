---
title: Aggiunta, rimozione ed elenco di gruppi in un'unità amministrativa (anteprima)-Azure Active Directory | Microsoft Docs
description: Gestire i gruppi e le autorizzazioni relative ai ruoli in un'unità amministrativa in Azure Active Directory
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683263"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Aggiunta e gestione di gruppi in unità amministrative in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere gruppi a un'unità amministrativa (AU) per un ambito amministrativo di controllo più granulare.

Per i passaggi necessari per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Aggiungere gruppi a un AU

### <a name="azure-portal"></a>Portale di Azure

Nell'anteprima è possibile assegnare gruppi solo singolarmente a un'unità amministrativa. Non è possibile eseguire l'assegnazione bulk dei gruppi a un'unità amministrativa. È possibile assegnare un gruppo a un'unità amministrativa in uno dei due modi seguenti nel portale:

1. Dalla pagina **gruppi di Azure AD >**

    Aprire la pagina Panoramica gruppi in Azure AD e selezionare il gruppo che deve essere assegnato all'unità amministrativa. Sul lato sinistro selezionare **unità amministrative** per elencare le unità amministrative a cui è assegnato il gruppo. Nella parte superiore viene rilevata l'opzione assegna a unità amministrativa e si fa clic su di essa per scegliere l'unità amministrativa sul lato destro.

    ![assegnare un gruppo singolarmente a un'unità amministrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Dalla **Azure AD > unità amministrative > pagina tutti i gruppi**

    Aprire il pannello tutti i gruppi in Azure AD > unità amministrative. Se sono già stati assegnati gruppi all'unità amministrativa, verranno visualizzati sul lato destro. Fare clic su **Aggiungi** nella parte superiore e un pannello di destra scorre l'elenco dei gruppi disponibili nell'organizzazione Azure ad. Selezionare uno o più gruppi da assegnare alle unità amministrative.

    ![Selezionare un'unità amministrativa e quindi selezionare Aggiungi membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

In questo esempio viene usato il cmdlet Add-AzureADAdministrativeUnitMember per aggiungere il gruppo all'unità amministrativa. L'ID oggetto dell'unità amministrativa e l'ID oggetto del gruppo da aggiungere vengono considerati argomenti. La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Esempio:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Elencare i gruppi in un AU

### <a name="azure-portal"></a>Portale di Azure

Passare a **Azure AD > unità amministrative** nel portale. Selezionare l'unità amministrativa per cui si vuole elencare gli utenti. Per impostazione predefinita, **tutti gli utenti** sono già selezionati nel riquadro sinistro. Selezionare **tutti i gruppi** e a destra è presente l'elenco dei gruppi che sono membri dell'unità amministrativa selezionata.

![Selezionare un'unità amministrativa da eliminare](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Ciò consentirà di ottenere tutti i membri dell'unità amministrativa. Se si desidera visualizzare tutti i gruppi membri dell'unità amministrativa, è possibile utilizzare il frammento di codice seguente:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Elenco di AUs per un gruppo

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure AD è possibile aprire i dettagli di un gruppo aprendo **gruppi**. Selezionare un gruppo per aprire il profilo del gruppo. Selezionare **unità amministrative** per elencare tutte le unità amministrative in cui è membro il gruppo.

![Elencare le unità amministrative per un gruppo](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Rimuovere un gruppo da un'AU

### <a name="azure-portal"></a>Portale di Azure

Esistono due modi per rimuovere un gruppo da un'unità amministrativa nella portale di Azure.

Aprire i**gruppi** di **Azure ad** > e aprire il profilo per il gruppo che si desidera rimuovere dall'unità amministrativa. Selezionare **unità amministrative** nel pannello sinistro per elencare tutte le unità amministrative in cui è membro il gruppo. Selezionare l'unità amministrativa da cui si desidera rimuovere il gruppo, quindi selezionare **Rimuovi da unità amministrativa**.

![Rimuovere un gruppo da un'unità amministrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

In alternativa, è possibile passare a **Azure ad** > **unità amministrative** e selezionare l'unità amministrativa in cui è membro il gruppo. Selezionare **gruppi** nel pannello sinistro per elencare i gruppi di membri. Selezionare il gruppo da rimuovere dall'unità amministrativa, quindi selezionare **Rimuovi gruppi**.

![Elencare i gruppi in un'unità amministrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](roles-admin-units-assign-roles.md)
- [Gestire gli utenti in un'unità amministrativa](roles-admin-units-add-manage-users.md)
