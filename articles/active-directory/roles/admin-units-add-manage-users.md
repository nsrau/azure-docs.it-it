---
title: Aggiungere, rimuovere ed elencare gli utenti in un'unità amministrativa-Azure Active Directory | Microsoft Docs
description: Gestire gli utenti e le autorizzazioni relative ai ruoli in un'unità amministrativa in Azure Active Directory
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
ms.openlocfilehash: b490e03ea7ac0a3bc780fa731629217126b6828a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395530"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Aggiungere e gestire gli utenti in un'unità amministrativa in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere utenti a un'unità amministrativa per un ambito amministrativo di controllo più granulare.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Aggiungere utenti a un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile assegnare gli utenti a unità amministrative singolarmente o come operazione bulk.

- Assegnare singoli utenti da un profilo utente:

   1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi.

   1. Selezionare **utenti** e quindi, per aprire il profilo dell'utente, selezionare l'utente da assegnare a un'unità amministrativa.
   
   1. Selezionare **unità amministrative**. 
   
   1. Per assegnare l'utente a una o più unità amministrative, selezionare **assegna a unità amministrativa** , quindi nel riquadro destro selezionare le unità amministrative a cui si desidera assegnare l'utente.

       ![Screenshot del riquadro "unità amministrative" per l'assegnazione di un utente a un'unità amministrativa.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Assegnare singoli utenti da un'unità amministrativa:

   1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi.
   1. Selezionare **unità amministrative** , quindi selezionare l'unità amministrativa a cui deve essere assegnato l'utente.
   1. Selezionare **tutti gli utenti** , selezionare **Aggiungi membro** , quindi nel riquadro **Aggiungi membro** selezionare uno o più utenti che si desidera assegnare all'unità amministrativa.

        ![Screenshot del riquadro "utenti" dell'unità amministrativa per l'assegnazione di un utente a un'unità amministrativa.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Assegnare gli utenti come operazione bulk:

   1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi.

   1. Selezionare **unità amministrative**.

   1. Selezionare l'unità amministrativa a cui si desidera aggiungere gli utenti.

   1. Selezionare **gli utenti**  >  **bulk attività**  >  **Aggiungi membri**. È quindi possibile scaricare il modello con valori delimitati da virgole (CSV) e modificare il file. Il formato è semplice e richiede l'aggiunta di un singolo nome dell'entità utente in ogni riga. Quando il file è pronto, salvarlo in una posizione appropriata e quindi caricarlo come parte di questo passaggio.

      ![Screenshot del riquadro "utenti" per l'assegnazione di utenti a un'unità amministrativa come operazione bulk.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Usare PowerShell

In PowerShell usare il `Add-AzureADAdministrativeUnitMember` cmdlet nell'esempio seguente per aggiungere l'utente all'unità amministrativa. L'ID oggetto dell'unità amministrativa a cui si desidera aggiungere l'utente e l'ID oggetto dell'utente che si desidera aggiungere vengono considerati argomenti. Modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Sostituire il segnaposto con le informazioni di test ed eseguire il comando seguente:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Esempio:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Visualizzare un elenco di unità amministrative per un utente

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Nella portale di Azure è possibile aprire il profilo di un utente eseguendo le operazioni seguenti:

1. Passare a **Azure ad** , quindi selezionare **utenti**.

1. Selezionare l'utente di cui si desidera visualizzare il profilo.

1. Selezionare **unità amministrative** per visualizzare l'elenco di unità amministrative a cui è stato assegnato l'utente.

   ![Screenshot delle unità amministrative a cui è stato assegnato un utente.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> Per impostazione predefinita, `Get-AzureADAdministrativeUnitMember` restituisce solo i membri di 100 di un'unità amministrativa. Per recuperare più membri, è possibile aggiungere `"-All $true"` .

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Sostituire il segnaposto con le informazioni di test ed eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Rimuovere un singolo utente da un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile rimuovere un utente da un'unità amministrativa in uno dei due modi seguenti: 

* Nella portale di Azure passare a **Azure ad** , quindi selezionare **utenti**. 
  1. Selezionare l'utente per aprire il profilo dell'utente. 
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere l'utente e quindi selezionare **Rimuovi da unità amministrativa**.

     ![Screenshot che illustra come rimuovere un utente da un'unità amministrativa dal riquadro del profilo dell'utente.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Nella portale di Azure passare a **Azure ad** , quindi selezionare **unità amministrative**.
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere l'utente. 
  1. Selezionare l'utente e quindi selezionare **Rimuovi membro**.
  
     ![Screenshot che illustra come rimuovere un utente a livello di unità amministrativa.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

Sostituire i segnaposto con le informazioni di test ed eseguire il comando seguente:

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Rimuovere più utenti come operazione bulk

Per rimuovere più utenti da un'unità amministrativa, procedere come segue:

1. Nella portale di Azure passare a **Azure ad**.

1. Selezionare **unità amministrative** , quindi selezionare l'unità amministrativa da cui si vogliono rimuovere gli utenti. 

1. Selezionare **bulk Remove members** , quindi scaricare il modello CSV da usare per elencare gli utenti da rimuovere.

   ![Screenshot che illustra il collegamento "Rimuovi in blocco membri" nel riquadro "utenti".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Modificare il modello CSV scaricato con le voci utente pertinenti. Non rimuovere le prime due righe del modello. Aggiungere un nome dell'entità utente (UPN) in ogni riga.

   ![Screenshot di un file CSV modificato per la rimozione bulk degli utenti da un'unità amministrativa.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Salvare le modifiche, caricare il file e quindi selezionare **Submit (Invia** ).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](admin-units-assign-roles.md)
- [Aggiungere gruppi a un'unità amministrativa](admin-units-add-manage-groups.md)
