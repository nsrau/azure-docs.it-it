---
title: Risoluzione dei problemi di grafici delle metriche di monitoraggio di Azure
description: Risoluzione dei problemi di creazione, personalizzazione o l'interpretazione dei grafici delle metriche
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939560"
---
# <a name="troubleshooting-metrics-charts"></a>Risoluzione dei problemi di grafici delle metriche

Usare questo articolo se si verificano problemi con la creazione, personalizzazione o l'interpretazione dei grafici in Esplora metriche di Azure. Se si ha familiarità con le metriche, Scopri [Introduzione a Esplora metriche](metrics-getting-started.md) e [avanzate funzionalità di Esplora metriche](metrics-charts.md). È anche possibile visualizzare [esempi](metric-chart-samples.md) dei grafici delle metriche configurati.

## <a name="cant-find-your-resource-to-select-it"></a>Non è possibile trovare la risorsa per selezionarla

Fa clic sui **selezionare una risorsa** pulsante, ma non la risorsa nella finestra di dialogo di selezione di risorse.

**Soluzione:** Esplora metriche richiede di selezionare le sottoscrizioni e gruppi di risorse prima dell'aggiunta di risorse disponibili. Se non viene visualizzata la risorsa:

1. Assicurarsi di aver selezionato la sottoscrizione corretta nel **sottoscrizione** elenco a discesa. Se la sottoscrizione non è elencata, fare clic sui **Directory e sottoscrizione impostazioni** e aggiungere una sottoscrizione con la risorsa.

1. Assicurarsi di aver selezionato il gruppo di risorse corretto.
    > [!WARNING]
    > Per prestazioni ottimali, quando si apre Esplora metriche, il **gruppo di risorse** elenco a discesa non dispone di nessun gruppo di risorse pre-selezionate. È necessario selezionare almeno un gruppo prima di poter vedere tutte le risorse.

## <a name="chart-shows-no-data"></a>Grafico non mostra alcun dato

In alcuni casi i grafici non potrebbero mostrano dati dopo aver selezionato le metriche e le risorse corrette. Questo comportamento può essere causato da diversi i motivi seguenti:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Provider di risorse Microsoft. Insights non è registrato per la sottoscrizione

È necessario esaminare le metriche *Microsoft. Insights* provider di risorse registrato nella sottoscrizione. In molti casi, è registrato automaticamente (vale a dire, dopo che si configura una regola di avviso, personalizzare le impostazioni di diagnostica per qualsiasi risorsa o configura una regola di scalabilità automatica). Se il provider di risorse Microsoft. Insights non è registrato, è necessario registrarlo manualmente seguendo i passaggi descritti in [provider di risorse di Azure e i tipi](../../azure-resource-manager/resource-manager-supported-services.md).

**Soluzione:** Aprire **abbonamenti**, **provider di risorse** scheda e verificare che *Microsoft. Insights* è registrato per la sottoscrizione.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Non hai accesso sufficienti per la risorsa

In Azure, l'accesso alle metriche è controllato dalle [controllo di accesso basato sui ruoli (RBAC)](../../role-based-access-control/overview.md). È necessario essere un membro del [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader), [collaboratore al monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor), o [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) per esplorare le metriche per qualsiasi risorsa.

**Soluzione:** Assicurarsi di avere autorizzazioni sufficienti per la risorsa da cui si Esplora metriche.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>La risorsa non generano metriche durante l'intervallo di tempo selezionato

Alcune risorse non generano costantemente le metriche. Ad esempio, Azure non raccoglie le metriche per macchine virtuali arrestate. Altre risorse possono trasmettere le metriche solo quando si verifica una condizione. Ad esempio, una metrica che mostra il tempo di elaborazione di una transazione richiede almeno una transazione. Se non esistesse alcuna transazione nell'intervallo di tempo selezionato, il grafico naturalmente sarà vuoto. Inoltre, la maggior parte delle metriche in Azure vengono raccolti ogni minuto, esistono alcune che vengono raccolti meno frequentemente. Vedere la documentazione delle metriche per ottenere altri dettagli sulla misura metrica che si sta tentando di esplorazione.

