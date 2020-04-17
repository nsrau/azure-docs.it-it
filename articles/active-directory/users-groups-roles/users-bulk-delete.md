---
title: Eliminazione in blocco di utenti nel portale di Azure Active Directory Documenti Microsoft
description: Eliminare gli utenti in blocco nell'interfaccia di amministrazione di Azure in Azure Active DirectoryDelete users in bulk in the Azure admin center in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532816"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Eliminare in blocco gli utenti in Azure Active DirectoryBulk delete users in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD) è possibile rimuovere un numero elevato di membri in un gruppo usando un file con valori delimitati da virgole (CSV) per eliminare in blocco gli utenti.

## <a name="to-bulk-delete-users"></a>Per eliminare in blocco gli utenti

1. [Accedere all'organizzazione](https://aad.portal.azure.com) di Azure AD con un account amministratore utente nell'organizzazione.
1. In Azure AD selezionare**Eliminazione in blocco** **utenti** > .
1. Nella pagina **Elimina utente in blocco** selezionare **Scarica** per ricevere un file CSV valido delle proprietà utente.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera eliminare](./media/users-bulk-delete/bulk-delete.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera eliminare. L'unico valore obbligatorio è **Nome applicazione utente**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli URL degli utenti da eliminare](./media/users-bulk-delete/delete-csv-file.png)

1. Nella pagina **Elimina utente in blocco,** in **Carica il file CSV,** individuare il file. Quando si seleziona il file e si fa clic su Invia, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che elimina gli utenti.
1. Al termine dell'operazione di eliminazione, verrà visualizzata una notifica che l'operazione in blocco è riuscita.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati operazione in blocco.** Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Successivamente, è possibile verificare che gli utenti eliminati esistano nell'organizzazione di Azure AD nel portale di Azure o tramite PowerShell.Next, you can check to see that the users you deleted exist in the Azure AD organization either in the Azure portal or by using PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificare gli utenti eliminati nel portale di AzureVerify deleted users in the Azure portal

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare Solo **utenti** e verificare che gli utenti eliminati non siano più elencati.

### <a name="verify-deleted-users-with-powershell"></a>Verificare gli utenti eliminati con PowerShellVerify deleted users with PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verificare che gli utenti eliminati non siano più elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Scarica l'elenco degli utenti](users-bulk-download.md)
- [Utenti di ripristino in blocco](users-bulk-restore.md)
