---
title: Esplora metriche di Monitoraggio di Azure
description: Informazioni sulle nuove funzionalità aggiunte in Esplora metriche di Monitoraggio di Azure
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2b6d982f514f347dc2a59f9ca8f3c33b8c24849b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827449"
---
# <a name="azure-monitor-metrics-explorer"></a>Esplora metriche di Monitoraggio di Azure

Esplora metriche di Monitoraggio di Azure è un componente del portale di Microsoft Azure che consente di tracciare grafici, correlare visivamente le tendenze ed esaminare i picchi e i cali nei valori delle metriche. Esplora metriche è uno strumento fondamentale da cui iniziare a esaminare le varie problematiche relative a prestazioni e disponibilità che si possono presentare con le applicazioni e l'infrastruttura ospitate in Azure o monitorate dai servizi Monitoraggio di Azure.

## <a name="metrics-in-azure"></a>Metriche in Azure

In Microsoft Azure le metriche sono la serie di valori e conteggi misurati che vengono raccolti e archiviati nel corso del tempo. Sono disponibili metriche standard (o di "piattaforma") e metriche personalizzate. Le metriche standard appartengono alla piattaforma stessa di Azure. Esse riflettono le statistiche sull'integrità e l'utilizzo delle risorse di Azure. Le metriche personalizzate vengono invece inviate ad Azure dalle applicazioni che usano l'[API di Application Insights per gli eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Le metriche personalizzate vengono archiviate nelle risorse di Application Insights insieme alle altre metriche specifiche delle applicazioni.

## <a name="create-a-new-chart"></a>Creare un nuovo grafico

1. Aprire il portale di Azure
2. Passare alla nuova scheda **Monitoraggio** e selezionare **Metriche**.

   ![Immagine metriche](./media/metrics-charts/00001.png)

