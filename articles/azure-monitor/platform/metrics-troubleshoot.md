---
title: Risoluzione dei problemi relativi ai grafici delle metriche di monitoraggio di Azure
description: Risolvere i problemi relativi alla creazione, personalizzazione o interpretazione di grafici delle metriche
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d31b046bf02893affff84069ee92b3bd7735b904
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243232"
---
# <a name="troubleshooting-metrics-charts"></a>Risoluzione dei problemi relativi ai grafici delle metriche

Usare questo articolo se si verificano problemi di creazione, personalizzazione o interpretazione dei grafici in Esplora metriche di Azure. Se non si ha [familiarità con le](metrics-getting-started.md) metriche, vedere Introduzione a Esplora metriche e [funzionalità avanzate di Esplora metriche](metrics-charts.md). È anche possibile vedere [esempi](metric-chart-samples.md) dei grafici delle metriche configurati.

## <a name="cant-find-your-resource-to-select-it"></a>La risorsa non è stata trovata per selezionarla

Quando si fa clic sul pulsante **Selezionare una risorsa**, la risorsa non viene visualizzata nella finestra di dialogo di selezione delle risorse.

**Soluzione:** Esplora metriche richiede di selezionare le sottoscrizioni e i gruppi di risorse prima di elencare le risorse disponibili. Se non viene visualizzata la risorsa:

1. Assicurarsi di avere selezionato la sottoscrizione corretta nell'elenco a discesa **Sottoscrizione**. Se la sottoscrizione non è elencata, fare clic su **Impostazioni di directory e sottoscrizione** e aggiungere una sottoscrizione con la risorsa.

1. Assicurarsi di aver selezionato il gruppo di risorse corretto.
    > [!WARNING]
    > Per prestazioni ottimali, quando si apre Esplora metriche, l'elenco a discesa **Gruppo di risorse** non dispone di nessun gruppo di risorse pre-selezionate. È necessario selezionare almeno un gruppo prima di poter visualizzare tutte le risorse.

## <a name="chart-shows-no-data"></a>Il grafico non Mostra dati

A volte i grafici potrebbero non mostrare dati dopo aver selezionato le metriche e le risorse corrette. Questo comportamento può essere causato da diversi dei motivi seguenti:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Il provider di risorse Microsoft. Insights non è registrato per la sottoscrizione

Per l'esplorazione delle metriche è necessario che nella sottoscrizione sia registrato il provider di risorse *Microsoft.Insights*. In molti casi è registrato automaticamente (vale a dire, dopo aver configurato una regola di avviso, personalizzare le impostazioni di diagnostica per qualsiasi risorsa o configurare una regola di scalabilità automatica). Se il provider di risorse Microsoft. Insights non è registrato, è necessario registrarlo manualmente attenendosi alla procedura descritta in [tipi e provider di risorse di Azure](../../azure-resource-manager/resource-manager-supported-services.md).

**Soluzione:** Aprire **sottoscrizioni**, scheda **provider di risorse** e verificare che *Microsoft. Insights* sia registrato per la sottoscrizione.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Non si dispone di diritti di accesso sufficienti per la risorsa

In Azure l'accesso alle metriche è controllato dal [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Per esplorare le metriche per qualsiasi risorsa, è necessario disporre del ruolo di [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader), [collaboratore al monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) o [collaboratore](../../role-based-access-control/built-in-roles.md#contributor).

**Soluzione:** Assicurarsi di disporre di autorizzazioni sufficienti per la risorsa da cui si esplorano le metriche.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>La risorsa non ha emesso alcuna metrica durante l'intervallo di tempo selezionato

Alcune risorse non generano costantemente metriche. Ad esempio, Azure non raccoglie metriche per le macchine virtuali arrestate. Altre risorse potrebbero generare metriche solo in presenza di determinate condizioni. Ad esempio, una metrica che mostra il tempo di elaborazione di una transazione richiede almeno una transazione. Se nell'intervallo di tempo selezionato non ci fossero transazioni, naturalmente il grafico sarebbe vuoto. Inoltre, mentre la maggior parte delle metriche in Azure viene raccolta ogni minuto, altre metriche vengono raccolte con frequenza inferiore. Per maggiori dettagli sulla metrica che si sta cercando di esplorare, vedere la documentazione sulle metriche.

**Soluzione:** Modificare l'ora del grafico in un intervallo più ampio. È possibile iniziare da "ultimi 30 giorni" utilizzando una granularità temporale maggiore (o basandosi sull'opzione "granularità automatica ora").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>È stato selezionato un intervallo di tempo maggiore di 30 giorni

