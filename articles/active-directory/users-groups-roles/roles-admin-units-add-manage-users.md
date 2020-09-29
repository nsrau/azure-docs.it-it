---
title: Aggiungere, rimuovere ed elencare gli utenti in un'unità amministrativa-Azure Active Directory | Microsoft Docs
description: Gestire gli utenti e le autorizzazioni relative ai ruoli in un'unità amministrativa in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e518a66ae2eedd952f521e0a67769b6e08de87
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450439"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Aggiungere e gestire gli utenti in un'unità amministrativa in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere utenti a un'unità amministrativa (AU) per un ambito amministrativo di controllo più granulare.

Per i passaggi necessari per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Aggiungere utenti a un AU

### <a name="azure-portal"></a>Portale di Azure

È possibile assegnare utenti a unità amministrative in due modi.

1. Assegnazione singola

    1. È possibile passare all'Azure AD nel portale e selezionare utenti e selezionare l'utente da assegnare a un'unità amministrativa. È quindi possibile selezionare unità amministrative nel pannello di sinistra. L'utente può essere assegnato a una o più unità amministrative facendo clic su * * assegna a unità amministrativa e selezionando le unità amministrative in cui l'utente deve essere assegnato.

       ![Selezionare Aggiungi e quindi immettere un nome per l'unità amministrativa](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. È possibile passare a Azure AD nel portale e selezionare unità amministrative nel riquadro sinistro, quindi selezionare l'unità amministrativa a cui assegnare gli utenti. Selezionare **tutti gli utenti** e quindi fare clic su **Aggiungi membro**. È quindi possibile procedere e selezionare uno o più utenti da assegnare all'unità amministrativa dal riquadro di destra.

        ![selezionare un'unità amministrativa e quindi Aggiungi membro](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Assegnazione in blocco

    Passare a Azure AD nel portale e selezionare unità amministrative. Selezionare l'unità amministrativa in cui si desidera aggiungere gli utenti. Per continuare, fare clic su tutti gli utenti > Aggiungi membri da un file con estensione CSV. È quindi possibile scaricare il modello CSV e modificare il file. Il formato è semplice e richiede l'aggiunta di un singolo UPN in ogni riga. Quando il file è pronto, salvarlo in una posizione appropriata e quindi caricarlo nel passaggio 3 come evidenziato nello snapshot.

    ![assegnare in blocco gli utenti a un'unità amministrativa](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

Nell'esempio precedente, il cmdlet Add-AzureADAdministrativeUnitMember viene usato per aggiungere l'utente all'unità amministrativa. ID oggetto dell'unità amministrativa in cui è necessario aggiungere l'utente e l'ID oggetto dell'utente da aggiungere viene considerato argomento. La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

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

## <a name="list-administrative-units-for-a-user"></a>Elencare le unità amministrative per un utente

### <a name="azure-portal"></a>Portale di Azure

Nella portale di Azure è possibile aprire il profilo di un utente per:

1. Apertura **Azure ad**  >  **utenti**.

1. Selezionare l'utente per aprire il profilo dell'utente.

1. Selezionare **unità amministrative** per visualizzare l'elenco di unità amministrative a cui è stato assegnato l'utente.

   ![Elencare le unità amministrative per un utente](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Rimuovere un utente singolo da un'AU

### <a name="azure-portal"></a>Portale di Azure

È possibile rimuovere un utente da un'unità amministrativa in due modi. Nella portale di Azure è possibile aprire il profilo di un utente accedendo a **Azure ad**  >  **utenti**. Selezionare l'utente per aprire il profilo dell'utente. Selezionare l'unità amministrativa da cui si vuole rimuovere l'utente e selezionare **Rimuovi da unità amministrativa**.

![Rimuovere un utente da un'unità amministrativa dal profilo utente](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

È anche possibile rimuovere un utente in **Azure ad**  >  **unità amministrative** selezionando l'unità amministrativa da cui si vogliono rimuovere gli utenti. Selezionare l'utente e selezionare **Rimuovi membro**.
  
![Rimuovere un utente a livello di unità amministrativa](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Rimuovere in blocco più di un utente

È possibile passare a Azure AD > unità amministrative e selezionare l'unità amministrativa da cui si vogliono rimuovere gli utenti. Fare clic su bulk Remove member. Scaricare il modello CSV per fornire l'elenco di utenti da rimuovere.

Modificare il modello CSV scaricato con le voci utente pertinenti. Non rimuovere le prime due righe del modello. Aggiungere un UPN utente in ogni riga.

![Modificare il file CSV per la rimozione di utenti in blocco da unità amministrative](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Dopo aver salvato le voci nel file, caricare il file e selezionare **Invia**.

![Inviare il file di caricamento bulk](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](roles-admin-units-assign-roles.md)
- [Aggiungere gruppi a un'unità amministrativa](roles-admin-units-add-manage-groups.md)
