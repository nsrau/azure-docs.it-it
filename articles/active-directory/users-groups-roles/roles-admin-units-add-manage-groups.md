---
title: Aggiunta, rimozione ed elenco di gruppi in un'unità amministrativa (anteprima) - Azure Active Directory Documenti Microsoft
description: Gestire i gruppi e le relative autorizzazioni di ruolo in un'unità amministrativa in Azure Active DirectoryManage groups and their role permissions in an administrative unit in Azure Active Directory
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683263"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Aggiungere e gestire gruppi in unità amministrative in Azure Active DirectoryAdd and manage groups in administrative units in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile aggiungere gruppi a un'unità amministrativa (AU) per un ambito amministrativo di controllo più granulare.

Per la procedura di preparazione all'utilizzo di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione.](roles-admin-units-manage.md#get-started)

## <a name="add-groups-to-an-au"></a>Aggiungere gruppi a un'Unione africana

### <a name="azure-portal"></a>Portale di Azure

Nell'anteprima, è possibile assegnare i gruppi solo singolarmente a un'unità amministrativa. Non è possibile l'assegnazione in blocco di gruppi a un'unità amministrativa. È possibile assegnare un gruppo a un'unità amministrativa in uno dei due modi seguenti nel portale:

1. Dalla pagina **Gruppi di > di Azure AD**

    Aprire la pagina Panoramica gruppi in Azure AD e selezionare il gruppo da assegnare all'unità amministrativa. Sul lato sinistro selezionare **Unità amministrative** per elencare le unità amministrative a cui è assegnato il gruppo. In alto troverete l'opzione Assegna all'unità amministrativa e cliccando su di esso darà un pannello sul lato destro per scegliere l'unità amministrativa.

    ![assegnare un gruppo individualmente a un'unità amministrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Dalla pagina **Unità amministrative di Azure AD > > tutti i gruppi**

    Aprire il pannello Tutti i gruppi in Azure AD > unità amministrative. Se sono già presenti gruppi assegnati all'unità amministrativa, questi verranno visualizzati sul lato destro. Selezionare **Aggiungi** in alto e un riquadro a destra scorrerà nell'elenco dei gruppi disponibili nell'organizzazione di Azure AD. Selezionare uno o più gruppi da assegnare alle unità amministrative.

    ![selezionare un'unità amministrativa, quindi selezionare Aggiungi membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

In questo esempio, il cmdlet Add-AzureADAdministrativeUnitMember viene usato per aggiungere il gruppo all'unità amministrativa. L'ID oggetto dell'unità amministrativa e l'ID oggetto del gruppo da aggiungere vengono presi come argomento. La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

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

## <a name="list-groups-in-an-au"></a>Elencare i gruppi in un'Unione africanaList groups in an AU

### <a name="azure-portal"></a>Portale di Azure

Passare ad **Azure AD > unità amministrative** nel portale. Selezionare l'unità amministrativa per la quale si desidera elencare gli utenti. Per impostazione predefinita, **l'opzione Tutti gli utenti** è già selezionata nel riquadro sinistro. Selezionare **Tutti i gruppi** e a destra si trova l'elenco dei gruppi membri dell'unità amministrativa selezionata.

![Selezionare un'unità amministrativa da eliminare](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Questo vi aiuterà a ottenere tutti i membri dell'unità amministrativa. Se si desidera visualizzare tutti i gruppi membri dell'unità amministrativa, è possibile utilizzare il frammento di codice seguente:

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

## <a name="list-aus-for-a-group"></a>Elenca punti neva per un gruppo

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure AD è possibile aprire i dettagli di un gruppo aprendo **Gruppi**. Selezionare un gruppo per aprire il profilo del gruppo. Selezionare **Unità amministrative** per elencare tutte le unità amministrative di cui il gruppo è membro.

![Elencare le unità amministrative per un gruppoList administrative units for a group](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Rimuovere un gruppo da un'Unione africana

### <a name="azure-portal"></a>Portale di Azure

Esistono due modi per rimuovere un gruppo da un'unità amministrativa nel portale di Azure.There are two ways you can remove a group from an administrative unit in the Azure portal.

Aprire**Gruppi** di **Azure AD** > e aprire il profilo per il gruppo che si vuole rimuovere dall'unità amministrativa. Selezionare **Unità amministrative** nel riquadro sinistro per elencare tutte le unità amministrative di cui il gruppo è membro. Selezionare l'unità amministrativa da cui si desidera rimuovere il gruppo e quindi selezionare **Rimuovi dall'unità amministrativa**.

![Rimuovere un gruppo da un'unità amministrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

In alternativa, è possibile passare alle**unità amministrative** di **Azure AD** > e selezionare l'unità amministrativa di cui il gruppo è membro. Selezionare **Gruppi** nel riquadro sinistro per elencare i gruppi di membri. Selezionare il gruppo da rimuovere dall'unità amministrativa, quindi selezionare **Rimuovi gruppi**.

![Elencare i gruppi in un'unità amministrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](roles-admin-units-assign-roles.md)
- [Gestire gli utenti in un'unità amministrativa](roles-admin-units-add-manage-users.md)
