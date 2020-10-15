---
title: Aggiungere e rimuovere unità amministrative-Azure Active Directory | Microsoft Docs
description: Usare unità amministrative per limitare l'ambito delle autorizzazioni dei ruoli in Azure Active Directory.
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
ms.openlocfilehash: 1166d2ad17aea97a4dd7fdda53c42d6b3df75936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450364"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gestire le unità amministrative in Azure Active Directory

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative (AUs).

## <a name="get-started"></a>Operazioni preliminari

1. Per eseguire query dalle istruzioni seguenti tramite [Graph Explorer](https://aka.ms/ge), seguire questa procedura:

    a. Nel portale di Azure passare ad Azure AD. Nell'elenco delle applicazioni selezionare **Graph Explorer**, quindi selezionare **Grant admin consenso to Graph Explorer**.

    ![Screenshot che mostra il collegamento a "Concedi il consenso dell'amministratore"](./media/roles-admin-units-manage/select-graph-explorer.png)


1. Usare la versione di anteprima di Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Aggiungere un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nella portale di Azure passare a Azure AD, quindi nel riquadro sinistro selezionare **unità amministrative**.

    ![Screenshot del unitslink amministrativo in Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selezionare **Aggiungi** e quindi immettere il nome dell'unità amministrativa. Facoltativamente, aggiungere una descrizione dell'unità amministrativa.

    ![Screenshot del pulsante Aggiungi e della casella di testo per immettere il nome dell'unità amministrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selezionare **Aggiungi** per finalizzare l'unità amministrativa.

### <a name="use-powershell"></a>Usare PowerShell

Installare Azure AD PowerShell (anteprima) prima di provare a eseguire i comandi seguenti:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

È possibile modificare i valori racchiusi tra virgolette, come richiesto.

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Rimuovere un'unità amministrativa

In Azure AD, è possibile rimuovere un'unità amministrativa che non è più necessaria come unità di ambito per i ruoli amministrativi.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nella portale di Azure passare a **Azure ad**  >  **unità amministrative**. 
1. Selezionare l'unità amministrativa da eliminare, quindi selezionare **Elimina**. 
1. Per confermare che si vuole eliminare l'unità amministrativa, selezionare **Sì**. L'unità amministrativa viene eliminata.

![Screenshot del pulsante di eliminazione dell'unità amministrativa e della finestra di conferma](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

È possibile modificare i valori racchiusi tra virgolette, come richiesto per l'ambiente specifico.

### <a name="use-the-graph-api"></a>Usare il API Graph

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli utenti in un'unità amministrativa](roles-admin-units-add-manage-users.md)
* [Gestire i gruppi in un'unità amministrativa](roles-admin-units-add-manage-groups.md)
