---
title: Gestire i dati cronologici con i criteri di conservazione-Azure SQL Edge
description: Informazioni su come gestire i dati cronologici con i criteri di conservazione in Azure SQL Edge
keywords: SQL Edge, conservazione dei dati
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392368"
---
# <a name="manage-historical-data-with-retention-policy"></a>Gestire i dati cronologici con i criteri di conservazione

La conservazione dei dati può essere abilitata nel database e in qualsiasi tabella sottostante singolarmente, consentendo agli utenti di creare criteri di invecchiamento flessibili per le tabelle e i database. L'applicazione della conservazione dei dati è semplice: è necessario impostare un solo parametro durante la creazione della tabella o come parte di un'operazione ALTER TABLE. 

Dopo aver definito i criteri di conservazione dei dati per un database e la tabella sottostante, viene eseguita un'attività timer di tempo in background per rimuovere tutti i record obsoleti dalla tabella abilitata per la conservazione dei dati. L'identificazione delle righe corrispondenti e la loro rimozione dalla tabella si verificano in modo trasparente nell'attività in background pianificata ed eseguita dal sistema. La condizione di età per le righe della tabella viene controllata in base alla colonna utilizzata come `filter_column` nella definizione della tabella. Se, ad esempio, il periodo di memorizzazione è impostato su una settimana, le righe della tabella idonee per la pulizia soddisfano una delle condizioni seguenti: 

- Se nella colonna filtro viene utilizzato il tipo di dati DATETIMEOFFSET, la condizione è `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- In caso contrario, la condizione è `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Fasi di pulizia della conservazione dei dati

L'operazione di pulizia della conservazione dei dati è costituita da due fasi. 
- Fase di individuazione: in questa fase l'operazione di pulizia identifica tutte le tabelle nei database utente per compilare un elenco per la pulizia. L'individuazione viene eseguita una volta al giorno.
- Fase di pulizia: in questa fase, la pulizia viene eseguita su tutte le tabelle con conservazione dei dati finito, identificati nella fase di individuazione. Se non è possibile eseguire l'operazione di pulizia in una tabella, tale tabella verrà ignorata nell'esecuzione corrente e verrà ritentata nell'iterazione successiva. I principi seguenti vengono usati durante la pulizia
    - Se una riga obsoleta è bloccata da un'altra transazione, la riga viene ignorata. 
    - La pulizia viene eseguita con un'impostazione predefinita di timeout di blocco di 5 secondi. Se non è possibile acquisire i blocchi sulle tabelle all'interno della finestra di timeout, la tabella verrà ignorata nell'esecuzione corrente e verrà ritentata nell'iterazione successiva.
    - Se si verifica un errore durante la pulizia di una tabella, la tabella verrà ignorata e verrà prelevata nell'iterazione successiva.

## <a name="manual-cleanup"></a>Pulizia manuale

A seconda delle impostazioni di conservazione dei dati in una tabella e della natura del carico di lavoro nel database, è possibile che il thread di pulizia automatica non rimuova completamente tutte le righe obsolete durante l'esecuzione. Per semplificare questa operazione e consentire agli utenti di rimuovere manualmente le righe obsolete, il `sys.sp_cleanup_data_retention` stored procedure è stato introdotto in Azure SQL Edge. 

Questa stored procedure accetta tre parametri. 
    - Nome schema: nome dello schema proprietario della tabella. Parametro obbligatorio. 
    - Nome tabella: nome della tabella per cui viene eseguita la pulizia manuale. Parametro obbligatorio. 
    - conteggio delle righe (output): variabile di output. Restituisce il numero di righe pulite dall'SP di pulizia manuale. Questo parametro è facoltativo. 

Nell'esempio seguente viene illustrata l'esecuzione del SP di pulizia manuale per la tabella `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Come vengono eliminate le righe obsolete

Il processo di pulizia dipende dal layout dell'indice della tabella. Viene creata un'attività in background per eseguire la pulizia dei dati obsoleti per tutte le tabelle con periodo di conservazione finito. La logica di pulizia per l'indice rowstore (albero B o heap) Elimina la riga obsoleta in blocchi più piccoli (fino a 10.000) riducendo il numero di richieste nel log del database e nel sottosistema IO. Anche se la logica di pulizia usa l'indice albero B richiesto, l'ordine di eliminazione delle righe con durata superiore al periodo di conservazione non può essere garantito con certezza. Di conseguenza evitare qualsiasi dipendenza dall'ordine di pulizia nelle applicazioni.

L'attività di pulizia per il columnstore cluster rimuove interi gruppi di righe in una sola volta (in genere contiene 1 milione di righe ognuna), che è molto efficiente, soprattutto quando i dati vengono generati e si esauriscono a ritmi elevati.

![Pulizia conservazione dei dati](./media/data-retention-cleanup/data-retention-cleanup.png)

Una buona compressione dei dati e una pulizia efficiente della conservazione rende l'indice columnstore cluster la scelta ideale per gli scenari in cui il carico di lavoro genera rapidamente una quantità elevata di dati.

> [!Note]
> Nel caso di indici e heap B-Tree, la conservazione dei dati esegue una query DELETE sulle tabelle sottostanti che possono entrare in conflitto con i trigger DELETE sulle tabelle. Si consiglia di rimuovere i trigger DELETE dalle tabelle o di non abilitare la conservazione dei dati nelle tabelle in cui è presente il trigger DML delete.

## <a name="monitoring-data-retention-cleanup"></a>Monitoraggio della pulizia della conservazione dei dati

Le operazioni di pulizia dei criteri di conservazione dei dati possono essere monitorate tramite gli eventi estesi (XEvent) in Azure SQL Edge. Per altre informazioni sugli eventi estesi, vedere [Panoramica di XEvent](/sql/relational-databases/extended-events/extended-events). 

I sei eventi estesi seguenti consentono di tenere traccia dello stato delle operazioni di pulizia. 

| Nome | Description |
|------| ------------|
| data_retention_task_started  | Si verifica quando viene avviata l'attività in background per la pulizia delle tabelle con i criteri di conservazione. |
| data_retention_task_completed  | Si verifica quando termina l'attività in background per la pulizia delle tabelle con criteri di conservazione. |
| data_retention_task_exception  | Si verifica quando l'attività in background per la pulizia delle tabelle con criteri di conservazione non riesce all'esterno del processo di pulizia della conservazione specifico per la tabella. |
| data_retention_cleanup_started  | Si verifica quando viene avviato il processo di pulizia della tabella con i criteri di conservazione dei dati. |
| data_retention_cleanup_exception  | Si verifica un processo di pulizia della tabella con criteri di conservazione non riuscito. |
| data_retention_cleanup_completed  | Si verifica quando termina il processo di pulizia della tabella con criteri di conservazione dei dati. |  

È stato inoltre aggiunto un nuovo tipo di buffer circolare denominato `RING_BUFFER_DATA_RETENTION_CLEANUP` per sys.dm_os_ring_buffers vista a gestione dinamica. Questa vista può essere utilizzata per monitorare le operazioni di pulizia della conservazione dei dati. 


## <a name="next-steps"></a>Passaggi successivi
- [Criteri di conservazione dei dati](data-retention-overview.md)
- [Abilitare e disabilitare i criteri di conservazione dei dati](data-retention-enable-disable.md)