**Soluzione:** Modifica del tempo del grafico per una gamma più ampia. È possibile avviare "Ultimi 30 giorni" usando una maggiore granularità temporale (o basarsi sull'opzione "granularità ora automatica").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Si è scelto un intervallo di tempo maggiore di 30 giorni

[La maggior parte delle metriche in Azure vengono archiviate per 93 giorni](data-platform-metrics.md#retention-of-metrics). Tuttavia, è possibile solo eseguire una query per non più di 30 giorni di dati in ogni singolo grafico. Questa limitazione non si applica a [le metriche basate su log](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Soluzione:** Se viene visualizzato un grafico vuoto o il grafico visualizza solo una parte dei dati delle metriche, verificare che la differenza tra a e fine-date di inizio nella casella di selezione di tempo non supera l'intervallo di 30 giorni.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Sono stati tutti i valori delle metriche di fuori dell'intervallo dell'asse y bloccato

Dal [bloccare i limiti dell'asse y del grafico](metrics-charts.md#lock-boundaries-of-chart-y-axis), è possibile involontariamente rendere l'area di visualizzazione grafico non mostra la linea del grafico. Ad esempio, se l'asse y è bloccato su un intervallo compreso tra 0% e il 50% e la metrica ha un valore costante del 100%, la riga viene sempre eseguita all'esterno dell'area visibile, rendere il grafico visualizzato vuoto.

**Soluzione:** Verificare che i limiti dell'asse y del grafico non vengono bloccati compreso nell'intervallo dei valori della metrica. Se i limiti dell'asse y sono bloccati, è possibile reimpostare temporaneamente in modo da garantire che la metrica di valori non rientrano nell'intervallo del grafico. Blocco dell'intervallo dell'asse y non è consigliato con granularità automatica per i grafici con **somma**, **min**, e **max** delle aggregazioni perché i relativi valori vengono modificati con granularità ridimensionamento finestra del browser o passando dalla risoluzione dello uno schermo a altro. Commutazione granularità potrebbe lasciare l'area di visualizzazione dei valori vuoti grafico.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Si sta esaminando una metrica del sistema operativo Guest, ma non abilitarla l'estensione diagnostica di Azure

Raccolta di **del sistema operativo Guest** metriche richiedono la configurazione dell'estensione di diagnostica di Azure o l'abilitazione tramite il **le impostazioni di diagnostica** pannello per la risorsa.

**Soluzione:** Se è abilitata l'estensione diagnostica di Azure, ma non si riesce ancora a visualizzare le metriche, attenersi alla procedura illustrata nel [Guida alla risoluzione dei problemi di estensione diagnostica di Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Vedere anche la risoluzione dei problemi per [non è possibile scegliere lo spazio dei nomi del sistema operativo Guest e le metriche](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Messaggio "Errore durante il recupero dei dati" nel dashboard

Questo problema è comune quando il dashboard è stato creato con una metrica che è stata deprecata e rimosso da Azure in un secondo momento. Per verificare che sia il caso, aprire il **metriche** scheda della finestra di risorse e controllare la metrica disponibile nel selettore di metrica. Se non viene visualizzata la metrica, la metrica è stato rimosso da Azure. In genere, quando una metrica è deprecata, si verifica una migliore nuova metrica che offre una prospettiva simile sull'integrità delle risorse.

**Soluzione:** Aggiornare il riquadro dell'errore selezionando una metrica alternativa per il grafico nel dashboard. È possibile [esaminare l'elenco delle metriche disponibili per servizi di Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Grafico Mostra linea tratteggiata

I grafici delle metriche di Azure usano lo stile di linea tratteggiata per indicare che è presente un valore mancante (noto anche come "valore null") tra l'intervallo di tempo noto due punti dati. Ad esempio, se nel selettore di tempo scelto granularità temporale "minuto", ma la metrica è stata segnalata in 07:26, 07:27, 07:29 e 07:30 (tenere presente un gap tra i punti dati secondo e terzo al minuto), una linea tratteggiata si connetterà 07:27 e 07:29 e una linea a tinta unita si connetterà tutti gli altri punti dati. Elimina la linea tratteggiata fino a zero quando si usa la metrica **conteggio** e **somma** aggregazione. Per il **avg**, **min** oppure **max** aggregazioni, la linea tratteggiata connette due punti dati noti più vicini. Inoltre, quando mancano i dati sul lato all'estrema destra o a sinistra del grafico, la linea tratteggiata si espande alla direzione del punto dati mancanti.
  ![immagine di metrica](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Soluzione:** Questo comportamento dipende dalla progettazione. È utile per identificare i punti dati mancanti. Il grafico a linee è una scelta eccellente per la visualizzazione delle tendenze di metriche ad alta densità, ma potrà essere difficile da interpretare le metriche con valori di tipo sparse, soprattutto quando corelating valori con intervallo di tempo è importante. La linea tratteggiata rende più facile la lettura di questi grafici, ma se il grafico è ancora poco chiaro, prendere in considerazione le metriche vengono osservate con un tipo di grafico diversi. Ad esempio, un grafico sparsi per la stessa metrica mostra chiaramente ogni intervallo di tempo da solo visualizzazione quando è previsto un valore di un punto e ignorando i dati punto completamente quando il valore è mancano: ![immagine di metrica](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Se si preferisce comunque un grafico a linee per la metrica, lo spostamento del mouse sul grafico può aiutare a valutare la granularità temporale evidenziando il punto dati in corrispondenza della posizione del puntatore del mouse.

## <a name="chart-shows-unexpected-drop-in-values"></a>Grafico mostra drop imprevisto nei valori

In molti casi, l'eliminazione percepito nei valori delle metriche è errata interpretazione dei dati visualizzati nel grafico. È possibile porti a conclusioni fuorvianti da un calo di somme o conteggi quando nel grafico verranno visualizzati gli ultimi minuti perché l'ultimo punto dati delle metriche non è stato ricevuto o elaborato da Azure ancora. A seconda del servizio, la latenza delle metriche di elaborazione può essere nell'intervallo di due minuti. Per grafici che mostrano un intervallo di tempo recente con una granularità di-1 o 5 minuti, diventa più evidente un calo del valore in base agli ultimi minuti: ![immagine di metrica](./media/metrics-troubleshoot/drop-in-values.png)

**Soluzione:** Questo comportamento dipende dalla progettazione. Microsoft ritiene che la visualizzazione di dati, non appena si riceverla è utile anche quando i dati siano *parziali* oppure *incompleto*. In questo modo consente di apportare importanti conclusioni più rapidamente e avvia indagine sin da subito. Ad esempio, per una metrica che mostra il numero di errori, visualizzare un valore parziale X indica che non vi sono almeno X errori in un minuto specificato. È possibile iniziare l'analisi del problema sin da subito, anziché attendere per ottenere il conteggio esatto di errori che si sono verificati in questo minuto, che potrebbe non essere importante. Il grafico verrà aggiornato una volta che viene visualizzato l'intero set di dati, ma in quel momento, potrebbe essere visualizzato anche nuovi punti dati incompleti da minuti più recenti.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Non è possibile scegliere le metriche e dello spazio dei nomi del sistema operativo Guest

Le macchine virtuali e set di scalabilità di macchine virtuali sono due categorie di metriche: **Host macchina virtuale** le metriche che vengono raccolti dall'ambiente di hosting di Azure, e **del sistema operativo Guest** metriche che vengono raccolte per il [agente di monitoraggio](agents-overview.md) in esecuzione nelle macchine virtuali. Si installa l'agente di monitoraggio abilitando [estensione di diagnostica di Azure](diagnostics-extension-overview.md).

Per impostazione predefinita, le metriche del sistema operativo Guest vengono archiviate nell'account di archiviazione di Azure, quali è scegliere tra i **le impostazioni di diagnostica** scheda della risorsa. Se non vengono raccolte le metriche del sistema operativo Guest o Esplora metriche non può accedervi, viene visualizzata solo il **Host macchina virtuale** dello spazio dei nomi delle metriche:

![Immagine di metrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Soluzione:** Se non viene visualizzata **sistemi operativi Guest** dello spazio dei nomi e le metriche in Esplora metriche:

1. Verificare che [estensione di diagnostica di Azure](diagnostics-extension-overview.md) sia abilitato e configurato per raccogliere le metriche.
    > [!WARNING]
    > Non è possibile usare [agente di Log Analitica](agents-overview.md#log-analytics-agent) (detto anche il Microsoft Monitoring Agent, o "MMA") per inviare **del sistema operativo Guest** in un account di archiviazione.

1. Verificare che l'account di archiviazione non è protetto dal firewall.

1. Usare la [Esplora archivi Azure](https://azure.microsoft.com/features/storage-explorer/) per convalidare che le metriche vengano trasmessi nell'account di archiviazione. Se non vengono raccolte le metriche, seguire le [Guida alla risoluzione dei problemi di estensione diagnostica di Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle operazioni iniziali con Esplora metriche](metrics-getting-started.md)
* [Informazioni sulle funzionalità avanzate di Esplora metriche](metrics-charts.md)
* [Visualizzare un elenco delle metriche disponibili per servizi di Azure](metrics-supported.md)
* [Vedere esempi grafici configurati](metric-chart-samples.md)