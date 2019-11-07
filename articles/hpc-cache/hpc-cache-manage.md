---
title: Gestire e aggiornare la cache HPC di Azure
description: Come gestire e aggiornare la cache HPC di Azure con la portale di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 62b54bfe120acdde1fd22c4a0d04165ea7243b50
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582207"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gestire la cache dalla portale di Azure

La pagina Panoramica della cache nel portale di Azure Mostra i dettagli del progetto, lo stato della cache e le statistiche di base per la cache. Dispone inoltre di controlli per eliminare la cache, svuotare i dati nell'archiviazione a lungo termine o aggiornare il software.

Per aprire la pagina Panoramica, selezionare la risorsa della cache nell'portale di Azure. Ad esempio, caricare la pagina **tutte le risorse** e fare clic sul nome della cache.

![screenshot della pagina Panoramica di un'istanza di cache HPC di Azure](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

I pulsanti nella parte superiore della pagina possono essere utili per gestire la cache:

* [**Scarica**](#flush-cached-data) -scrive tutti i dati memorizzati nella cache nelle destinazioni di archiviazione
* [**Aggiornamento**](#upgrade-cache-software) : aggiorna il software della cache
* **Aggiorna** -ricarica la pagina Panoramica
* [**Delete: Elimina**](#delete-the-cache) definitivamente la cache

Altre informazioni su queste opzioni sono disponibili di seguito.

## <a name="flush-cached-data"></a>Scarica dati memorizzati nella cache

Il pulsante **Scarica** nella pagina Panoramica indica alla cache di scrivere immediatamente tutti i dati modificati archiviati nella cache nelle destinazioni di archiviazione back-end. La cache Salva regolarmente i dati nelle destinazioni di archiviazione, pertanto non è necessario eseguire questa operazione manualmente, a meno che non si voglia assicurarsi che il sistema di archiviazione back-end sia aggiornato. Ad esempio, è possibile usare **Flush** prima di acquisire uno snapshot di archiviazione o di controllare le dimensioni del set di dati.

> [!NOTE]
> Durante il processo di scaricamento, la cache non può gestire le richieste dei client. L'accesso alla cache viene sospeso e riprende al termine dell'operazione.

Quando si avvia l'operazione di scaricamento della cache, la cache interrompe l'accettazione delle richieste client e lo stato della cache nella pagina panoramica viene modificato in **svuotamento**.

I dati nella cache vengono salvati nelle destinazioni di archiviazione appropriate. Il processo può richiedere alcuni minuti o può richiedere un'ora o più, a seconda della quantità di dati scritti di recente nella cache.

Dopo che tutti i dati sono stati salvati in destinazioni di archiviazione, la cache avvia automaticamente le richieste dei client. Lo stato della cache torna a **integro**.

## <a name="upgrade-cache-software"></a>Aggiornamento del software della cache

Se è disponibile una nuova versione del software, il pulsante **Aggiorna** diventa attivo. È anche possibile che venga visualizzato un messaggio nella parte superiore della pagina relativa all'aggiornamento del software.

![screenshot della riga superiore dei pulsanti con il pulsante Aggiorna abilitato](media/hpc-cache-upgrade-button.png)

L'accesso client non viene interrotto durante l'aggiornamento del software, ma rallenta le prestazioni della cache. Pianificare l'aggiornamento del software durante le ore di utilizzo non di punta o in un periodo di manutenzione pianificato.

L'aggiornamento software può richiedere diverse ore. Le cache configurate con una velocità effettiva superiore importano più tempo per l'aggiornamento rispetto alle cache con valori di velocità effettiva di picco

Quando un aggiornamento software è disponibile, sono disponibili diversi giorni per applicarlo manualmente. La data di fine è elencata nel messaggio di aggiornamento. Se non si esegue l'aggiornamento durante tale periodo, Azure applica automaticamente l'aggiornamento alla cache. La tempistica dell'aggiornamento automatico non è configurabile. Se si è interessati a influisca sulle prestazioni della cache, è necessario aggiornare il software manualmente prima della scadenza del periodo di tempo.

Fare clic sul pulsante **Aggiorna** per avviare l'aggiornamento software. Lo stato della cache diventa **aggiornamento** fino a quando l'operazione non viene completata.

## <a name="delete-the-cache"></a>Eliminare la cache

Il pulsante **Elimina** Elimina la cache. Quando si elimina una cache, tutte le relative risorse vengono distrutte e non vengono più addebitate spese per l'account.

Le destinazioni di archiviazione non sono interessate quando si elimina la cache. È possibile aggiungerli a una futura cache in un secondo momento oppure rimuovere le autorizzazioni separatamente.

La cache Scarica automaticamente tutti i dati non salvati nelle destinazioni di archiviazione come parte dell'arresto finale.

## <a name="cache-metrics-and-monitoring"></a>Metriche e monitoraggio della cache

La pagina Overview Mostra i grafici per alcune statistiche di base della cache, ovvero la velocità effettiva della cache, le operazioni al secondo e la latenza.

![Screenshot di tre grafici a linee che mostrano le statistiche sopra indicate per una cache di esempio](media/hpc-cache-overview-stats.png)

Questi grafici fanno parte degli strumenti di monitoraggio e analisi incorporati di Azure. Gli strumenti e gli avvisi aggiuntivi sono disponibili nelle pagine sotto l'intestazione **monitoraggio** nell'intestazione laterale del portale. Per altre informazioni, vedere la sezione portale della [documentazione di monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

<!-- * Learn more about metrics and statistics for hpc cache -->
* Scopri di più sugli [strumenti metriche e statistiche di Azure](../azure-monitor/index.yml)
* Ottenere [assistenza con la cache HPC di Azure](hpc-cache-support-ticket.md)
