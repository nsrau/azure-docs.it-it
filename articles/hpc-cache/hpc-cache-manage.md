---
title: Gestire e aggiornare la cache HPC di Azure
description: Come gestire e aggiornare la cache HPC di Azure con la portale di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 9ad6348e15c8a25f721a89be7eab3e17c58ae17c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988867"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gestire la cache dalla portale di Azure

La pagina Panoramica della cache nel portale di Azure Mostra i dettagli del progetto, lo stato della cache e le statistiche di base per la cache. Dispone inoltre di controlli per arrestare o avviare la cache, eliminare la cache, svuotare i dati nell'archiviazione a lungo termine e aggiornare il software.

Per aprire la pagina Panoramica, selezionare la risorsa della cache nell'portale di Azure. Ad esempio, caricare la pagina **tutte le risorse** e fare clic sul nome della cache.

![screenshot della pagina Panoramica di un'istanza di cache HPC di Azure](media/hpc-cache-overview.png)

I pulsanti nella parte superiore della pagina possono essere utili per gestire la cache:

* **Avvio** e [**arresto**](#stop-the-cache) -sospende l'operazione cache
* [**Flush**](#flush-cached-data) -scrive i dati modificati nelle destinazioni di archiviazione
* [**Aggiornamento**](#upgrade-cache-software) : aggiorna il software della cache
* **Aggiorna** -ricarica la pagina Panoramica
* [**Delete: Elimina**](#delete-the-cache) definitivamente la cache

Altre informazioni su queste opzioni sono disponibili di seguito.

## <a name="stop-the-cache"></a>Arrestare la cache

È possibile arrestare la cache per ridurre i costi durante un periodo di inattività. Quando la cache viene arrestata, non viene addebitato alcun tempo di attesa, ma viene addebitata l'archiviazione su disco allocata della cache. Per informazioni dettagliate, vedere la pagina relativa ai [prezzi](https://aka.ms/hpc-cache-pricing) .

Una cache arrestata non risponde alle richieste del client. Prima di arrestare la cache, è necessario smontare i client.

Il pulsante **Arresta** sospende una cache attiva. Il pulsante **Interrompi** è disponibile quando lo stato di una cache è **integro** o **danneggiato**.

![screenshot dei pulsanti principali con l'arresto evidenziato e un messaggio popup che descrive l'azione di arresto e la richiesta di continuare? con Sì (impostazione predefinita) e nessun pulsante](media/stop-cache.png)

Dopo aver fatto clic su Sì per confermare l'arresto della cache, la cache Scarica automaticamente il contenuto nelle destinazioni di archiviazione. Questo processo potrebbe richiedere del tempo, ma garantisce la coerenza dei dati. Infine, lo stato della cache diventa **arrestato**.

Per riattivare una cache arrestata, fare clic sul pulsante **Avvia** . Non è necessaria alcuna conferma.

![screenshot dei pulsanti principali con inizio evidenziato](media/start-cache.png)

## <a name="flush-cached-data"></a>Scarica dati memorizzati nella cache

Il pulsante **Scarica** nella pagina Panoramica indica alla cache di scrivere immediatamente tutti i dati modificati archiviati nella cache nelle destinazioni di archiviazione back-end. La cache Salva regolarmente i dati nelle destinazioni di archiviazione, pertanto non è necessario eseguire questa operazione manualmente, a meno che non si voglia assicurarsi che il sistema di archiviazione back-end sia aggiornato. Ad esempio, è possibile usare **Flush** prima di acquisire uno snapshot di archiviazione o di controllare le dimensioni del set di dati.

> [!NOTE]
> Durante il processo di scaricamento, la cache non può gestire le richieste dei client. L'accesso alla cache viene sospeso e riprende al termine dell'operazione.

![screenshot dei pulsanti principali con lo scaricamento evidenziato e un messaggio popup che descrive l'azione di scaricamento e la richiesta di "continuare?" con Sì (impostazione predefinita) e nessun pulsante](media/hpc-cache-flush.png)

Quando si avvia l'operazione di scaricamento della cache, la cache interrompe l'accettazione delle richieste client e lo stato della cache nella pagina panoramica viene modificato in **svuotamento**.

I dati nella cache vengono salvati nelle destinazioni di archiviazione appropriate. A seconda della quantità di dati che devono essere scaricati, il processo può richiedere alcuni minuti o più di un'ora.

Dopo che tutti i dati sono stati salvati in destinazioni di archiviazione, la cache avvia automaticamente le richieste dei client. Lo stato della cache torna a **integro**.

## <a name="upgrade-cache-software"></a>Aggiornamento del software della cache

Se è disponibile una nuova versione del software, il pulsante **Aggiorna** diventa attivo. Verrà visualizzato anche un messaggio nella parte superiore della pagina relativa all'aggiornamento del software.

![screenshot della riga superiore dei pulsanti con il pulsante Aggiorna abilitato](media/hpc-cache-upgrade-button.png)

L'accesso client non viene interrotto durante l'aggiornamento del software, ma rallenta le prestazioni della cache. Pianificare l'aggiornamento del software durante le ore di utilizzo non di punta o in un periodo di manutenzione pianificato.

L'aggiornamento software può richiedere diverse ore. Le cache configurate con una velocità effettiva superiore importano più tempo per l'aggiornamento rispetto alle cache con valori di velocità effettiva di picco

Quando è disponibile un aggiornamento software, è necessario disporre di una settimana per applicarlo manualmente. La data di fine è elencata nel messaggio di aggiornamento. Se non si esegue l'aggiornamento durante tale periodo, Azure applica automaticamente l'aggiornamento alla cache. La tempistica dell'aggiornamento automatico non è configurabile. Se si è interessati all'effetto sulle prestazioni della cache, è necessario aggiornare il software manualmente prima della scadenza del periodo di tempo.

Fare clic sul pulsante **Aggiorna** per avviare l'aggiornamento software. Lo stato della cache diventa **aggiornamento** fino a quando l'operazione non viene completata.

## <a name="delete-the-cache"></a>Eliminare la cache

Il pulsante **Elimina** Elimina la cache. Quando si elimina una cache, tutte le relative risorse vengono distrutte e non vengono più addebitate spese per l'account.

I volumi di archiviazione back-end usati come destinazioni di archiviazione non sono interessati quando si elimina la cache. È possibile aggiungerli a una futura cache in un secondo momento oppure rimuovere le autorizzazioni separatamente.

> [!NOTE]
> La cache HPC di Azure non scrive automaticamente i dati modificati dalla cache nei sistemi di archiviazione back-end prima di eliminare la cache.
>
> Per assicurarsi che tutti i dati nella cache siano stati scritti nell'archiviazione a lungo termine, [arrestare la cache](#stop-the-cache) prima di eliminarla. Assicurarsi che venga visualizzato lo stato **interrotto** prima di fare clic sul pulsante Elimina.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Metriche e monitoraggio della cache

La pagina Overview Mostra i grafici per alcune statistiche di base della cache, ovvero la velocità effettiva della cache, le operazioni al secondo e la latenza.

![Screenshot di tre grafici a linee che mostrano le statistiche sopra indicate per una cache di esempio](media/hpc-cache-overview-stats.png)

Questi grafici fanno parte degli strumenti di monitoraggio e analisi incorporati di Azure. Gli strumenti e gli avvisi aggiuntivi sono disponibili nelle pagine sotto l'intestazione **monitoraggio** nell'intestazione laterale del portale. Per altre informazioni, vedere la sezione portale della [documentazione di monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

<!-- * Learn more about metrics and statistics for hpc cache -->
* Scopri di più sugli [strumenti metriche e statistiche di Azure](../azure-monitor/index.yml)
* Ottenere [assistenza con la cache HPC di Azure](hpc-cache-support-ticket.md)
