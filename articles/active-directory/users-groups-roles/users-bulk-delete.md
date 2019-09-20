---
title: Eliminare in blocco gli utenti (anteprima) nel portale di Azure Active Directory | Microsoft Docs
description: Eliminare gli utenti in blocco nell'interfaccia di amministrazione di Azure in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146423"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Eliminazione in blocco degli utenti (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile rimuovere un numero elevato di membri a un gruppo usando un file con valori delimitati da virgole (CSV) per eliminare in blocco gli utenti.

## <a name="to-bulk-delete-users"></a>Per eliminare in blocco gli utenti

1. Accedere all'organizzazione Azure AD con un account amministratore dell'organizzazione.
1. In Azure ad selezionare **Users** > **Bulk Delete**.
1. Nella pagina **utente di eliminazione in blocco** selezionare **Scarica** per ricevere un file CSV valido di proprietà utente e quindi aggiungere gli utenti che si desidera eliminare.

   ![Il file CSV contiene i nomi e gli ID degli utenti da eliminare](./media/users-bulk-delete/delete-csv-file.png)

1. Al termine della modifica del file CSV, selezionare il file in **caricare il file CSV** da convalidare.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera eliminare](./media/users-bulk-delete/bulk-delete.png)

1. Quando il contenuto del file viene convalidato, è necessario correggere gli eventuali errori prima dell'invio del processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare il processo di Azure batch che elimina gli utenti. Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina risultati operazione bulk. Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-delete/bulk-center.png)

A questo punto, è possibile verificare che gli utenti eliminati esistano nell'organizzazione Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificare gli utenti eliminati nella portale di Azure

1. Accedere al portale di Azure con un account che sia un amministratore utente nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare solo **tutti gli utenti** e verificare che gli utenti eliminati non siano più elencati.

### <a name="verify-deleted-users-with-powershell"></a>Verificare gli utenti eliminati con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verificare che gli utenti eliminati non siano più elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Scarica l'elenco di utenti](users-bulk-download.md)
- [Ripristino in blocco degli utenti](users-bulk-restore.md)
