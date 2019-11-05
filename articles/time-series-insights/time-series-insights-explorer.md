---
title: Esplorare i dati tramite Esplora Azure Time Series Insights | Microsoft Docs
description: In questo articolo viene descritto come usare lo strumento di esplorazione di Azure Time Series Insights nel Web browser per avere in modo semplice e rapido una visualizzazione globale dei Big Data e convalidare l'ambiente IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 25f2c07678217b69699b881d53ee9d5f92f84be1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990115"
---
# <a name="azure-time-series-insights-explorer"></a>Strumento di esplorazione di Azure Time Series Insights

Questo articolo descrive le funzionalità e le opzioni disponibili a livello generale per l' [app Web](https://insights.timeseries.azure.com/)di Azure Time Series Insights Explorer. Esplora Time Series Insights illustra le potenti funzionalità di visualizzazione dei dati fornite dal servizio ed è possibile accedervi all'interno dell'ambiente.

Azure Time Series Insights è un servizio completamente gestito per analisi, archiviazione e visualizzazione, che semplifica l'esplorazione e l'analisi di miliardi di eventi IoT contemporaneamente. Offre una visualizzazione globale dei dati e consente di convalidare rapidamente le soluzioni IoT e di evitare così costosi periodi di inattività per i dispositivi di importanza strategica. È possibile individuare tendenze nascoste e anomalie, nonché eseguire analisi della causa radice. Lo strumento di esplorazione di Time Series Insights è attualmente disponibile nella versione di anteprima pubblica.

> [!TIP]
> Per una presentazione guidata dell'ambiente demo, vedere la [Guida introduttiva Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Informazioni sull'esecuzione di query sui dati tramite Esplora Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Vedere il video precedente <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Introduzione a Time Series Insights usando un acceleratore di soluzioni di Azure."</a>

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare lo strumento di esplorazione di Time Series Insights, è necessario:

- Creare un ambiente Time Series Insights Per ulteriori informazioni, vedere [come iniziare a usare Time Series Insights](./time-series-insights-get-started.md).
- [Fornire l'accesso](time-series-insights-data-access.md) all'account nell'ambiente.
- Aggiungere un' [origine eventi dell'hub](time-series-insights-how-to-add-an-event-source-iothub.md) eventi o dell' [Hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) .

## <a name="explore-and-query-data"></a>Esplorare ed eseguire query sui dati

Dopo aver connesso l'origine evento all'ambiente Time Series Insights, è possibile esplorare ed eseguire query sui dati di serie temporali.

