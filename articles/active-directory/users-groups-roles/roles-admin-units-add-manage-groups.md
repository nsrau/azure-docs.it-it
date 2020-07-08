---
title: Aggiungere, rimuovere ed elencare gruppi in un'unità amministrativa (anteprima) - Azure Active Directory | Microsoft Docs
description: Gestire i gruppi e le autorizzazioni relative ai ruoli in un'unità amministrativa in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 06/23/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76026313eea8c8fbb2f3e55321e2e4ebbe5dcfc7
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850921"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Aggiornare e gestire gruppi nelle unità amministrative in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile aggiungere gruppi a un'unità amministrativa per un ambito amministrativo di controllo più granulare.

Per i passaggi necessari per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Aggiungere gruppi a un'unità amministrativa

### <a name="azure-portal"></a>Portale di Azure

Nell'anteprima è possibile assegnare gruppi solo singolarmente a un'unità amministrativa. Non è possibile eseguire l'assegnazione bulk dei gruppi a un'unità amministrativa. È possibile assegnare un gruppo a un'unità amministrativa in uno dei due modi nel portale:

1. Dalla pagina **Azure AD > Gruppi**

    Aprire la pagina di panoramica Gruppi in Azure AD e selezionare il gruppo che deve essere assegnato all'unità amministrativa. Sul lato sinistro selezionare **Unità amministrative** per elencare le unità amministrative a cui è assegnato il gruppo. Nella parte superiore viene rilevata l'opzione Assegna all'unità amministrativa. Se si fa clic su di essa, sul lato destro viene visualizzato un pannello per scegliere l'unità amministrativa.

    ![assegnare un gruppo singolarmente a un'unità amministrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Dalla pagina **Azure AD > Unità amministrative > Tutti i gruppi**

    Aprire il pannello Tutti i gruppi in Azure AD > Unità amministrative. Gli eventuali gruppi già stati assegnati all'unità amministrativa verranno visualizzati sul lato destro. Selezionare **Aggiungi** nella parte superiore. Verrà visualizzato un pannello destro con l'elenco dei gruppi disponibili nell'organizzazione di Azure AD. Selezionare uno o più gruppi da assegnare alle unità amministrative.

    ![selezionare un'unità amministrativa e quindi Aggiungi membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

In questo esempio viene usato il cmdlet Add-AzureADAdministrativeUnitMember per aggiungere il gruppo all'unità amministrativa. L'ID oggetto dell'unità amministrativa e l'ID oggetto del gruppo da aggiungere vengono usati come argomenti. La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

Esempio:

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>Elencare i gruppi in un'unità amministrativa

### <a name="azure-portal"></a>Portale di Azure

Andare ad **Azure AD > Unità amministrative** nel portale. Selezionare l'unità amministrativa per cui si vuole elencare gli utenti. Per impostazione predefinita, **Tutti gli utenti** è già selezionato nel pannello sinistro. Selezionare **Tutti i gruppi**. A destra verrà visualizzato l'elenco dei gruppi che sono membri dell'unità amministrativa selezionata.

![Elencare i gruppi in un'unità amministrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Ciò consentirà di ottenere tutti i membri dell'unità amministrativa. Se si vuole visualizzare tutti i gruppi membri dell'unità amministrativa, è possibile usare il frammento di codice seguente:

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Elencare le unità organizzative per un gruppo

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure AD è possibile aprire i dettagli di un gruppo aprendo **Gruppi**. Selezionare un gruppo per aprire il profilo del gruppo. Selezionare **Unità amministrative** per elencare tutte le unità amministrative di cui il gruppo è membro.

![Elencare le unità amministrative per un gruppo](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Rimuovere un gruppo da un'unità amministrativa

### <a name="azure-portal"></a>Portale di Azure

Esistono due modi per rimuovere un gruppo da un'unità amministrativa nel portale di Azure.

Aprire **Azure AD** > **Gruppi**, quindi aprire il profilo per il gruppo che si vuole rimuovere dall'unità amministrativa. Selezionare **Unità amministrative** nel pannello sinistro per elencare tutte le unità amministrative di cui il gruppo è membro. Selezionare l'unità amministrativa da cui si vuole rimuovere il gruppo, quindi selezionare **Rimuovi da unità amministrativa**.

![Rimuovere un gruppo da un'unità amministrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

In alternativa, è possibile andare ad **Azure AD** > **Unità amministrative** e selezionare l'unità amministrativa di cui il gruppo è membro. Selezionare **Gruppi** nel pannello sinistro per elencare i gruppi membri. Selezionare il gruppo da rimuovere dall'unità amministrativa, quindi selezionare **Rimuovi gruppi**.

![Elencare i gruppi in un'unità amministrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](roles-admin-units-assign-roles.md)
- [Gestire gli utenti in un'unità amministrativa](roles-admin-units-add-manage-users.md)
