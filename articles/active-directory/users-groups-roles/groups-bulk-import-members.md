---
title: Importazione in blocco del caricamento per aggiungere membri a un gruppo - Azure Active Directory Documenti Microsoft
description: Aggiungere i membri del gruppo in blocco nell'interfaccia di amministrazione di Azure Active Directory.Add group members in bulk in the Azure Active Directory admin center.
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
ms.openlocfilehash: 15960caa55274f06159263c1af4a6c8280e83f4e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533479"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Importazione in blocco dei membri del gruppo in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD) è possibile aggiungere un numero elevato di membri a un gruppo usando un file con valori delimitati da virgole (CSV) per importare in blocco i membri del gruppo.

## <a name="to-bulk-import-group-members"></a>Per importare in blocco i membri del gruppo

1. Accedere al portale di [Azure](https://portal.azure.com) con un account amministratore utente nell'organizzazione. I proprietari dei gruppi possono anche importare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **tutti i gruppi**.
1. Aprire il gruppo a cui si stanno aggiungendo membri e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Importa membri**.
1. Nella pagina **Importa in blocco** i membri del gruppo selezionare **Scarica** per ottenere il modello di file CSV con le proprietà del membro del gruppo necessarie.

    ![Il comando Importa membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-import-members/import-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo che si desidera importare nel gruppo (i valori obbligatori sono **ID oggetto membro** o Nome entità **utente**). Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli URL per i membri da importare](./media/groups-bulk-import-members/csv-file.png)

1. Nella pagina In blocco dei membri del **gruppo,** in **Carica il file CSV,** individuare il file. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Quando il contenuto del file viene convalidato, nella pagina di importazione in blocco viene visualizzato **File caricato correttamente.** Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che importa i membri del gruppo nel gruppo.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica che l'operazione in blocco è riuscita.

## <a name="check-import-status"></a>Controllare lo stato dell'importazione

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Per informazioni dettagliate su ogni elemento pubblicitario all'interno dell'operazione in blocco, selezionare i valori nelle colonne **: Operazione riuscita**, **Non riuscita**o **Richieste totali.** Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione in blocco

Ogni attività in blocco per importare un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Ciò consente l'importazione di un elenco di almeno 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Rimuovere in blocco i membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scaricare un elenco di tutti i gruppi](groups-bulk-download.md)