[La maggior parte delle metriche in Azure viene conservata per 93 giorni](data-platform-metrics.md#retention-of-metrics). Tuttavia, è possibile eseguire query solo per un massimo di 30 giorni di dati per ogni singolo grafico. Questa limitazione non si applica alle [metriche basate su log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Soluzione:** Se viene visualizzato un grafico vuoto o il grafico Visualizza solo una parte dei dati della metrica, verificare che la differenza tra le date di inizio e di fine nella selezione ora non superi l'intervallo di 30 giorni.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Tutti i valori delle metriche non erano compresi nell'intervallo di asse y bloccato

[Bloccando i limiti dell'asse y del grafico](metrics-charts.md#lock-boundaries-of-chart-y-axis), è possibile impedire involontariamente all'area di visualizzazione dei grafici di mostrare la linea del grafico. Ad esempio, se l'asse y è bloccato su un intervallo compreso tra 0% e 50% e le metriche hanno un valore costante del 100%, il rendering della linea viene sempre eseguito fuori dall'area visibile, facendo apparire vuoto il grafico.

**Soluzione:** Verificare che i limiti dell'asse y del grafico non siano bloccati al di fuori dell'intervallo dei valori della metrica. Se i limiti dell'asse y sono bloccati, è possibile reimpostarli temporaneamente per garantire che i valori delle metriche non siano al di fuori dell'intervallo del grafico. È sconsigliabile bloccare l'intervallo dell'asse y con granularità automatica per i grafici con aggregazione **sum**, **min** e **max** perché i valori cambiano con granularità ridimensionando la finestra del browser o cambiando risoluzione dello schermo. Con la modifica della granularità, l'area visualizzata del grafico potrebbe restare vuota.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Si sta osservando una metrica del sistema operativo guest ma non è stata abilitata l'estensione diagnostica di Azure

La raccolta delle metriche del **sistema operativo guest** richiede la configurazione dell'estensione Diagnostica di Azure o la sua abilitazione tramite il pannello **Impostazioni di diagnostica** della risorsa.

**Soluzione:** Se Diagnostica di Azure estensione è abilitata ma non è ancora possibile visualizzare le metriche, seguire i passaggi descritti nella Guida alla [risoluzione dei problemi di diagnostica di Azure estensione](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Vedere anche la procedura di risoluzione dei problemi per [non è possibile selezionare le metriche e lo spazio dei nomi del sistema operativo](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Messaggio "errore durante il recupero dei dati" nel dashboard

Questo problema può verificarsi quando il dashboard è stato creato con una metrica deprecata e rimossa da Azure in un secondo momento. Per verificare se sia questo il caso, aprire la scheda **Metriche** della risorsa e controllare la metrica disponibile nel selettore di metrica. Se la metrica non viene visualizzata, allora è stata rimossa da Azure. In genere, quando una metrica è deprecata, è disponibile una nuova metrica migliore che offre una prospettiva simile sull'integrità delle risorse.

**Soluzione:** Aggiornare il riquadro con errore selezionando una metrica alternativa per il grafico nel dashboard. È possibile [consultare un elenco di metriche disponibili per i servizi di Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Grafico che mostra la linea tratteggiata

I grafici delle metriche di Azure usano lo stile di linea tratteggiata per indicare che è presente un valore mancante (noto anche come "valore null") tra due punti dati del tempo noto. Se, ad esempio, nel selettore di tempo è stata selezionata la granularità dell'ora "1 minuto" ma la metrica è stata segnalata a 07:26, 07:27, 07:29 e 07:30 (si noti un gap di minuti tra il secondo e il terzo punto dati), una linea tratteggiata connetterà 07:27 e 07:29 e una linea continua verrà connessa tutti gli altri punti dati. La linea tratteggiata scende verso il basso fino a zero quando la metrica usa l'aggregazione **count** e **Sum** . Per le aggregazioni **AVG**, **min** o **Max** , la linea tratteggiata connette due punti dati noti più vicini. Inoltre, quando mancano i dati sul lato all'estrema destra o sinistra del grafico, la linea tratteggiata si espande nella direzione dei punti dati mancanti.
  ![immagine metrica](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Soluzione:** Questo comportamento è da progettazione. È utile per identificare i punti dati mancanti. Il grafico a linee rappresenta una scelta superiore per la visualizzazione delle tendenze delle metriche ad alta densità, ma può essere difficile da interpretare per le metriche con valori di tipo sparse, soprattutto quando i valori con granularità temporale sono importanti. La linea tratteggiata rende più facile la lettura di questi grafici. Tuttavia, se il grafico è ancora poco chiaro, valutare l'uso di un tipo di grafico diverso per visualizzare le metriche. Un grafico tracciato sparse per la stessa metrica, ad esempio, mostra chiaramente ogni granularità visualizzando un punto solo quando è presente un valore e ignorando completamente il punto dati quando manca il valore: ![immagine metrica](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Se si preferisce comunque un grafico a linee per la metrica, lo spostamento del mouse sul grafico può aiutare a valutare la granularità temporale evidenziando il punto dati in corrispondenza della posizione del puntatore del mouse.

## <a name="chart-shows-unexpected-drop-in-values"></a>Il grafico mostra una riduzione imprevista dei valori

In molti casi, il calo percepito nei valori delle metriche deriva da un'errata interpretazione dei dati visualizzati nel grafico. È possibile giungere a conclusioni fuorvianti da un calo delle somme o dei conteggi quando nel grafico vengono visualizzati gli ultimi minuti perché gli ultimi punti dati delle metriche non sono stati ancora ricevuti o elaborati da Azure. A seconda del servizio, la latenza delle metriche di elaborazione può essere compresa in un intervallo di due minuti. Per i grafici che mostrano un intervallo di tempo recente con una granularità di 1 o 5 minuti, un calo del valore negli ultimi minuti diventa più evidente: ![immagine della metrica](./media/metrics-troubleshoot/drop-in-values.png)

**Soluzione:** Questo comportamento è da progettazione. Microsoft ritiene che la visualizzazione dei dati appena ricevuti sia utile anche quando i dati sono *parziali* o *incompleti*. In questo modo è possibile giungere a importanti conclusioni più rapidamente e avviare eventuali indagini sin da subito. Ad esempio, per una metrica che mostra il numero di errori, visualizzare un valore parziale X indica che si sono verificati almeno X errori in un minuto specificato. È possibile iniziare l'analisi del problema sin da subito, anziché attendere il conteggio esatto degli errori che si sono verificati in questo minuto, che potrebbe non essere importante. Il grafico verrà aggiornato una volta ricevuto l'intero set di dati, ma in quel momento potrebbe mostrare anche nuovi punti dati incompleti di minuti più recenti.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Impossibile selezionare lo spazio dei nomi e le metriche del sistema operativo guest

Le macchine virtuali e i set di scalabilità di macchine virtuali hanno due categorie di metriche: le metriche **host della macchina virtuale** raccolte dall'ambiente host di Azure e le metriche **del sistema operativo guest (classiche)** raccolte dall' [agente di monitoraggio](agents-overview.md) in esecuzione nelle macchine virtuali. Installare l'agente di monitoraggio abilitando l'[estensione di diagnostica di Azure](diagnostics-extension-overview.md).

Per impostazione predefinita, le metriche del sistema operativo Guest vengono archiviate nell'account di archiviazione di Azure, scelto nella scheda **Impostazioni di diagnostica** della risorsa. Se le metriche del sistema operativo Guest non vengono raccolte oppure Esplora metriche non può accedervi, viene visualizzato solo lo spazio dei nomi delle metriche per l'**host della macchina virtuale**:

![Immagine di metrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Soluzione:** Se lo spazio dei nomi e le metriche **del sistema operativo guest (classico)** non sono visualizzati in Esplora metriche:

1. Verificare che l'[Estensione di diagnostica di Azure](diagnostics-extension-overview.md) sia abilitata e configurata per raccogliere le metriche.
    > [!WARNING]
    > Non è possibile usare l'[agente di Log Analytics](agents-overview.md#log-analytics-agent) (noto anche come Microsoft Monitoring Agent, o "MMA") per inviare il **sistema operativo Guest** in un account di archiviazione.

1. Verificare che il provider di risorse **Microsoft. Insights** sia [registrato per la sottoscrizione](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Verificare che l'account di archiviazione non sia protetto da firewall. Il portale di Azure richiede l'accesso all'account di archiviazione per recuperare i dati delle metriche e tracciare i grafici.

1. Usare lo [strumento di esplorazione dell'archiviazione di Azure](https://azure.microsoft.com/features/storage-explorer/) per accertarsi che le metriche vengano trasmesse nell'account di archiviazione. Se le metriche non vengono raccolte, seguire le [Guida alla risoluzione dei problemi dell'estensione Diagnostica di Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come iniziare a usare Esplora metriche](metrics-getting-started.md)
* [Informazioni sulle funzionalità avanzate di Esplora metriche](metrics-charts.md)
* [Elenco di metriche disponibili per i servizi di Azure](metrics-supported.md)
* [Esempi di grafici configurati](metric-chart-samples.md)
