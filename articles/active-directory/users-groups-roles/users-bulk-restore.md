---
title: Ripristinare in blocco gli utenti eliminati (anteprima) nel portale di Azure Active Directory | Microsoft Docs
description: Ripristinare in blocco gli utenti eliminati nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
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
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174276"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Ripristino in blocco degli utenti eliminati (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di creazione ed eliminazione di utenti in blocco, invito bulk per Guest e supporto per il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="to-bulk-restore-users"></a>Per eseguire il ripristino bulk degli utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account che sia un amministratore utente nell'organizzazione Azure ad.
1. In Azure AD selezionare **utenti** > **eliminati**.
1. Nella pagina **utenti eliminati** selezionare **ripristino Bulk** per caricare un file CSV valido di proprietà degli utenti da ripristinare.

   ![Selezionare il comando di ripristino Bulk nella pagina utenti eliminati](./media/users-bulk-restore/bulk-restore.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera ripristinare. L'unico valore richiesto è **ObjectID**. Salvare quindi il file.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-restore/upload-button.png)

1. Nella pagina **ripristino bulk (anteprima)** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione di Azure bulk per il ripristino degli utenti.
1. Al termine dell'operazione di ripristino, verrà visualizzata una notifica di esito positivo dell'operazione bulk.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **risultati operazione bulk** . Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-restore/bulk-center.png)

A questo punto, è possibile verificare che gli utenti ripristinati esistano nell'organizzazione del Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visualizzare gli utenti ripristinati nella portale di Azure

1. [Accedere al centro di amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **tutti gli utenti** e verificare che gli utenti ripristinati siano elencati.

### <a name="view-users-with-powershell"></a>Visualizzare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si noterà che gli utenti ripristinati sono elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Importazione bulk di utenti](users-bulk-add.md)
- [Eliminare gli utenti in blocco](users-bulk-delete.md)
- [Scarica l'elenco di utenti](users-bulk-download.md)
