---
title: Caricamento bulk per aggiungere o creare membri di un gruppo-Azure Active Directory | Microsoft Docs
description: Aggiungere i membri del gruppo in blocco nell'interfaccia di amministrazione di Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8902c3147bbe142fc58d4e2c3fa83601c8ccbba3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203523"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Importazione bulk di membri del gruppo in Azure Active Directory

Utilizzando il portale Azure Active Directory (Azure AD), è possibile aggiungere un numero elevato di membri a un gruppo utilizzando un file con valori delimitati da virgole (CSV) per l'importazione bulk dei membri del gruppo.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento bulk per aggiungere correttamente Azure AD membri del gruppo in blocco. Il modello CSV potrebbe essere simile a questo esempio:

![Foglio di calcolo per il caricamento e la chiamata che spiega lo scopo e i valori per ogni riga e colonna](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Le righe in un modello CSV scaricato sono le seguenti:

- **Numero di versione**: la prima riga contenente il numero di versione deve essere inclusa nel file CSV di caricamento.
- **Intestazioni di colonna** &lt;: il formato delle intestazioni di colonna è il *nome* &gt; dell'elemento [PropertyName &lt;] *obbligatorio o vuoto*&gt;. Ad esempio: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Alcune versioni precedenti del modello potrebbero presentare variazioni minime. Per le modifiche apportate all'appartenenza al gruppo, è possibile scegliere quale identificatore utilizzare: ID oggetto membro o nome entità utente.
- **Esempi di riga**: sono stati inclusi nel modello una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate oppure il caricamento non può essere elaborato.
- Le colonne obbligatorie sono elencate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte verranno ignorate e non elaborate.
- Si consiglia di scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-bulk-import-group-members"></a>Per eseguire l'importazione bulk dei membri del gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione. I proprietari del gruppo possono anche importare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure ad selezionare **gruppi** > **tutti i gruppi**.
1. Aprire il gruppo a cui vengono aggiunti i membri e quindi selezionare **membri**.
1. Nella pagina **membri** selezionare **Importa membri**.
1. Nella pagina **membri del gruppo di importazione bulk** selezionare **download** per ottenere il modello di file CSV con le proprietà obbligatorie del membro del gruppo.

    ![Il comando importa membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-import-members/import-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo che si desidera importare nel gruppo. i valori obbligatori sono **ID oggetto membro** o **nome entità utente**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli ID dei membri da importare](./media/groups-bulk-import-members/csv-file.png)

1. Nella pagina **membri del gruppo importazione bulk** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Quando il contenuto del file viene convalidato, la pagina di importazione bulk Visualizza il **file caricato correttamente**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che importa i membri del gruppo nel gruppo.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica relativa all'esito positivo dell'operazione bulk.

## <a name="check-import-status"></a>Controlla stato importazione

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk** .

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Per informazioni dettagliate su ogni elemento della riga nell'operazione bulk, selezionare i valori nelle colonne **# Success**, **# Failure**o **Total requests** . Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione bulk

Ogni attività bulk per l'importazione di un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Questo consente l'importazione di un elenco di almeno 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Rimuovere in blocco i membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scarica un elenco di tutti i gruppi](groups-bulk-download.md)
