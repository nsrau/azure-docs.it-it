---
title: Ripristino in blocco degli utenti eliminati nel portale di Azure Active Directory . Documenti Microsoft
description: Ripristinare gli utenti eliminati in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active DirectoryRestore deleted users in bulk in the Azure AD admin center in Azure Active Directory
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
ms.openlocfilehash: f75fe224491c2853f819a45db678e87849dc72d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532714"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Ripristinare in blocco gli utenti eliminati in Azure Active DirectoryBulk restore deleted users in Azure Active Directory

Azure Active Directory (Azure AD) supporta le operazioni di creazione ed eliminazione di utenti in blocco, l'invito in blocco per gli ospiti e supporta il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="to-bulk-restore-users"></a>Per ripristinare in blocco gli utenti

1. [Accedere all'organizzazione di Azure AD](https://aad.portal.azure.com) con un account amministratore utente nell'organizzazione di Azure AD.
1. In Azure AD selezionare **Utenti** > **eliminati**.
1. Nella pagina **Utenti eliminati** selezionare Ripristino **in blocco** per caricare un file CSV valido delle proprietà degli utenti da ripristinare.

   ![Selezionare il comando ripristino in blocco nella pagina Utenti eliminati](./media/users-bulk-restore/bulk-restore.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera ripristinare. L'unico valore obbligatorio è **ObjectID**. Salvare quindi il file.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-restore/upload-button.png)

1. Nella pagina **Ripristino in blocco,** in **Carica il file CSV,** individuare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che ripristina gli utenti.
1. Al termine dell'operazione di ripristino, verrà visualizzata una notifica che l'operazione in blocco è riuscita.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati operazione in blocco.** Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

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
