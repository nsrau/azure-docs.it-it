---
title: Gestire e aggiornare la cache HPC di Azure
description: Come gestire e aggiornare la cache HPC di Azure con il portale di Azure o l'interfaccia della riga di comando di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 08/31/2020
ms.author: v-erkel
ms.openlocfilehash: 067b12d4dcfd5ba2b730204ef680b900d79f1b72
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648073"
---
# <a name="manage-your-cache"></a>Gestire la cache

La pagina Panoramica della cache nel portale di Azure Mostra i dettagli del progetto, lo stato della cache e le statistiche di base per la cache. Dispone inoltre di controlli per arrestare o avviare la cache, eliminare la cache, svuotare i dati nell'archiviazione a lungo termine e aggiornare il software.

Questo articolo illustra anche come eseguire queste attività di base con l'interfaccia della riga di comando di Azure.

Per aprire la pagina Panoramica, selezionare la risorsa della cache nell'portale di Azure. Ad esempio, caricare la pagina **tutte le risorse** e fare clic sul nome della cache.

![screenshot della pagina Panoramica di un'istanza di cache HPC di Azure](media/hpc-cache-overview.png)

I pulsanti nella parte superiore della pagina possono essere utili per gestire la cache:

* **Avvia** e [**Arresta**](#stop-the-cache) -riprende o sospende l'operazione di cache
* [**Flush**](#flush-cached-data) -scrive i dati modificati nelle destinazioni di archiviazione
* [**Aggiornamento**](#upgrade-cache-software) : aggiorna il software della cache
* [**Raccolta di dati diagnostici**](#collect-diagnostics) -carica informazioni di debug
* **Aggiorna** -ricarica la pagina Panoramica
* [**Delete: Elimina**](#delete-the-cache) definitivamente la cache

Altre informazioni su queste opzioni sono disponibili di seguito.

Fare clic sull'immagine seguente per guardare un [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) che illustra le attività di gestione della cache.

[![anteprima video: cache HPC di Azure: gestione (fare clic per visitare la pagina del video)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Arrestare la cache

È possibile arrestare la cache per ridurre i costi durante un periodo di inattività. Quando la cache viene arrestata, non viene addebitato alcun tempo di attesa, ma viene addebitata l'archiviazione su disco allocata della cache. Per informazioni dettagliate, vedere la pagina relativa ai [prezzi](https://aka.ms/hpc-cache-pricing) .

Una cache arrestata non risponde alle richieste del client. Prima di arrestare la cache, è necessario smontare i client.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Il pulsante **Arresta** sospende una cache attiva. Il pulsante **Interrompi** è disponibile quando lo stato di una cache è **integro** o **danneggiato**.

![screenshot dei pulsanti principali con l'arresto evidenziato e un messaggio popup che descrive l'azione di arresto e la richiesta di continuare? con Sì (impostazione predefinita) e nessun pulsante](media/stop-cache.png)

Dopo aver fatto clic su Sì per confermare l'arresto della cache, la cache Scarica automaticamente il contenuto nelle destinazioni di archiviazione. Questo processo potrebbe richiedere del tempo, ma garantisce la coerenza dei dati. Infine, lo stato della cache diventa **arrestato**.

Per riattivare una cache arrestata, fare clic sul pulsante **Avvia** . Non è necessaria alcuna conferma.

![screenshot dei pulsanti principali con inizio evidenziato](media/start-cache.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Sospendere temporaneamente una cache con il comando [AZ HPC-cache Stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Questa azione è valida solo quando lo stato di una cache è **integro** o **danneggiato**.

La cache Scarica automaticamente il contenuto nelle destinazioni di archiviazione prima di arrestarsi. Questo processo potrebbe richiedere del tempo, ma garantisce la coerenza dei dati.

Al termine dell'azione, lo stato della cache diventa **arrestato**.

Riattivare una cache arrestata con [AZ HPC-cache Start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Quando si esegue il comando avvia o Interrompi, la riga di comando Mostra un messaggio di stato "in esecuzione" fino al completamento dell'operazione.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Al termine, il messaggio viene aggiornato a "completato" e Mostra i codici restituiti e altre informazioni.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Scarica dati memorizzati nella cache

Il pulsante **Scarica** nella pagina Panoramica indica alla cache di scrivere immediatamente tutti i dati modificati archiviati nella cache nelle destinazioni di archiviazione back-end. La cache Salva regolarmente i dati nelle destinazioni di archiviazione, pertanto non è necessario eseguire questa operazione manualmente, a meno che non si voglia assicurarsi che il sistema di archiviazione back-end sia aggiornato. Ad esempio, è possibile usare **Flush** prima di acquisire uno snapshot di archiviazione o di controllare le dimensioni del set di dati.

> [!NOTE]
> Durante il processo di scaricamento, la cache non può gestire le richieste dei client. L'accesso alla cache viene sospeso e riprende al termine dell'operazione.

Quando si avvia l'operazione di scaricamento della cache, la cache interrompe l'accettazione delle richieste client e lo stato della cache nella pagina panoramica viene modificato in **svuotamento**.

I dati nella cache vengono salvati nelle destinazioni di archiviazione appropriate. A seconda della quantità di dati che devono essere scaricati, il processo può richiedere alcuni minuti o più di un'ora.

Dopo che tutti i dati sono stati salvati in destinazioni di archiviazione, la cache avvia automaticamente le richieste dei client. Lo stato della cache torna a **integro**.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per svuotare la cache, fare clic sul pulsante **Scarica** e quindi fare clic su **Sì** per confermare l'azione.

![screenshot dei pulsanti principali con lo scaricamento evidenziato e un messaggio popup che descrive l'azione di scaricamento e la richiesta di "continuare?" con Sì (impostazione predefinita) e nessun pulsante](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Usare [AZ HPC-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) per forzare la scrittura di tutti i dati modificati nelle destinazioni di archiviazione da parte della cache.

Esempio:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Al termine dello svuotamento, viene restituito un messaggio di operazione completata.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Aggiornamento del software della cache

Se è disponibile una nuova versione del software, il pulsante **Aggiorna** diventa attivo. Verrà visualizzato anche un messaggio nella parte superiore della pagina relativa all'aggiornamento del software.

![screenshot della riga superiore dei pulsanti con il pulsante Aggiorna abilitato](media/hpc-cache-upgrade-button.png)

L'accesso client non viene interrotto durante l'aggiornamento del software, ma rallenta le prestazioni della cache. Pianificare l'aggiornamento del software durante le ore di utilizzo non di punta o in un periodo di manutenzione pianificato.

L'aggiornamento software può richiedere diverse ore. Le cache configurate con una velocità effettiva superiore importano più tempo per l'aggiornamento rispetto alle cache con valori di velocità effettiva di picco

Quando è disponibile un aggiornamento software, è necessario disporre di una settimana per applicarlo manualmente. La data di fine è elencata nel messaggio di aggiornamento. Se non si esegue l'aggiornamento durante tale periodo, Azure applica automaticamente l'aggiornamento alla cache. La tempistica dell'aggiornamento automatico non è configurabile. Se si è interessati all'effetto sulle prestazioni della cache, è necessario aggiornare il software manualmente prima della scadenza del periodo di tempo.

Se la cache viene arrestata al termine della data di fine, la cache aggiornerà automaticamente il software al successivo avvio. L'aggiornamento potrebbe non essere avviato immediatamente, ma verrà avviato nella prima ora.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Fare clic sul pulsante **Aggiorna** per avviare l'aggiornamento software. Lo stato della cache diventa **aggiornamento** fino a quando l'operazione non viene completata.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Nell'interfaccia della riga di comando di Azure le nuove informazioni software sono incluse alla fine del rapporto stato cache. Usare [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) per verificare. Cercare la stringa "upgradeStatus" nel messaggio.

Usare [AZ HPC-cache Upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) per applicare l'aggiornamento, se disponibile.

Se non è disponibile alcun aggiornamento, questa operazione non ha alcun effetto.

Questo esempio mostra lo stato della cache (nessun aggiornamento disponibile) e i risultati del comando upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Raccolta di diagnostica

Il pulsante **Raccogli diagnostica** avvia manualmente il processo per raccogliere le informazioni di sistema e caricarlo nel servizio Microsoft e il supporto per la risoluzione dei problemi. La cache raccoglie e carica automaticamente le stesse informazioni di diagnostica se si verifica un problema di cache grave.

Utilizzare questo controllo se il servizio Microsoft e il supporto tecnico lo richiedono.

Dopo aver fatto clic sul pulsante, fare clic su **Sì** per confermare il caricamento.

![screenshot del messaggio di conferma popup ' Avvia raccolta diagnostica '. Il pulsante predefinito ' Sì' è evidenziato.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Eliminare la cache

Il pulsante **Elimina** Elimina la cache. Quando si elimina una cache, tutte le relative risorse vengono distrutte e non vengono più addebitate spese per l'account.

I volumi di archiviazione back-end usati come destinazioni di archiviazione non sono interessati quando si elimina la cache. È possibile aggiungerli a una futura cache in un secondo momento oppure rimuovere le autorizzazioni separatamente.

> [!NOTE]
> La cache HPC di Azure non scrive automaticamente i dati modificati dalla cache nei sistemi di archiviazione back-end prima di eliminare la cache.
>
> Per assicurarsi che tutti i dati nella cache siano stati scritti nell'archiviazione a lungo termine, [arrestare la cache](#stop-the-cache) prima di eliminarla. Assicurarsi che venga visualizzato lo stato **interrotto** prima dell'eliminazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dopo aver arrestato la cache, fare clic sul pulsante **Elimina** per rimuovere definitivamente la cache.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Usare il comando dell'interfaccia della riga di comando di Azure [AZ HPC-cache Delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) per rimuovere definitivamente la cache.

Esempio:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Metriche e monitoraggio della cache

La pagina Overview Mostra i grafici per alcune statistiche di base della cache, ovvero la velocità effettiva della cache, le operazioni al secondo e la latenza.

![Screenshot di tre grafici a linee che mostrano le statistiche sopra indicate per una cache di esempio](media/hpc-cache-overview-stats.png)

Questi grafici fanno parte degli strumenti di monitoraggio e analisi incorporati di Azure. Gli strumenti e gli avvisi aggiuntivi sono disponibili nelle pagine sotto l'intestazione **monitoraggio** nell'intestazione laterale del portale. Per altre informazioni, vedere la sezione portale della [documentazione di monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sugli [strumenti metriche e statistiche di Azure](../azure-monitor/index.yml)
* Ottenere [assistenza con la cache HPC di Azure](hpc-cache-support-ticket.md)
