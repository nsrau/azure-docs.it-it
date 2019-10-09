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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174354"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Eliminazione in blocco degli utenti (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile rimuovere un numero elevato di membri a un gruppo usando un file con valori delimitati da virgole (CSV) per eliminare in blocco gli utenti.

## <a name="to-bulk-delete-users"></a>Per eliminare in blocco gli utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure AD selezionare **utenti** > **eliminazione in blocco**.
1. Nella pagina **utente di eliminazione in blocco** selezionare **Scarica** per ricevere un file CSV valido di proprietà utente.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera eliminare](./media/users-bulk-delete/bulk-delete.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera eliminare. L'unico valore obbligatorio è il **nome dell'entità utente**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli ID degli utenti da eliminare](./media/users-bulk-delete/delete-csv-file.png)

1. Nella pagina **eliminazione in blocco utente (anteprima)** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file e si fa clic su Invia, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che elimina gli utenti.
1. Al termine dell'operazione di eliminazione, viene visualizzata una notifica che l'operazione bulk è riuscita.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **risultati operazione bulk** . Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-delete/bulk-center.png)

A questo punto, è possibile verificare che gli utenti eliminati esistano nell'organizzazione Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificare gli utenti eliminati nella portale di Azure

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
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
