---
title: Gestire e aggiornare la cache HPC di AzureManage and update Azure HPC Cache
description: Come gestire e aggiornare la cache HPC di Azure usando il portale di AzureHow to manage and update Azure HPC Cache using the Azure portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537951"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gestire la cache dal portale di AzureManage your cache from the Azure portal

La pagina di panoramica della cache nel portale di Azure mostra i dettagli del progetto, lo stato della cache e le statistiche di base per la cache. Dispone inoltre di controlli per arrestare o avviare la cache, eliminare la cache, scaricare i dati nell'archiviazione a lungo termine e aggiornare il software.

Per aprire la pagina di panoramica, selezionare la risorsa cache nel portale di Azure.To open the overview page, select your cache resource in the Azure portal. Ad esempio, caricare la pagina **Tutte le risorse** e fare clic sul nome della cache.

![Screenshot della pagina Panoramica di un'istanza della cache HPC di Azure](media/hpc-cache-overview.png)

I pulsanti nella parte superiore della pagina possono aiutarti a gestire la cache:

* **Avvia** e [**arresta**](#stop-the-cache) - Sospende l'operazione di cacheStart and Stop - Suspends cache operation
* [**Flush**](#flush-cached-data) - Scrive i dati modificati nelle destinazioni di archiviazione
* [**Aggiornamento**](#upgrade-cache-software) - Aggiorna il software della cache
* **Aggiorna** - Ricarica la pagina di panoramica
* [**Elimina**](#delete-the-cache) - Elimina definitivamente la cache

Per saperne di più su queste opzioni di seguito.

## <a name="stop-the-cache"></a>Arrestare la cache

È possibile interrompere la cache per ridurre i costi durante un periodo di inattività. Non viene addebitato alcun costo per il tempo di attività mentre la cache è stata arrestata, ma viene addebitato lo spazio di archiviazione su disco allocato della cache. (Vedere la pagina [dei prezzi](https://aka.ms/hpc-cache-pricing) per i dettagli.)

Una cache arrestata non risponde alle richieste client. È necessario smontare i client prima di arrestare la cache.

Il pulsante **Stop** sospende una cache attiva. Il pulsante **Interrompi** è disponibile quando lo stato di una cache è **Integro** o **Degradato**.

![Screenshot dei pulsanti in alto con l'opzione Interrompi evidenziata e un messaggio popup che descrive l'azione di arresto e chiede "Continuare?" con i pulsanti Sì (impostazione predefinita) e No](media/stop-cache.png)

Dopo aver fatto clic su Sì per confermare l'arresto della cache, la cache scarica automaticamente il contenuto nelle destinazioni di archiviazione. Questo processo potrebbe richiedere del tempo, ma garantisce la coerenza dei dati. Infine, lo stato della cache diventa **Arrestato**.

Per riattivare una cache arrestata, fare clic sul pulsante **Start.** Non è necessaria alcuna conferma.

![Schermata dei pulsanti in alto con l'opzione Avvia evidenziata](media/start-cache.png)

## <a name="flush-cached-data"></a>Scarica dati memorizzati nella cache

Il pulsante **Scarica** nella pagina di panoramica indica alla cache di scrivere immediatamente tutti i dati modificati archiviati nella cache nelle destinazioni di archiviazione back-end. La cache salva regolarmente i dati nelle destinazioni di archiviazione, pertanto non è necessario eseguire questa operazione manualmente a meno che non si desideri assicurarsi che il sistema di archiviazione back-end sia aggiornato. Ad esempio, è possibile utilizzare **Flush** prima di creare uno snapshot di archiviazione o controllare le dimensioni del set di dati.

> [!NOTE]
> Durante il processo di scaricamento, la cache non può soddisfare le richieste client. L'accesso alla cache viene sospeso e riprende al termine dell'operazione.

![Screenshot dei pulsanti in alto con Flush evidenziato e un messaggio pop-up che descrive l'azione flush e chiedendo 'Vuoi continuare?' con i pulsanti Sì (impostazione predefinita) e No](media/hpc-cache-flush.png)

Quando si avvia l'operazione di scaricamento della cache, la cache interrompe l'accettazione delle richieste client e lo stato della cache nella pagina di panoramica viene modificato in **Flushing**.

I dati nella cache vengono salvati nelle destinazioni di archiviazione appropriate. A seconda della quantità di dati da scaricare, il processo può richiedere alcuni minuti o più di un'ora.

Dopo che tutti i dati vengono salvati nelle destinazioni di archiviazione, la cache ricomincia automaticamente a riacquistare le richieste client. Lo stato della cache torna in **Integro**.

## <a name="upgrade-cache-software"></a>Aggiornare il software della cache

Se è disponibile una nuova versione del software, il pulsante **Aggiorna** diventa attivo. Dovresti anche vedere un messaggio nella parte superiore della pagina sull'aggiornamento del software.

![Screenshot della riga superiore dei pulsanti con il pulsante Aggiorna abilitato](media/hpc-cache-upgrade-button.png)

L'accesso client non viene interrotto durante un aggiornamento software, ma le prestazioni della cache rallentano. Pianificare l'aggiornamento del software durante le ore di lavoro non di punta o in un periodo di manutenzione pianificato.

L'aggiornamento software può richiedere diverse ore. Le cache configurate con una velocità effettiva maggiore richiedono più tempo per l'aggiornamento rispetto alle cache con valori di velocità effettiva di picco più piccoli.

Quando è disponibile un aggiornamento software, si avrà una settimana o giù di lì per applicarlo manualmente. La data di fine è elencata nel messaggio di aggiornamento. Se non si esegue l'aggiornamento durante tale periodo, Azure applica automaticamente l'aggiornamento alla cache. La tempistica dell'aggiornamento automatico non è configurabile. Se si è preoccupati per l'impatto sulle prestazioni della cache, è necessario aggiornare il software manualmente prima della scadenza del periodo di tempo.

Se la cache viene arrestata al termine della data di fine, il software verrà aggiornato automaticamente al successivo avvio. (L'aggiornamento potrebbe non avviarsi immediatamente, ma inizierà nella prima ora.)

Fare clic sul pulsante **Aggiorna** per avviare l'aggiornamento software. Lo stato della cache diventa **Aggiornamento** fino al completamento dell'operazione.

## <a name="delete-the-cache"></a>Eliminare la cache

Il pulsante **Elimina** elimina la cache. Quando si elimina una cache, tutte le relative risorse vengono eliminate e non comportano più costi dell'account.

I volumi di archiviazione back-end usati come destinazioni di archiviazione non vengono interessati quando si elimina la cache. È possibile aggiungerli a una cache futura in un secondo momento o rimuovere le autorizzazioni separatamente.

> [!NOTE]
> La cache HPC di Azure non scrive automaticamente i dati modificati dalla cache ai sistemi di archiviazione back-end prima di eliminare la cache.
>
> Per assicurarsi che tutti i dati nella cache siano stati scritti nell'archivio a lungo termine, [arrestare la cache](#stop-the-cache) prima di eliminarla. Assicurarsi che mostri lo stato **Interrotto** prima di fare clic sul pulsante Elimina.

## <a name="cache-metrics-and-monitoring"></a>Metriche e monitoraggio della cache

La pagina di panoramica mostra i grafici per alcune statistiche di base della cache: velocità effettiva della cache, operazioni al secondo e latenza.

![screenshot di tre grafici a linee che mostrano le statistiche di cui sopra per una cache di esempio](media/hpc-cache-overview-stats.png)

Questi grafici fanno parte degli strumenti di monitoraggio e analisi predefiniti di Azure.These charts are part of Azure's built-in monitoring and analytics tools. Ulteriori strumenti e avvisi sono disponibili dalle pagine sotto l'intestazione **Monitoraggio** nella barra laterale del portale. Per altre informazioni, vedere la sezione del portale della [documentazione relativa](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)al monitoraggio di Azure.Learn more on the portal section of the Azure Monitoring documentation .

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle metriche e sugli [strumenti statistici](../azure-monitor/index.yml) di AzureLearn more about Azure metrics and statistics tools
* Ottenere [assistenza per la cache HPC di AzureGet help with your Azure HPC Cache](hpc-cache-support-ticket.md)
