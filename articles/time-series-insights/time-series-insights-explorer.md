---
title: Esplorare i dati usando Azure Time Series Insights explorer | Microsoft Docs
description: In questo articolo viene descritto come usare lo strumento di esplorazione di Azure Time Series Insights nel Web browser per avere in modo semplice e rapido una visualizzazione globale dei Big Data e convalidare l'ambiente IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: cbb6d75b2ed6b73ce7e3421596520f6a3f1bf3b1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399812"
---
# <a name="azure-time-series-insights-explorer"></a>Strumento di esplorazione di Azure Time Series Insights

Questo articolo vengono descritte le funzionalità e le opzioni in generale la disponibilità di Azure Time Series Insights [app web di Esplora](https://insights.timeseries.azure.com/). L'esplorazione di Time Series Insights vengono illustrate le funzionalità di visualizzazione avanzata dei dati fornite dal servizio ed è accessibile all'interno del proprio ambiente.

Azure Time Series Insights è un servizio completamente gestito per analisi, archiviazione e visualizzazione, che semplifica l'esplorazione e l'analisi di miliardi di eventi IoT contemporaneamente. Offre una visualizzazione globale dei dati e consente di convalidare rapidamente le soluzioni IoT e di evitare così costosi periodi di inattività per i dispositivi di importanza strategica. È possibile individuare tendenze nascoste e anomalie, nonché eseguire analisi della causa radice. Lo strumento di esplorazione di Time Series Insights è attualmente disponibile nella versione di anteprima pubblica.

> [!TIP]
> Per una panoramica guidata l'ambiente dimostrativo, vedere la [Guida introduttiva di Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Informazioni sulle query sui dati usando l'esplorazione di Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Vedere il video precedente <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Getting started with Time Series Insights tramite un acceleratore di soluzione IoT di Azure."</a>

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare lo strumento di esplorazione di Time Series Insights, è necessario:

- Creare un ambiente Time Series Insights Per altre informazioni, vedere [come iniziare a usare Time Series Insights](./time-series-insights-get-started.md).
- [Fornire l'accesso](time-series-insights-data-access.md) al proprio account nell'ambiente.
- Aggiungere un [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) oppure [dell'hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) origine evento a esso.

## <a name="explore-and-query-data"></a>Esplorare ed eseguire query sui dati

Dopo aver connesso l'origine evento all'ambiente Time Series Insights, è possibile esplorare ed eseguire query sui dati di serie temporali.

1. Per iniziare, aprire il [Time Series Insights explorer](https://insights.timeseries.azure.com/) nel web browser. Sul lato sinistro della finestra, selezionare un ambiente. Tutti gli ambiente a cui l'utente può accedere sono elencati in ordine alfabetico.

1. Dopo aver selezionato un ambiente, usare il **dal** e **a** configurazioni nella parte superiore, oppure fare clic e trascinamento sull'intervallo di tempo desiderato. Selezionare la lente di ingrandimento nell'angolo superiore destro o destro del mouse sul periodo di tempo selezionato e selezionare **ricerca**.

1. È anche possibile aggiornare la disponibilità automaticamente ogni minuto selezionando il **automatico sul** pulsante. Il **automatico sul** pulsante si applica solo al grafico di disponibilità, non il contenuto della visualizzazione principale.

1. L'icona di cloud di Azure consente di passare all'ambiente nel portale di Azure.

   [![Ambiente Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Nel grafico visualizzato è riportato il conteggio di tutti gli eventi durante l'intervallo di tempo selezionato. Sono disponibili i controlli seguenti:

    - **Pannello dell'Editor di condizioni**: in questo pannello vengono eseguite le query sull'ambiente. Si trova sul lato sinistro dello schermo:
      - **Misura**: Questo elenco di riepilogo a discesa Mostra tutte le colonne numeriche (**doppi**).
      - **Dividi per**: Questo elenco di riepilogo a discesa Mostra le colonne categoriche (**stringhe**).
      - È possibile abilitare l'interpolazione graduale, visualizzare minimo e massimo e adattare l'asse y dal Pannello di controllo accanto a **misure**. È anche possibile modificare sia i dati visualizzati un conteggio, Media o totale dei dati.
      - È possibile aggiungere fino a cinque termini da visualizzare sull'asse x. Usare la **copia verso il basso** pulsante per aggiungere un altro termine oppure scegliere **Add** per aggiungere un nuovo termine.

        [![Pannello dell'Editor di condizioni](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predicato**: Usare il predicato per filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca tramite selezione o facendo clic su, il predicato automaticamente gli aggiornamenti in base la ricerca. I tipi di operandi supportati includono:

         |Operazione  |Tipi supportati  |Note  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL.        |
         |HAS     | String        |  Sono consentiti solo valori letterali di stringa costante sul lato destro. Non sono consentiti valori NULL o stringhe vuote.       |

      - **Esempi di query**

         [![Query di esempio](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. È possibile usare la **dimensioni intervallo** dispositivo di scorrimento per ingrandire o ridurre gli intervalli di failover nello stesso intervallo. Il dispositivo di scorrimento fornisce un controllo più preciso dello spostamento tra intervalli di tempo che mostrano le tendenze smooth a sezioni di piccoli fino al millisecondo, che consentono di vedere tagli ad alta risoluzione, granulari dei dati. Punto di partenza predefinito del dispositivo di scorrimento sia impostato come la visualizzazione ottimale dei dati dalla selezione a bilanciare la risoluzione, velocità delle query e granularità.

1. Il **ora pennello** lo strumento consente di passare da un intervallo di tempo a altra.

1. Usare la **salvare** comando per salvare la query corrente e condividerlo con altri utenti dell'ambiente. Quando si usa **aperto**, è possibile visualizzare tutte le query salvate e qualsiasi query condivisa di altri utenti dell'ambiente è possibile utilizzare.

   [![Query](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualizzare i dati

1. Usare la **visualizzazione prospettiva** strumento per una visualizzazione simultanea di un massimo di quattro query univoche. Il **visualizzazione prospettiva** pulsante si trova nell'angolo superiore destro del grafico.

   [![Visualizzazione prospettiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Visualizzare un grafico per esplorare i dati visivamente e usare la **grafico** strumenti:

    - **Selezionare** oppure **fare clic su** un intervallo di tempo specifico o una serie di dati.
    - All'interno di una selezione intervallo di tempo, è possibile eseguire lo zoom o esplorare gli eventi.
    - Selezione all'interno di una serie di dati: è possibile suddividere la serie in base a una colonna diversa, aggiungere la serie sotto forma di nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - Nell'area filtro a sinistra del grafico, è possibile visualizzare tutte le serie di dati visualizzati e riordinare i base al valore o nome. È anche possibile visualizzare tutti i dati delle serie o qualsiasi bloccate o sbloccate. È possibile selezionare una serie di dati e suddividerla in base a un'altra colonna, aggiungere la serie come nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - Quando si visualizzano più termini contemporaneamente, è possibile dello stack, separa i, visualizzare dati aggiuntivi su una serie di dati e usare lo stesso asse y in tutti i termini. Usare i pulsanti nell'angolo superiore destro del grafico.

    [![Strumento grafico](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Usare la **mappa termica** individuare rapidamente serie di dati univoci o anomali in una determinata query. È possibile visualizzare come mappa termica solo un termine di ricerca.

    [![Mappa termica](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Quando si Esplora eventi selezionando o facendo clic su, il **eventi** pannello viene reso disponibile. In questa area è possibile visualizzare tutti gli eventi non elaborati ed esportare gli eventi come file con estensione json o csv. Time Series Insights archivia tutti i dati non elaborati.

    [![Eventi](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Selezionare il **STATS** scheda dopo avere esplorato gli eventi per visualizzare i modelli e statistiche di colonna.

    - **I modelli**: Questa funzionalità evidenzia in modo proattivo i modelli statisticamente più significativi in un'area dati selezionata. Non è necessario esaminare migliaia di eventi per individuare i modelli che richiedono più tempo ed energia. Con Time Series Insights, è possibile passare direttamente a questi modelli per continuare l'esecuzione di un'analisi statisticamente significativi. Questa funzionalità risulta utile anche per le analisi retrospettive di dati storici.
    - **Statistiche di colonna**: Statistiche di colonna offrono i grafici e tabelle che suddividono i dati di ogni colonna della serie di dati selezionato l'intervallo di tempo selezionato.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Si è visto le varie funzionalità e le opzioni disponibili all'interno dell'app web di Time Series Insights explorer.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [diagnosticare e risolvere i problemi](time-series-insights-diagnose-and-solve-problems.md) nell'ambiente Time Series Insights.
- Richiedere il guidati [Guida introduttiva di Azure Time Series Insights](time-series-quickstart.md) tour.
