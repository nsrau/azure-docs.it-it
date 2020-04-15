---
title: Esplorare i dati usando Esplora risorse - Azure Time Series Insights Documenti Microsoft
description: Informazioni su come usare Azure Time Series Insights explorer per visualizzare i dati IoT.Learn how to use the Azure Time Series Insights explorer to view your IoT data.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: b564373780da5bba71bc46ddbac3dc69a0c94a4a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382772"
---
# <a name="azure-time-series-insights-explorer"></a>Strumento di esplorazione di Azure Time Series Insights

Questo articolo descrive le funzionalità e le opzioni di disponibilità generale per l'app Web Azure Time Series Insights [Explorer.](https://insights.timeseries.azure.com/) Time Series Insights Explorer illustra le potenti funzionalità di visualizzazione dei dati fornite dal servizio ed è possibile accedervi all'interno del proprio ambiente.

Azure Time Series Insights è un servizio completamente gestito per analisi, archiviazione e visualizzazione, che semplifica l'esplorazione e l'analisi di miliardi di eventi IoT contemporaneamente. Offre una visualizzazione globale dei dati e consente di convalidare rapidamente le soluzioni IoT e di evitare così costosi periodi di inattività per i dispositivi di importanza strategica. È possibile individuare tendenze nascoste e anomalie, nonché eseguire analisi della causa radice. Lo strumento di esplorazione di Time Series Insights è attualmente disponibile nella versione di anteprima pubblica.

> [!TIP]
> Per una presentazione guidata nell'ambiente dimostrativo, leggere la guida introduttiva di [Azure Time Series Insights.](time-series-quickstart.md)

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Informazioni sull'esecuzione di query sui dati tramite Esplora time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Riproduci il video precedente <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Introduzione a Time Series Insights usando Azure IoT Solution Accelerator".</a>

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare lo strumento di esplorazione di Time Series Insights, è necessario:

- Creare un ambiente Time Series Insights Per altre informazioni, vedere [Come iniziare a usare Time Series Insights](./time-series-insights-get-started.md).
- [Fornire l'accesso](time-series-insights-data-access.md) all'account nell'ambiente.
- Aggiungere un [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) o un'origine eventi [dell'hub eventi.](time-series-insights-how-to-add-an-event-source-eventhub.md)

## <a name="explore-and-query-data"></a>Esplorare ed eseguire query sui dati

Dopo aver connesso l'origine evento all'ambiente Time Series Insights, è possibile esplorare ed eseguire query sui dati di serie temporali.

