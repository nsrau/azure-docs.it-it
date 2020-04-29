---
title: Funzionalità avanzate di Esplora metriche di Azure
description: Informazioni sulle funzionalità avanzate di monitoraggio di Azure Esplora metriche
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371591"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Funzionalità avanzate di Esplora metriche di Azure

> [!NOTE]
> Questo articolo presuppone che l'utente abbia familiarità con le funzionalità di base di Esplora metriche. Se si è un nuovo utente e si vuole imparare a creare il primo grafico delle metriche, vedere [Introduzione ad Azure Esplora metriche](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metriche in Azure

[In Monitoraggio di Azure le metriche](data-platform-metrics.md) sono la serie di valori e conteggi misurati che vengono raccolti e archiviati nel corso del tempo. Sono disponibili metriche standard (o di "piattaforma") e metriche personalizzate. Le metriche standard appartengono alla piattaforma stessa di Azure. Esse riflettono le statistiche sull'integrità e l'utilizzo delle risorse di Azure. Mentre le metriche personalizzate vengono inviate ad Azure dalle applicazioni usando l' [api Application Insights per gli eventi e le metriche personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), l' [estensione di Windows diagnostica di Azure (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)o l' [API REST di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Creare visualizzazioni con più metriche e grafici

È possibile creare grafici che tracciano più righe di metriche o visualizzano più grafici delle metriche contemporaneamente. Questa funzionalità consente di:

- correlare le metriche correlate nello stesso grafico per vedere come un valore è correlato a un altro
- visualizzare le metriche con unità di misura diverse in prossimità
- aggregare e confrontare visivamente le metriche da più risorse

Se, ad esempio, si dispone di 5 account di archiviazione e si desidera conoscere la quantità di spazio totale utilizzata tra di essi, è possibile creare un grafico ad area (in pila) che mostri il singolo e la somma di tutti i valori in momenti specifici.

### <a name="multiple-metrics-on-the-same-chart"></a>Più metriche nello stesso grafico

Per prima cosa, [creare un nuovo grafico](metrics-getting-started.md#create-your-first-metric-chart). Fare clic su **Aggiungi metrica** e ripetere i passaggi per aggiungere un'altra metrica nello stesso grafico.

   > [!NOTE]
   > È preferibile in genere non avere nello stesso grafico metriche con unità di misura diverse (ad esempio "millisecondi" e "KB") o con proporzioni molto diverse. Si può in questo caso considerare di usare più grafici. Fare clic sul pulsante Aggiungi grafico per creare più grafici in Esplora metriche.

### <a name="multiple-charts"></a>Più grafici

Fare clic sul **grafico Aggiungi** e creare un altro grafico con una metrica diversa.

### <a name="order-or-delete-multiple-charts"></a>Ordinare o eliminare più grafici

Per ordinare o eliminare più grafici, fare clic sul simbolo dei puntini di sospensione ( **..** .) per aprire il menu grafico e scegliere la voce di menu appropriata di **Sposta su**, **Sposta giù**o **Elimina**.

## <a name="apply-filters-to-charts"></a>Applicare filtri ai grafici

È possibile applicare filtri ai grafici che mostrano metriche con dimensioni. Si prenda ad esempio il caso della metrica "Transaction count" (Numero transazioni) che dispone di una dimensione, "Response type" (Tipo di risposta), che indica se la risposta dalle transazioni ha avuto o meno esito positivo. Se si applica un filtro a questa dimensione, nel grafico viene tracciata una linea che riflette solo uno dei due tipi di transazioni, con esito positivo o con esito negativo. 

### <a name="to-add-a-filter"></a>Per aggiungere un filtro

1. Selezionare **Aggiungi filtro** sopra il grafico

2. Selezionare la dimensione (proprietà) che si desidera filtrare.

   ![Immagine di metrica](./media/metrics-charts/00006.png)

3. Selezionare i valori della dimensione che si desidera includere nel tracciamento del grafico. Questo esempio mostra l'applicazione del filtro per le transazioni di archiviazione con esito positivo:

   ![Immagine di metrica](./media/metrics-charts/00007.png)

4. Dopo avere selezionato i valori di filtro, fare clic all'esterno del selettore di filtro per chiuderlo. Il grafico mostra a questo punto il numero di transazioni di archiviazione con esito negativo:

   ![Immagine di metrica](./media/metrics-charts/00008.png)

5. È possibile ripetere i passaggi da 1 a 4 per applicare più filtri agli stessi grafici.



## <a name="apply-splitting-to-a-chart"></a>Applicare la suddivisione a un grafico

È possibile suddividere una metrica per dimensione in modo da confrontare visivamente i diversi segmenti e identificare i segmenti esterni di una dimensione.

### <a name="apply-splitting"></a>Applicare la suddivisione

1. Fare clic su **Applicare separazione** sopra il grafico.
 
   > [!NOTE]
   > Impossibile utilizzare la suddivisione con grafici con più metriche. Inoltre, è possibile disporre di più filtri, ma solo di una dimensione di suddivisione applicata a un singolo grafico.

2. Scegliere una dimensione in cui si desidera segmentare il grafico:

   ![Immagine di metrica](./media/metrics-charts/00010.png)

   Il grafico mostra ora più righe, una per ogni segmento della dimensione:

   ![Immagine di metrica](./media/metrics-charts/00012.png)

3. Fare clic al di fuori del **selettore di raggruppamento** per chiuderlo.

   > [!NOTE]
   > Usare sia il filtro che la separazione nella stessa dimensione per nascondere i segmenti che sono irrilevanti per lo scenario e semplificare così la lettura dei grafici.

## <a name="lock-boundaries-of-chart-y-axis"></a>Bloccare i limiti dell'asse y del grafico

Bloccare l'intervallo dell'asse y diventa importante quando il grafico mostra fluttuazioni minori di valori più grandi. 

Ad esempio, quando il volume di richieste riuscite si abbassa dal 99,99% al 99,5%, ciò potrebbe rappresentare una significativa riduzione della qualità del servizio. Tuttavia, notare una piccola fluttuazione del valore numerico sarebbe difficile o persino impossibile dalle impostazioni predefinite del grafico. In questo caso è possibile bloccare il limite inferiore del grafico al 99%, cosa che può rendere questa piccola diminuzione più evidente. 

Un altro esempio è una fluttuazione della memoria disponibile, in cui il valore tecnicamente non raggiungerà mai lo zero. Fissare l'intervallo su un valore più alto può rendere la diminuzione della memoria disponibile più facile da rilevare. 

Per controllare l'intervallo dell'asse y, usare "..." nel menu del grafico e selezionare **Modifica grafico** per accedere alle impostazioni avanzate del grafico. Modificare i valori nella sezione Intervallo asse Y oppure usare il pulsante **Auto** per ripristinare le impostazioni predefinite.

![Immagine di metrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Bloccare i limiti dell'asse y dei grafici che tengono traccia dei vari conteggi o somme in un periodo di tempo (e che quindi usano le aggregazioni Count, Sum, Minimum o Maximum) richiede in genere di specificare una granularità temporale fissa anziché di basarsi sulle impostazioni predefinite automatiche. Questa operazione è necessaria perché i valori nei grafici cambiano quando la granularità temporale viene automaticamente modificata dall'utente che ridimensiona la finestra del browser o che passa da una risoluzione dello schermo a un'altra. La modifica risultante nella granularità temporale influenza l'aspetto del grafico, invalidando la selezione corrente dell'intervallo dell'asse y.

## <a name="change-colors-of-chart-lines"></a>Modificare i colori delle linee del grafico

Una volta configurati i grafici, alle linee del grafico viene assegnato automaticamente un colore da una tavolozza predefinita. È possibile modificare questi colori.

Per modificare il colore di una linea del grafico, fare clic sulla barra colorata nella legenda che corrisponde al grafico. Viene visualizzata la finestra di dialogo Selezione colori. Utilizzare la selezione colori per configurare il colore per la riga.

Una volta configurati, i colori del grafico rimarranno in questo modo quando si aggiunge il grafico a un dashboard. Nella sezione seguente viene illustrato come aggiungere un grafico.

> [!NOTE]
> A causa dei vincoli della pianificazione del rilascio e della pubblicazione, la modifica dei colori delle linee del grafico richiede temporaneamente il passaggio di un parametro speciale **? feature. ColorPicker = true** all'avvio portale di Azure [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true). Questa limitazione verrà rimossa a breve. 

![Immagine di metrica](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Aggiungere grafici ai dashboard

Dopo avere configurato i grafici, è possibile che si desideri aggiungerli ai dashboard per poterli visualizzare nuovamente, possibilmente nel contesto di altri dati di telemetria di monitoraggio, o condividere con il team.

Per aggiungere un grafico configurato a un dashboard:

Dopo avere configurato il grafico, fare clic sul menu delle **azioni del grafico** nell'angolo in alto a destra del grafico e fare clic su **Aggiungi al dashboard**.

![Immagine di metrica](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Creazione di regole di avviso

È possibile usare i criteri impostati per visualizzare le metriche come base di una regola di avviso basata su metriche. La nuova regola di avviso includerà la risorsa di destinazione, la metrica, la suddivisione e le dimensioni di filtro del grafico. Sarà possibile modificare queste impostazioni in seguito nel riquadro di creazione della regola di avviso.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Per creare una nuova regola di avviso, fare clic su **Nuova regola di avviso**.

![Pulsante Nuova regola di avviso evidenziato in rosso](./media/metrics-charts/015.png)

Si verrà reindirizzati al riquadro di creazione della regola di avviso con le dimensioni di metriche sottostanti del grafico prepopolato per semplificare la generazione di regole di avviso personalizzate.

![Creare la regola di avviso](./media/metrics-charts/016.png)

Per altre informazioni sulla configurazione di avvisi delle metriche, vedere questo [articolo](alerts-metric.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

*All'interno del grafico non vengono visualizzati dati.*

* I filtri si applicano a tutti i grafici del riquadro. Assicurarsi che, mentre ci si concentra su un grafico, non sia stato impostato un filtro che escluda tutti i dati di un altro grafico.

* Se si desidera impostare filtri diversi nei vari grafici, creare grafici in diversi pannelli e salvarli come Preferiti separati. Se si desidera, è possibile impostarli sul dashboard in modo da visualizzarli uno accanto all'altro.

* Se si segmenta un grafico per una proprietà non definita nella metrica, il grafico sarà vuoto. Provare a cancellare la segmentazione (suddivisione) oppure scegliere un'altra proprietà.

## <a name="next-steps"></a>Passaggi successivi

  Per informazioni sulle procedure consigliate per la creazione di dashboard operativi con metriche, leggere [Creating custom KPI dashboards](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) (Creazione di dashboard KPI personalizzati).

