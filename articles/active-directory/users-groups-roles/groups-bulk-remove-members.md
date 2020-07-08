---
title: Rimuovere in blocco i membri del gruppo caricando un file CSV - Azure Active Directory | Microsoft Docs
description: Rimuovere i membri del gruppo in operazioni in blocco nell'interfaccia di amministrazione di Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9084b486681ded0c194c93f07a404f5f5e88fa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728470"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Aggiungere o rimuovere in blocco i membri di un gruppo in Azure Active Directory

Tramite il portale di Azure Active Directory (Azure AD), è possibile rimuovere un numero elevato di membri da un gruppo usando un file con valori delimitati da virgole (CSV) per eseguire questa operazione in blocco.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento in blocco per aggiungere i membri di un gruppo di Azure AD in una sola volta. Il modello CSV potrebbe essere simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito:

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni. Per le modifiche apportate all'appartenenza al gruppo, è possibile scegliere quale identificatore usare, tra ID oggetto membro e nome dell'entità utente.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non può essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-bulk-remove-group-members"></a>Per rimuovere in blocco i membri del gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore utenti nell'organizzazione. I proprietari di gruppo possono anche rimuovere in blocco i membri dei loro gruppi.
1. In Azure AD selezionare **Gruppi** > **Tutti i gruppi**.
1. Aprire il gruppo da cui rimuovere i membri e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Rimuovi membri**.
1. Nella pagina **Rimuovi in blocco i membri del gruppo** selezionare **Scarica** per ottenere il modello di file CSV con le proprietà obbligatorie per i membri del gruppo.

   ![Il comando Rimuovi membri è disponibile nella pagina del profilo del gruppo](./media/groups-bulk-remove-members/remove-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo da rimuovere. I valori obbligatori sono quelli di ID oggetto membro o nome dell'entità utente. Salvare quindi il file.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Il file CSV contiene i nomi e gli ID dei membri del gruppo da rimuovere":::

1. Nella pagina **Rimuovi in blocco i membri del gruppo** individuare il file in **Caricare il file CSV**. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, nella pagina di importazione in blocco viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Dopo che il file ha superato la convalida, selezionare **Invia** per avviare l'operazione di Azure per la rimozione in blocco dei membri dal gruppo.
1. Al termine, verrà visualizzata una notifica per indicare la corretta esecuzione dell'operazione di rimozione in blocco.

## <a name="check-removal-status"></a>Controllare lo stato della rimozione

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Per informazioni dettagliate su ogni voce all'interno dell'operazione in blocco, selezionare i valori nelle colonne **N. operazioni riuscite**, **N. errori** o **Richieste totali**. Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-removal-service-limits"></a>Limiti del servizio di rimozione in blocco

Ogni attività in blocco per la rimozione di un elenco di membri da un gruppo essere eseguita per un massimo di un'ora. Questo consente la rimozione di un elenco di almeno 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Importare in blocco i membri del gruppo](groups-bulk-import-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scaricare un elenco di tutti i gruppi](groups-bulk-download.md)