1. Per iniziare, apri Esplora [Time Series Insights](https://insights.timeseries.azure.com/) nel Web browser. Sul lato sinistro della finestra, selezionare un ambiente. Tutti gli ambiente a cui l'utente può accedere sono elencati in ordine alfabetico.

1. Dopo aver selezionato un ambiente, utilizzare le configurazioni **Da** e **A** nella parte superiore oppure selezionare e trascinare sull'intervallo di tempo desiderato. Selezionare la lente di ingrandimento nell'angolo superiore destro oppure fare clic con il pulsante destro del mouse sull'intervallo di tempo selezionato e scegliere **Cerca**.

1. È inoltre possibile aggiornare automaticamente la disponibilità ogni minuto selezionando il pulsante **Auto On.** Il pulsante **Auto Attivato** si applica solo al grafico di disponibilità, non al contenuto della visualizzazione principale.

1. L'icona del cloud di Azure consente di accedere all'ambiente nel portale di Azure.The Azure cloud icon takes you to your environment in the Azure portal.

   [![Selezione dell'ambiente Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Successivamente, viene visualizzato un grafico che mostra un conteggio di tutti gli eventi durante l'intervallo di tempo selezionato. Sono disponibili i controlli seguenti:

    - **Pannello Editor termini**: Il termine spazio è dove si esegue una query nell'ambiente. Si trova sul lato sinistro dello schermo:
      - **MEASURE**: Questo elenco a discesa mostra tutte le colonne numeriche (**Doubles**).
      - **SPLIT BY**: questo elenco a discesa mostra le colonne categoriche (**Stringhe**).
      - È possibile attivare l'interpolazione passo, mostrare minimo e massimo e regolare l'asse y dal pannello di controllo accanto a **MEASURE**. È inoltre possibile regolare se i dati visualizzati sono un conteggio, una media o una somma dei dati.
      - È possibile aggiungere fino a cinque termini per visualizzare sullo stesso asse x. Selezionare **Aggiungi** per aggiungere un termine nuovo o usare il pulsante **Clona questo termine** per aggiungere una copia di un termine esistente.

        [![Riquadro Selezione, filtro e query dei termini](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicato**: usare il predicato per filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando o facendo clic, il predicato viene aggiornato automaticamente in base a tale ricerca. I tipi di operandi supportati includono:

         |Operazione  |Tipi supportati  |Note  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Doppio**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **Stringa**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**Pollici**     | **Stringa**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Tutti gli operandi devono essere dello stesso tipo o essere costanti **NULL.**        |
         |**HAS**     | **Stringa**        |  Solo valori letterali stringa costante sono consentiti sul lato destro. Stringa vuota e **NULL** non sono consentiti.       |

      - **Query di esempio**

         [![Query GA di esempio](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. È possibile utilizzare lo strumento di scorrimento **Dimensione intervallo** per ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Il dispositivo di scorrimento fornisce un controllo più preciso del movimento tra grandi sezioni di tempo che mostrano tendenze uniformi fino a fette piccole come il millisecondo, che consentono di visualizzare e analizzare tagli granulari e ad alta risoluzione dei dati. Il punto di partenza predefinito del dispositivo di scorrimento viene impostato come la visualizzazione ottimale dei dati della selezione per bilanciare risoluzione, velocità di query e granularità.

1. Lo strumento **pennello Tempo** semplifica la navigazione da un intervallo di tempo all'altro.

1. Selezionare l'icona **Salva** per salvare la query corrente e condividerla con altri utenti dell'ambiente. Quando si seleziona l'icona **Apri,** è possibile esaminare tutte le query salvate e tutte le query condivise di altri utenti in ambienti a cui si ha accesso.

   [![Query](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualizzare i dati

1. Utilizzare lo strumento **Vista prospettica** per una visualizzazione simultanea di un massimo di quattro query univoche. Il pulsante **Vista prospettica** si trova nell'angolo superiore destro del grafico.

   [![Selezionare le query da aggiungere al riquadro della prospettiva](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Visualizzare un grafico per esplorare visivamente i dati e usare gli strumenti **Grafico:**

    - **Selezionare** o **fare clic su** un intervallo di tempo specifico o su una singola serie di dati.
    - All'interno di una selezione dell'intervallo di tempo, è possibile ingrandire o esplorare gli eventi.
    - Selezione all'interno di una serie di dati: è possibile suddividere la serie in base a una colonna diversa, aggiungere la serie sotto forma di nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - Nell'area filtro a sinistra del grafico, è possibile esaminare tutte le serie di dati visualizzate e riordinare in base al valore o al nome. È inoltre possibile visualizzare tutte le serie di dati o qualsiasi serie bloccata o sbloccata. È possibile selezionare una singola serie di dati e dividere la serie in base a un'altra colonna, aggiungere la serie come nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, aggiungere la serie o esplorare gli eventi della serie selezionata.
    - Quando si visualizzano più termini contemporaneamente, è possibile impilare, annullare l'impilamento, esaminare dati aggiuntivi su una serie di dati e utilizzare lo stesso asse y in tutti i termini. Utilizzare i pulsanti nell'angolo superiore destro del grafico.

    [![Impostazioni delle opzioni dell'angolo superiore destro dello strumento Grafico](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Utilizzare la **mappa** termica per individuare rapidamente serie di dati univoche o anomale in una determinata query. È possibile visualizzare come mappa termica solo un termine di ricerca.

    [![GA explorer heatmap charting](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Quando si esplorano gli eventi selezionando o facendo clic con il pulsante destro del mouse, viene reso disponibile il pannello **EVENTI.** Qui puoi esaminare tutti gli eventi non elaborati ed esportare gli eventi come file JSON o CSV. Time Series Insights archivia tutti i dati non elaborati.

    [![Eventi](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Seleziona la scheda **STATS** dopo aver esplorato gli eventi per esporre i modelli e le statistiche delle colonne.

    - **Modelli**: Questa funzionalità espone in modo proattivo i modelli statisticamente più significativi in un'area dati selezionata. Non è necessario guardare migliaia di eventi per capire quali modelli richiedono più tempo ed energia. Con Time Series Insights, è possibile passare direttamente a questi modelli statisticamente significativi per continuare a condurre un'analisi. Questa funzionalità risulta utile anche per le analisi retrospettive di dati storici.
    - **Statistiche colonne:** le statistiche delle colonne forniscono grafici e tabelle che suddividono i dati di ogni colonna della serie di dati selezionata nell'intervallo di tempo selezionato.

      [![Opzioni e istogrammi STATS](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Ora hai appreso quali sono le funzionalità chiave, le impostazioni di configurazione e le opzioni di visualizzazione disponibili nell'app Web di Explorer Time Series Insights.

## <a name="next-steps"></a>Passaggi successivi

- Scopri come [diagnosticare e risolvere i problemi](time-series-insights-diagnose-and-solve-problems.md) nell'ambiente Time Series Insights.

- Partecipa alla presentazione guidata di [Azure Time Series Insights.](time-series-quickstart.md)
