---
title: Caricamento dell'importazione bulk per aggiungere membri a un gruppo-Azure Active Directory | Microsoft Docs
description: Aggiungere i membri del gruppo in blocco nell'interfaccia di amministrazione di Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146277"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Importazione bulk dei membri del gruppo (anteprima) in Azure Active Directory

Utilizzando il portale Azure Active Directory (Azure AD), è possibile aggiungere un numero elevato di membri a un gruppo utilizzando un file con valori delimitati da virgole (CSV) per l'importazione bulk dei membri del gruppo.

> [!NOTE]
> Azure AD operazioni bulk sono una funzionalità di anteprima pubblica di Azure AD e sono disponibili con qualsiasi piano di licenza Azure AD a pagamento. Per ulteriori informazioni sui termini di utilizzo dell'anteprima, vedere [condizioni supplementari per l'utilizzo di Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-bulk-import-group-members"></a>Per eseguire l'importazione bulk dei membri del gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione. I proprietari del gruppo possono anche importare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure ad selezionare **gruppi** > **tutti i gruppi**.
1. Aprire il gruppo a cui vengono aggiunti i membri e quindi selezionare **membri**.
1. Nella pagina **membri** selezionare **Importa membri**.
1. Nella pagina **membri del gruppo di importazione bulk (anteprima)** selezionare **download** per ottenere il modello di file CSV con le proprietà obbligatorie del membro del gruppo.

    ![Il comando importa membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-import-members/import-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo che si desidera importare nel gruppo. i valori obbligatori sono **ID oggetto membro** o **nome entità utente**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli ID dei membri da importare](./media/groups-bulk-import-members/csv-file.png)

1. Nella pagina **membri del gruppo di importazione bulk (anteprima)** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Quando il contenuto del file viene convalidato, la pagina di importazione bulk Visualizza il **file caricato correttamente**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che importa i membri del gruppo nel gruppo.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica relativa all'esito positivo dell'operazione bulk.

## <a name="check-import-status"></a>Controlla stato importazione

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![La pagina Risultati operazioni bulk Mostra lo stato della richiesta bulk](./media/groups-bulk-import-members/bulk-center.png)

Per informazioni dettagliate su ogni elemento della riga nell'operazione bulk, selezionare i valori nelle colonne **# Success**, **# Failure**o **Total requests** . Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione bulk

Ogni attività bulk per l'importazione di un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Questo consente l'importazione di un elenco di almeno 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Rimuovere in blocco i membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scarica un elenco di tutti i gruppi](groups-bulk-download.md)