1. Per iniziare, aprire [esplora Time Series Insights](https://insights.timeseries.azure.com/) nel Web browser. Sul lato sinistro della finestra selezionare un ambiente. Tutti gli ambiente a cui l'utente può accedere sono elencati in ordine alfabetico.

1. Dopo aver selezionato un ambiente, usare le configurazioni **da** e **a** nella parte superiore oppure fare clic e trascinare l'intervallo di tempo desiderato. Selezionare la lente di ingrandimento nell'angolo superiore destro oppure fare clic con il pulsante destro del mouse sull'intervallo di tempo selezionato e scegliere **Cerca**.

1. È anche possibile aggiornare automaticamente la disponibilità ogni minuto selezionando il pulsante **auto on (automatico** ). Il pulsante **auto on** si applica solo al grafico di disponibilità, non al contenuto della visualizzazione principale.

1. L'icona del cloud di Azure consente di portarsi all'ambiente nella portale di Azure.

   [ambiente Time Series Insights ![](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Nel grafico visualizzato è riportato il conteggio di tutti gli eventi durante l'intervallo di tempo selezionato. Sono disponibili i controlli seguenti:

    - **Pannello dei termini dell'editor**: il termine spazio è il punto in cui viene eseguita una query sull'ambiente. Si trova sul lato sinistro della schermata:
      - **Measure**: questo elenco a discesa Mostra tutte le colonne numeriche (**Double**).
      - **Split by**: questo elenco a discesa Mostra le colonne categoriche (**stringhe**).
      - È possibile abilitare l'interpolazione dei passaggi, visualizzare il valore minimo e massimo e regolare l'asse y dal pannello di controllo accanto a **misura**. È anche possibile regolare se i dati visualizzati sono un conteggio, una media o una somma dei dati.
      - È possibile aggiungere fino a cinque termini per visualizzare sullo stesso asse x. Usare il pulsante **copia giù** per aggiungere un termine aggiuntivo oppure selezionare **Aggiungi** per aggiungere un nuovo termine.

        [pannello Editor termini ![](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predicato**: usare il predicato per filtrare rapidamente gli eventi usando il set di operandi elencati nella tabella seguente. Se si esegue una ricerca selezionando o facendo clic su, il predicato viene aggiornato automaticamente in base a tale ricerca. I tipi di operandi supportati includono:

         |Operazione  |Tipi supportati  |Note  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL.        |
         |HAS     | String        |  Sul lato destro sono consentiti solo valori letterali stringa costanti. Non sono consentite stringhe vuote e NULL.       |

      - **Esempi di query**

         [query di esempio ![](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. È possibile utilizzare lo strumento Slider **Dimensioni intervallo** per ingrandire e ridurre gli intervalli sullo stesso intervallo di tempo. Il dispositivo di scorrimento fornisce un controllo più preciso dello spostamento tra le vaste sezioni di tempo che mostrano tendenze uniformi fino a sezioni fino al millisecondo, che consentono di visualizzare tagli granulari e a risoluzione elevata dei dati. Il punto di partenza predefinito del dispositivo di scorrimento è impostato come visualizzazione ottimale dei dati della selezione per bilanciare la risoluzione, la velocità delle query e la granularità.

1. Lo strumento **pennello ora** semplifica la navigazione da un intervallo di tempo a un altro.

1. Usare il comando **Salva** per salvare la query corrente e condividerla con altri utenti dell'ambiente. Quando si usa **Apri**, è possibile visualizzare tutte le query salvate e tutte le query condivise di altri utenti negli ambienti a cui si ha accesso.

   [Query di ![](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualizzare i dati

1. Utilizzare lo strumento **visualizzazione prospettiva** per una visualizzazione simultanea di un massimo di quattro query univoche. Il pulsante **visualizzazione prospettiva** si trova nell'angolo superiore destro del grafico.

   [visualizzazione ![prospettiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Visualizzazione di un grafico per esplorare visivamente i dati e utilizzare gli strumenti per i **grafici** :

    - **Selezionare** o **fare clic su** un intervallo di tempo specifico o su una singola serie di dati.
    - All'interno di una selezione TimeSpan è possibile ingrandire o esplorare gli eventi.
    - Selezione all'interno di una serie di dati: è possibile suddividere la serie in base a una colonna diversa, aggiungere la serie sotto forma di nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - Nell'area del filtro a sinistra del grafico è possibile visualizzare tutte le serie di dati visualizzate e riordinarle in base al valore o al nome. È anche possibile visualizzare tutte le serie di dati o qualsiasi serie bloccata o sbloccata. È possibile selezionare una singola serie di dati e suddividere le serie in base a un'altra colonna, aggiungere la serie come nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, aggiungere tale serie o esplorare gli eventi della serie selezionata.
    - Quando si visualizzano più termini simultaneamente, è possibile stack, unstack, visualizzare dati aggiuntivi relativi a una serie di dati e usare lo stesso asse y in tutti i termini. Utilizzare i pulsanti nell'angolo superiore destro del grafico.

    [strumento grafico ![](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Usare **mappa termica** per individuare in modo rapido la serie di dati univoca o anomala in una determinata query. È possibile visualizzare come mappa termica solo un termine di ricerca.

    [![mappa termica](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Quando si esplorano gli eventi selezionando o facendo clic con il pulsante destro del mouse, viene reso disponibile il pannello **eventi** . In questa area è possibile visualizzare tutti gli eventi non elaborati ed esportare gli eventi come file con estensione json o csv. Time Series Insights archivia tutti i dati non elaborati.

    [Eventi ![](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Selezionare la scheda **statistiche** dopo aver esplorato gli eventi per esporre i modelli e le statistiche di colonna.

    - **Modelli**: questa funzionalità consente di ripartire in modo proattivo i modelli più statistici significativi in un'area dati selezionata. Non è necessario esaminare migliaia di eventi per comprendere quali modelli richiedono più tempo ed energia. Con Time Series Insights, è possibile passare direttamente a questi modelli statisticamente significativi per continuare a eseguire un'analisi. Questa funzionalità risulta utile anche per le analisi retrospettive di dati storici.
    - **Column stats**: le statistiche di colonna forniscono grafici e tabelle che suddividono i dati da ogni colonna della serie di dati selezionata nell'intervallo di tempo selezionato.

      [Statistiche ![](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Sono ora disponibili le varie funzionalità e opzioni disponibili nell'app Web di Time Series Insights Explorer.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [diagnosticare e risolvere i problemi](time-series-insights-diagnose-and-solve-problems.md) nell'ambiente Time Series Insights.

- Visitare la Guida [introduttiva Azure Time Series Insights Guida introduttiva](time-series-quickstart.md) .
