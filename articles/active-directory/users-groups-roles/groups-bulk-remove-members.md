---
title: Rimuovere in blocco i membri del gruppo caricando un file CSV - Azure Active Directory . Documenti Microsoft
description: Rimuovere i membri del gruppo nelle operazioni in blocco nell'interfaccia di amministrazione di Azure.Remove group members in bulk operations in the Azure admin center.
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
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533258"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Rimuovere in blocco i membri del gruppo in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD) è possibile rimuovere un numero elevato di membri da un gruppo usando un file con valori delimitati da virgole (CSV) per rimuovere in blocco i membri del gruppo.

## <a name="to-bulk-remove-group-members"></a>Per rimuovere in blocco i membri del gruppo

1. Accedere al portale di [Azure](https://portal.azure.com) con un account amministratore utente nell'organizzazione. I proprietari dei gruppi possono anche rimuovere in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **tutti i gruppi**.
1. Aprire il gruppo da cui si stanno rimuovendo i membri e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Rimuovi membri**.
1. Nella pagina **Rimozione in blocco dei membri** del gruppo selezionare **Scarica** per ottenere il modello di file CSV con le proprietà dei membri del gruppo necessarie.

   ![Il comando Rimuovi membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-remove-members/remove-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo che si desidera rimuovere dal gruppo (i valori obbligatori sono ID oggetto membro o Nome entità utente). Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli URL che i membri devono rimuovere](./media/groups-bulk-remove-members/csv-file.png)

1. Nella pagina Rimozione in blocco dei membri del **gruppo,** in **Carica il file CSV,** individuare il file. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Quando il contenuto del file viene convalidato, nella pagina di importazione in blocco viene visualizzato **File caricato correttamente.** Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che rimuove i membri del gruppo dal gruppo.
1. Al termine dell'operazione di rimozione, verrà visualizzata una notifica che l'operazione in blocco è riuscita.

## <a name="check-removal-status"></a>Controllare lo stato della rimozione

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Per informazioni dettagliate su ogni elemento pubblicitario all'interno dell'operazione in blocco, selezionare i valori nelle colonne **: Operazione riuscita**, **Non riuscita**o **Richieste totali.** Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-removal-service-limits"></a>Limiti del servizio di rimozione in blocco

Ogni attività in blocco da cui rimuovere un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Ciò consente la rimozione di un elenco di almeno 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Membri del gruppo di importazione in blocco](groups-bulk-import-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scaricare un elenco di tutti i gruppi](groups-bulk-download.md)
