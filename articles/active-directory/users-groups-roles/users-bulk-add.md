---
title: Creare utenti in blocco (anteprima) nel portale di Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174326"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Creazione bulk di utenti (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di creazione ed eliminazione di utenti in blocco, invito bulk per Guest e supporto per il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per creare in blocco gli utenti nel portale di amministrazione, è necessario effettuare l'accesso come amministratore globale o Amministratore utenti.

## <a name="to-create-users-in-bulk"></a>Per creare utenti in blocco

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure AD selezionare **utenti** > **creazione bulk**.
1. Nella pagina **creazione bulk utente** selezionare **download** per ricevere un file con valori delimitati da virgole (CSV) di proprietà utente e quindi aggiungere Aggiungi utenti che si desidera creare.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-add/upload-button.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si vuole creare. Gli unici valori obbligatori sono **nome**, **nome dell'entità utente**, **password iniziale** e **accesso a blocchi (sì/no)** . Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli ID degli utenti da creare](./media/users-bulk-add/add-csv-file.png)

1. Nella pagina **creazione in blocco dell'utente (anteprima)** , in caricare il file CSV, selezionare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Una volta convalidato il contenuto del file, il **file verrà caricato correttamente**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che importa i nuovi utenti.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica dello stato del processo dell'operazione bulk.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **risultati operazione bulk** . Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-add/bulk-center.png)

A questo punto, è possibile verificare che gli utenti creati siano presenti nell'organizzazione Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verificare gli utenti nella portale di Azure

1. [Accedere al centro di amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **tutti gli utenti** e verificare che gli utenti creati siano elencati.

### <a name="verify-users-with-powershell"></a>Verificare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si noterà che gli utenti creati sono elencati.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione bulk

Ogni attività bulk per la creazione di utenti può essere eseguita per un massimo di un'ora. Questo consente la creazione bulk di almeno 50.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Eliminare gli utenti in blocco](users-bulk-delete.md)
- [Scarica l'elenco di utenti](users-bulk-download.md)
- [Ripristino in blocco degli utenti](users-bulk-restore.md)
