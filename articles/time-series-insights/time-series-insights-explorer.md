---
title: Esplorare i dati tramite lo strumento di esplorazione di Azure Time Series Insights | Microsoft Docs
description: In questo articolo viene descritto come usare lo strumento di esplorazione di Azure Time Series Insights nel Web browser per avere in modo semplice e rapido una visualizzazione globale dei Big Data e convalidare l'ambiente IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0f22a0245d002b94d9fc0004214c37944350e262
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413019"
---
# <a name="azure-time-series-insights-explorer"></a>Strumento di esplorazione di Azure Time Series Insights

Questo articolo vengono descritte le funzionalità e le opzioni in generale la disponibilità di Azure Time Series Insights [app web di Esplora](https://insights.timeseries.azure.com/). L'esplorazione di Time Series Insights vengono illustrate le funzionalità di visualizzazione avanzata dei dati fornite dal servizio ed è accessibile all'interno del proprio ambiente.

Azure Time Series Insights è un servizio completamente gestito per analisi, archiviazione e visualizzazione, che semplifica l'esplorazione e l'analisi di miliardi di eventi IoT contemporaneamente. Offre una visualizzazione globale dei dati e consente di convalidare rapidamente le soluzioni IoT e di evitare così costosi periodi di inattività per i dispositivi di importanza strategica. È possibile individuare tendenze nascoste e anomalie, nonché eseguire analisi della causa radice. Lo strumento di esplorazione di Time Series Insights è attualmente disponibile nella versione di anteprima pubblica.

> [!TIP]
> Per una panoramica guidata l'ambiente dimostrativo, vedere la [avvio rapido di Azure ora Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-using-the-time-series-insights-explorer-br"></a>Informazioni sulle query sui dati usando l'esplorazione di Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Vedere il video precedente <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Getting started with utilizzando un acceleratore di soluzione IoT di Azure Time Series Insights"</a>.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare lo strumento di esplorazione di Time Series Insights, è necessario:

- Creare un ambiente Time Series Insights Per altre informazioni, vedere [come iniziare a usare Time Series Insights](./time-series-insights-get-started.md).
- [Fornire l'accesso](time-series-insights-data-access.md) al proprio account nell'ambiente.
- Aggiungere un [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) oppure [dell'Hub eventi](time-series-insights-how-to-add-an-event-source-eventhub.md) origine evento a esso.

## <a name="explore-and-query-data"></a>Esplorare ed eseguire query sui dati

Dopo aver connesso l'origine evento all'ambiente Time Series Insights, è possibile esplorare ed eseguire query sui dati di serie temporali.

1. Per iniziare, aprire lo [strumento di esplorazione di Time Series Insights ](https://insights.timeseries.azure.com/) nel Web browser e selezionare un ambiente sul lato sinistro della finestra. Tutti gli ambiente a cui l'utente può accedere sono elencati in ordine alfabetico.

1. Dopo aver selezionato un ambiente, usare le configurazioni **FROM** (DA) e **TO** (A) disponibili nella parte superiore oppure fare clic e trascinare il puntatore del mouse sull'intervallo di tempo desiderato.  Fare clic sulla lente di ingrandimento in alto a destra o fare clic con il pulsante destro del mouse sull'intervallo di tempo selezionato e scegliere **Search** (Cerca).  

1. È anche possibile aggiornare automaticamente la disponibilità ogni minuto selezionando il pulsante **Auto On** (Abilita aggiornamento automatico). Il **accensione automatica** pulsante si applica solo al grafico di disponibilità, non il contenuto della visualizzazione principale.

1. L'icona del cloud di Azure consente di passare all'ambiente nel portale di Azure.

   [![Ambiente Time Series Insights](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Nel grafico visualizzato è riportato il conteggio di tutti gli eventi durante l'intervallo di tempo selezionato.  Sono disponibili i controlli seguenti:

    **Pannello Terms Editor** (Editor termini):  in questo pannello vengono eseguite le query sull'ambiente.  Si trova sul lato sinistro dello schermo:
      - **Misura**:  Questo elenco a discesa Mostra tutte le colonne numeriche (**doppi**)
      - **Dividi per**: Questo elenco a discesa Mostra le colonne categoriche (**stringhe**)
      - È possibile abilitare l'interpolazione graduale, visualizzare i valori massimi e minimi e adattare l'asse Y tramite il pannello di controllo accanto alla misura.  È anche possibile definire se i dati visualizzati sono un conteggio, una media oppure una somma di dati.
      - È possibile aggiungere fino a cinque termini da visualizzare sull'asse X.  Usare il pulsante per la **copia verso il basso** per aggiungere altri termini oppure fare clic sul pulsante **Add**  (Aggiungi) per aggiungere un nuovo termine.

        [![Pannello dell'Editor di condizioni](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predicato**:  il predicato consente di filtrare rapidamente gli eventi tramite il set di operandi elencati di seguito. Se si esegue una ricerca tramite selezione o clic, il predicato viene automaticamente aggiornato in base a tale ricerca. I tipi di operandi supportati includono:

         |Operazione  |Tipi supportati  |Note  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Tutti gli operandi devono essere dello stesso tipo o essere una costante NULL.        |
         |HAS     | String        |  Sono consentiti solo valori letterali di tipo stringa costante sul lato destro. Non sono consentiti valori NULL o stringhe vuote.       |

      - **Esempi di query**

         [![Query di esempio](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Il dispositivo di scorrimento **Interval Size** (Dimensione intervallo) consente di eseguire lo zoom avanti e indietro dei periodi inclusi nello stesso intervallo di tempo.  Questo strumento garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità, consentendo di visualizzare tendenze uniformi in sezioni temporali riferite a millisecondi e pertanto di mostrare sezioni di dati con granularità e risoluzione superiori. Il punto iniziale predefinito del dispositivo di scorrimento è impostato sulla visualizzazione ottimale dei dati basata sulla selezione effettuata, ovvero risoluzione del bilanciamento, velocità della query e granularità.

1. Lo strumento **Time brush** (Pennello tempo) semplifica il passaggio tra intervalli di tempo diversi grazie a un'esperienza utente intuitiva che garantisce spostamenti uniformi tra intervalli di tempo.

1. Il comando **Save** (Salva) consente di salvare la query corrente e abilitarla per la condivisione con altri utenti dell'ambiente. Usare il pulsante **Open** per visualizzare tutte le query salvate e qualsiasi query condivisa di altri utenti dell'ambiente a cui si ha accesso.

   [![Query](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualizzare i dati

1. Lo strumento **Perspective View** (Visualizzazione prospettiva) fornisce una visualizzazione simultanea di un massimo di quattro query univoche. Il pulsante Perspective View (Visualizzazione prospettiva) è disponibile nell'angolo superiore destro del grafico.  

   [![Visualizzazione prospettiva](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Lo strumento **Chart** (Grafico) consente di esplorare visivamente i dati. Lo strumento Chart (Grafico) include le funzionalità seguenti:

    - **Selezione/clic:**, che consente una selezione di un intervallo di tempo specifico o di una serie di dati.  
    - Selezione all'interno di un intervallo di tempo: è possibile eseguire lo zoom o esplorare gli eventi.  
    - Selezione all'interno di una serie di dati: è possibile suddividere la serie in base a una colonna diversa, aggiungere la serie sotto forma di nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - Nell'area filtro a sinistra del grafico, è possibile vedere tutte le serie di dati visualizzati e riordinare i base al valore o nome, consente di visualizzare tutti i dati delle serie o qualsiasi bloccate o sbloccate.  È anche possibile selezionare una singola serie di dati e suddividerla in base a una colonna diversa, aggiungere la serie sotto forma di nuovo termine, visualizzare solo la serie selezionata, escludere la serie selezionata, bloccare tale serie o esplorare gli eventi nella serie selezionata.
    - In caso di visualizzazione contemporanea di più termini, è possibile impilare, rimuovere la pila, visualizzare dati aggiuntivi su una serie di dati specifica e usare lo stesso asse Y in tutti i termini grazie ai pulsanti disponibili nell'angolo superiore destro del grafico.

    [![Strumento grafico](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Lo strumento **Heatmap** (Mappa termina) può essere usato per individuare rapidamente serie di dati univoci o anomali in una query specifica. È possibile visualizzare come mappa termica solo un termine di ricerca.

    [![Mappa termica](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. **Eventi**:  quando si sceglie di esplorare gli eventi mediante una selezione o un clic con il pulsante destro del mouse nell'area soprastante, viene visualizzato il riquadro degli eventi.  In questa area è possibile visualizzare tutti gli eventi non elaborati ed esportare gli eventi come file con estensione json o csv. Time Series Insights archivia tutti i dati non elaborati.

    [![Eventi](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Fare clic sulla scheda **STATS** (Statistiche) dopo aver esplorato gli eventi per visualizzare i modelli e le statistiche di colonna.  

    - **Patterns** (Motivi): questa funzionalità evidenzia in modo proattivo i modelli statisticamente più significativi all'interno di un'area dati selezionata. Ciò consente di evitare di dover analizzare migliaia di eventi per individuare i modelli che maggiormente richiedono tempo ed energia. Time Series Insights consente inoltre di passare direttamente ai modelli statisticamente più significativi individuati e continuare ad analizzare i dati. Questa funzionalità risulta utile anche per le analisi retrospettive di dati storici.

    - **Statistiche di colonna**:  in questa area sono visualizzati grafici e tabelle che suddividono i dati per ciascuna colonna della serie di dati selezionata nell'intervallo di tempo selezionato.  

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Sono state introdotte le varie funzionalità e opzioni disponibili nell'app Web dello strumento di esplorazione di Time Series Insights.

## <a name="next-steps"></a>Passaggi successivi

- Scopri [la diagnosi e risoluzione dei problemi](time-series-insights-diagnose-and-solve-problems.md) nell'ambiente Time Series Insights.

- Richiedere il guidati [avvio rapido di Azure ora Series Insights](time-series-quickstart.md) tour.
