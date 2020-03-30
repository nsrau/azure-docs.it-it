---
title: Ripristinare in blocco gli utenti eliminati (anteprima) nel portale di Azure Active Directory Documenti Microsoft
description: Ripristinare gli utenti eliminati in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active DirectoryRestore deleted users in bulk in the Azure AD admin center in Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174276"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Ripristinare in blocco gli utenti eliminati (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) supporta le operazioni di creazione ed eliminazione di utenti in blocco, l'invito in blocco per gli ospiti e supporta il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="to-bulk-restore-users"></a>Per ripristinare in blocco gli utenti

1. [Accedere all'organizzazione di Azure AD](https://aad.portal.azure.com) con un account amministratore utente nell'organizzazione di Azure AD.
1. In Azure AD selezionare **Utenti** > **eliminati**.
1. Nella pagina **Utenti eliminati** selezionare Ripristino **in blocco** per caricare un file CSV valido delle proprietà degli utenti da ripristinare.

   ![Selezionare il comando ripristino in blocco nella pagina Utenti eliminati](./media/users-bulk-restore/bulk-restore.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera ripristinare. L'unico valore obbligatorio è **ObjectID**. Salvare quindi il file.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-restore/upload-button.png)

1. Nella pagina **Ripristino in blocco (anteprima),** in **Carica il file CSV,** individuare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che ripristina gli utenti.
1. Al termine dell'operazione di ripristino, verrà visualizzata una notifica che l'operazione in blocco è riuscita.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati operazione in blocco.** Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione in blocco (anteprima).**

   ![Controllare lo stato del caricamento nella pagina Risultati operazioni in blocco](./media/users-bulk-restore/bulk-center.png)

Successivamente, è possibile verificare che gli utenti ripristinati esistano nell'organizzazione di Azure AD nel portale di Azure o tramite PowerShell.Next, you can check to see that the users you restored exist in the Azure AD organization either in the Azure portal or by using PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visualizzare gli utenti ripristinati nel portale di AzureView restored users in the Azure portal

1. [Accedere all'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con un account amministratore utente nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **Tutti gli utenti** e verificare che gli utenti ripristinati siano elencati.

### <a name="view-users-with-powershell"></a>Visualizzare gli utenti con PowerShellView users with PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si dovrebbe vedere che gli utenti che hai ripristinato sono elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Importazione in blocco degli utenti](users-bulk-add.md)
- [Eliminare in blocco gli utenti](users-bulk-delete.md)
- [Scarica l'elenco degli utenti](users-bulk-download.md)
