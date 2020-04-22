---
title: Aggiunta, rimozione ed elenco di utenti in un'unità amministrativa (anteprima) - Azure Active Directory Documenti Microsoft
description: Gestire gli utenti e le relative autorizzazioni di ruolo in un'unità amministrativa in Azure Active DirectoryManage users and their role permissions in an administrative unit in Azure Active Directory
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684964"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Aggiungere e gestire utenti in un'unità amministrativa in Azure Active DirectoryAdd and manage users in an administrative unit in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile aggiungere utenti a un'unità amministrativa (AU) per un ambito amministrativo di controllo più granulare.

Per la procedura di preparazione all'utilizzo di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione.](roles-admin-units-manage.md#get-started)

## <a name="add-users-to-an-au"></a>Aggiungere utenti a un'Unione africana

### <a name="azure-portal"></a>Portale di Azure

È possibile assegnare gli utenti alle unità amministrative in due modi.

1. Assegnazione individuale

    1. È possibile passare ad Azure AD nel portale e selezionare Utenti e selezionare l'utente da assegnare a un'unità amministrativa. È quindi possibile selezionare Unità amministrative nel pannello di sinistra. L'utente può essere assegnato a una o più unità amministrative facendo clic su Assegna a unità amministrative e selezionando le unità amministrative in cui l'utente deve essere assegnato.

       ![Selezionare Aggiungi, quindi immettere un nome per l'unità amministrativa](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. È possibile passare ad Azure AD nel portale e selezionare Unità amministrative nel riquadro sinistro e quindi selezionare l'unità amministrativa in cui devono essere assegnati gli utenti. Selezionare Tutti gli utenti nel riquadro sinistro e quindi selezionare Aggiungi membro. È quindi possibile andare avanti e selezionare uno o più utenti da assegnare all'unità amministrativa dal riquadro di destra.

        ![selezionare un'unità amministrativa, quindi selezionare Aggiungi membro](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Assegnazione in blocco

    Passare ad Azure AD nel portale e selezionare Unità amministrative. Selezionare l'unità amministrativa in cui devono essere aggiunti gli utenti. Procedere facendo clic su Tutti gli utenti > Aggiungi membri dal file CSV. È quindi possibile scaricare il modello CSV e modificare il file. Il formato è semplice e richiede un singolo UPN da aggiungere in ogni riga. Quando il file è pronto, salvarlo in una posizione appropriata e quindi caricarlo nel passaggio 3 come evidenziato nello snapshot.

    ![assegnare in blocco gli utenti a un'unità amministrativa](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

Nell'esempio precedente, il cmdlet Add-AzureADAdministrativeUnitMember viene usato per aggiungere l'utente all'unità amministrativa. L'ID oggetto dell'unità amministrativa in cui deve essere aggiunto l'utente e l'ID oggetto dell'utente da aggiungere vengono considerati come argomenti. La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Esempio:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Elencare le unità amministrative per un utenteList administrative units for a user

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure è possibile aprire il profilo di un utente passando a Utenti di Azure AD >. Fare clic sull'utente per aprire il profilo dell'utente.

![Aprire il profilo di un utente in Azure Active DirectoryOpen a user's profile in Azure Active Directory](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Selezionare **Unità amministrative** nel riquadro sinistro per visualizzare l'elenco delle unità amministrative a cui è stato assegnato l'utente.

![Elencare le unità amministrative per un utente](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Rimuovere un singolo utente da un'UNIONE utenteRemove a single user from an AU

### <a name="azure-portal"></a>Portale di Azure

Esistono due modi per rimuovere un utente da un'unità amministrativa. Nel portale di Azure è possibile aprire il profilo di un utente passando a**Utenti** **di Azure AD.** >  Selezionare l'utente per aprire il profilo dell'utente. Selezionare l'unità amministrativa da cui si desidera rimuovere l'utente e selezionare **Rimuovi dall'unità amministrativa**.

![Rimuovere un utente da un'unità amministrativa dal profilo utente](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

È anche possibile rimuovere un utente nelle**unità di amministrazione** di Azure **AD** > selezionando l'unità amministrativa da cui si vuole rimuovere gli utenti. Selezionare l'utente e selezionare **Rimuovi membro**.
  
![Rimuovere un utente a livello di unità amministrativa](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Rimuovere in blocco più utenti

È possibile passare ad Azure AD > unità amministrative e selezionare l'unità amministrativa da cui si desidera rimuovere gli utenti. Fare clic su Rimuovi membro in blocco. Scaricare il modello CSV per fornire l'elenco degli utenti da rimuovere.

Modificare il modello CSV scaricato con le voci utente pertinenti. Non rimuovere le prime due righe del modello. Aggiungere un UPN utente in ogni riga.

![Modificare il file CSV per la rimozione in blocco degli utenti dalle unità amministrative](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Dopo aver salvato le voci nel file, caricare il file, selezionare **Invia**.

![Inviare il file di caricamento in blocco](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](roles-admin-units-assign-roles.md)
- [Aggiungere gruppi a un'unità amministrativa](roles-admin-units-add-manage-groups.md)
