---
title: Aggiunta, rimozione ed elenco di gruppi in un'unità amministrativa-Azure Active Directory | Microsoft Docs
description: Gestire i gruppi e le autorizzazioni relative ai ruoli in un'unità amministrativa in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394748"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Aggiunta e gestione di gruppi in un'unità amministrativa in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere gruppi a un'unità amministrativa per un ambito amministrativo di controllo più granulare.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Aggiungere gruppi a un'unità amministrativa

È possibile aggiungere gruppi a un'unità amministrativa usando il portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile assegnare solo singoli gruppi a un'unità amministrativa. Non è possibile assegnare gruppi come operazione bulk. Nella portale di Azure è possibile assegnare un gruppo a un'unità amministrativa in uno dei due modi seguenti:

* Dal riquadro **gruppi** :

  1. Nella portale di Azure passare a **Azure ad**.
  1. Selezionare **gruppi** e quindi selezionare il gruppo che si desidera assegnare all'unità amministrativa. 
  1. Nel riquadro sinistro selezionare **unità amministrative** per visualizzare un elenco delle unità amministrative a cui è assegnato il gruppo. 

     ![Screenshot del collegamento "assegna a unità amministrativa" nel riquadro "unità amministrative".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selezionare **assegna a unità amministrativa**.
  1. Nel riquadro destro selezionare l'unità amministrativa.

* Dal riquadro **unità amministrative**  >  **tutti i gruppi** :

  1. Nella portale di Azure passare a **Azure ad**.
  
  1. Nel riquadro sinistro selezionare **unità amministrative** e quindi selezionare **tutti i gruppi**. 
     Tutti i gruppi già assegnati all'unità amministrativa vengono visualizzati nel riquadro di destra. 

  1. Nel riquadro **gruppi** selezionare **Aggiungi**.
    Il riquadro destro elenca tutti i gruppi disponibili nell'organizzazione Azure AD. 

     ![Screenshot del pulsante "Aggiungi" per aggiungere un gruppo a un'unità amministrativa.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selezionare uno o più gruppi da assegnare all'unità amministrativa, quindi selezionare il pulsante **Seleziona** .

### <a name="use-powershell"></a>Usare PowerShell

Nell'esempio seguente usare il `Add-AzureADMSAdministrativeUnitMember` cmdlet per aggiungere il gruppo all'unità amministrativa. L'ID oggetto dell'unità amministrativa e l'ID oggetto del gruppo da aggiungere vengono considerati argomenti. Modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Eseguire i comandi seguenti:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

Esempio:

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Visualizzare un elenco di gruppi in un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nella portale di Azure passare a **Azure ad**.

1. Nel riquadro sinistro selezionare **unità amministrative** , quindi selezionare l'unità amministrativa di cui si desidera visualizzare i gruppi. Per impostazione predefinita, **tutti gli utenti** sono selezionati nel riquadro sinistro. 

1. Nel riquadro sinistro selezionare **gruppi**. Il riquadro di destra visualizza un elenco dei gruppi che sono membri dell'unità amministrativa selezionata.

   ![Screenshot del riquadro "gruppi" in cui è visualizzato un elenco di gruppi in un'unità amministrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Per visualizzare un elenco di tutti i membri dell'unità amministrativa, eseguire il comando seguente: 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Per visualizzare tutti i gruppi membri dell'unità amministrativa, usare il frammento di codice seguente:

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Eseguire il comando seguente:

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Visualizzare un elenco di unità amministrative per un gruppo

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nella portale di Azure passare a **Azure ad**.

1. Nel riquadro sinistro selezionare **gruppi** per visualizzare un elenco di gruppi.

1. Selezionare un gruppo per aprire il profilo del gruppo. 

1. Nel riquadro sinistro selezionare **unità amministrative** per elencare tutte le unità amministrative in cui il gruppo è membro.

   ![Screenshot del riquadro "unità amministrative", che visualizza un elenco di unità amministrative a cui è assegnato un gruppo.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Rimuovere un gruppo da un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile rimuovere un gruppo da un'unità amministrativa nel portale di Azure in uno dei due modi seguenti:

- Rimuoverlo da una panoramica del gruppo:

  1. Nella portale di Azure passare a **Azure ad**.
  1. Nel riquadro sinistro selezionare **gruppi** e quindi aprire il profilo per il gruppo che si desidera rimuovere da un'unità amministrativa.
  1. Nel riquadro sinistro selezionare **unità amministrative** per elencare tutte le unità amministrative a cui è assegnato il gruppo. 
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere il gruppo, quindi selezionare **Rimuovi da unità amministrativa**.

     ![Screenshot del riquadro "unità amministrative", che visualizza un elenco di gruppi assegnati all'unità amministrativa selezionata.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Rimuoverlo da un'unità amministrativa:

  1. Nella portale di Azure passare a **Azure ad**.
  1. Nel riquadro sinistro selezionare **unità amministrative** e quindi selezionare l'unità amministrativa a cui è assegnato il gruppo.
  1. Nel riquadro sinistro selezionare **gruppi** per elencare tutti i gruppi assegnati all'unità amministrativa.
  1. Selezionare il gruppo che si desidera rimuovere, quindi selezionare **Rimuovi gruppi**.

    ![Screenshot del riquadro "gruppi", che visualizza un elenco di gruppi in un'unità amministrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](admin-units-assign-roles.md)
- [Gestire gli utenti in un'unità amministrativa](admin-units-add-manage-users.md)