3. Il **selettore di metrica** verrà aperto automaticamente. Scegliere una risorsa dall'elenco per visualizzarne le risorse associate. Nell'elenco vengono visualizzate solo le risorse che dispongono di metriche.

   ![Immagine metriche](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Se si dispone di più sottoscrizioni di Azure, Esplora metriche estrae le risorse da tutte le sottoscrizioni che sono selezionate nell'elenco Impostazioni del portale -> Filtra per sottoscrizioni. Per modificare l'elenco, fare clic sull'icona a forma di ingranaggio Impostazioni del portale in cima alla schermata e selezionare le sottoscrizioni che si desidera usare.

4. Per alcuni tipi di risorse, ad esempio gli account di archiviazione e le macchine virtuali, prima di selezionare una metrica è necessario scegliere uno **Spazio dei nomi**. Ogni spazio dei nomi presenta un proprio set di metriche che sono pertinenti solo a quello spazio dei nomi e non ad atri spazi dei nomi.

   Ogni Archiviazione di Azure, ad esempio, dispone di metriche per i sottoservizi "BLOB", "File", "Code" e "Tabelle", che fanno tutti parte dell'account di archiviazione. La metrica "Queue Message Count" (Numero di messaggi in coda) è peraltro ovviamente applicabile al sottoservizio "Code" e a nessun altro sottoservizio dell'account di archiviazione.

   ![Immagine metriche](./media/metrics-charts/00003.png)

5. Selezionare una metrica dall'elenco. Se si conosce solo in parte il nome della metrica desiderata, è possibile iniziare a digitare il nome per visualizzare un elenco filtrato di metriche disponibili:

   ![Immagine metriche](./media/metrics-charts/00004.png)

6. Dopo avere selezionato una metrica, verrà eseguito il rendering del grafico con l'aggregazione predefinita per la metrica selezionata. È possibile a questo punto fare semplicemente clic sul **selettore di metrica** per chiuderlo. Se lo si desidera, è possibile spostare il grafico in un'aggregazione diversa. Per alcune metriche, il passaggio di aggregazione consente di scegliere quale valore si desidera visualizzare nel grafico. È ad esempio possibile passare tra i valori medi, minimi e massimi. 

7. Facendo clic su **Aggiungi metrica** e ripetendo i passaggi da 3 a 6, è possibile aggiungere altre metriche nello stesso grafico.

   > [!NOTE]
   > È preferibile in genere non avere nello stesso grafico metriche con unità di misura diverse (ad esempio "millisecondi" e "KB") o con proporzioni molto diverse. Si può in questo caso considerare di usare più grafici. Fare clic sul pulsante Aggiungi grafico per creare più grafici in Esplora metriche.

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

## <a name="segment-a-chart"></a>Segmentare un grafico

È possibile suddividere una metrica per dimensione in modo da confrontare visivamente i diversi segmenti e identificare i segmenti esterni di una dimensione. 

### <a name="to-segment-a-chart"></a>Per segmentare un grafico

1. Fare clic su **Applicare separazione** sopra il grafico.
 
   > [!NOTE]
   > È possibile avere più filtri, ma un solo valore di separazione/segmentazione in ogni singolo grafico.

2. Scegliere una dimensione in cui si desidera segmentare il grafico:

   ![Immagine di metrica](./media/metrics-charts/00010.png)

   Il grafico mostra ora più righe, una per ogni segmento della dimensione:

   ![Immagine di metrica](./media/metrics-charts/00012.png)

3. Fare clic al di fuori del **selettore di raggruppamento** per chiuderlo.

   > [!NOTE]
   > Usare sia il filtro che la separazione nella stessa dimensione per nascondere i segmenti che sono irrilevanti per lo scenario e semplificare così la lettura dei grafici.

### <a name="new-alert-rule"></a>Nuova regola di avviso

È anche possibile usare i criteri impostati per visualizzare le metriche come base per la logica sottostante di una regola di avviso basata su metriche. 

Se si fa clic su **Nuova regola di avviso**

![Pulsante Nuova regola di avviso evidenziato in rosso](./media/metrics-charts/015.png)

Si verrà reindirizzati al riquadro di creazione della regola di avviso con le dimensioni di metriche sottostanti del grafico prepopolato per semplificare la generazione di regole di avviso personalizzate.

![Creare la regola di avviso](./media/metrics-charts/016.png)

Per altre informazioni sulla configurazione di avvisi delle metriche, vedere questo [articolo](alerts-metric.md).

## <a name="lock-boundaries-of-chart-y-axis"></a>Bloccare i limiti dell'asse y del grafico

Bloccare l'intervallo dell'asse y diventa importante quando il grafico mostra fluttuazioni minori di valori più grandi. 

Ad esempio, quando il volume di richieste riuscite si abbassa dal 99,99% al 99,5%, ciò potrebbe rappresentare una significativa riduzione della qualità del servizio. Tuttavia, notare una piccola fluttuazione del valore numerico sarebbe difficile o persino impossibile dalle impostazioni predefinite del grafico. In questo caso è possibile bloccare il limite inferiore del grafico al 99%, cosa che può rendere questa piccola diminuzione più evidente. 

Un altro esempio è una fluttuazione della memoria disponibile, in cui il valore tecnicamente non raggiungerà mai lo zero. Fissare l'intervallo su un valore più alto può rendere la diminuzione della memoria disponibile più facile da rilevare. 

Per controllare l'intervallo dell'asse y, usare "..." nel menu del grafico e selezionare **Modifica grafico** per accedere alle impostazioni avanzate del grafico. Modificare i valori nella sezione Intervallo asse Y oppure usare il pulsante **Auto** per ripristinare le impostazioni predefinite.

![Immagine di metrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Bloccare i limiti dell'asse y dei grafici che tengono traccia dei vari conteggi o somme in un periodo di tempo (e che quindi usano le aggregazioni Count, Sum, Minimum o Maximum) richiede in genere di specificare una granularità temporale fissa anziché di basarsi sulle impostazioni predefinite automatiche. Questa operazione è necessaria perché i valori nei grafici cambiano quando la granularità temporale viene automaticamente modificata dall'utente che ridimensiona la finestra del browser o che passa da una risoluzione dello schermo a un'altra. La modifica risultante nella granularità temporale influenza l'aspetto del grafico, invalidando la selezione corrente dell'intervallo dell'asse y.

## <a name="pin-charts-to-dashboards"></a>Aggiungere grafici ai dashboard

Dopo avere configurato i grafici, è possibile che si desideri aggiungerli ai dashboard per poterli visualizzare nuovamente, possibilmente nel contesto di altri dati di telemetria di monitoraggio, o condividere con il team.

Per aggiungere un grafico configurato a un dashboard:

Dopo avere configurato il grafico, fare clic sul menu delle **azioni del grafico** nell'angolo in alto a destra del grafico e fare clic su **Aggiungi al dashboard**.

![Immagine di metrica](./media/metrics-charts/00013.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

*All'interno del grafico non vengono visualizzati dati.*

* I filtri si applicano a tutti i grafici del riquadro. Assicurarsi che, mentre ci si concentra su un grafico, non sia stato impostato un filtro che escluda tutti i dati di un altro grafico.

* Se si desidera impostare filtri diversi nei vari grafici, creare grafici in diversi pannelli e salvarli come Preferiti separati. Se si desidera, è possibile impostarli sul dashboard in modo da visualizzarli uno accanto all'altro.

* Se si segmenta un grafico per una proprietà non definita nella metrica, il grafico sarà vuoto. Provare a cancellare la segmentazione (suddivisione) oppure scegliere un'altra proprietà.

## <a name="next-steps"></a>Passaggi successivi

  Per informazioni sulle procedure consigliate per la creazione di dashboard operativi con metriche, leggere [Creating custom KPI dashboards](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) (Creazione di dashboard KPI personalizzati).

