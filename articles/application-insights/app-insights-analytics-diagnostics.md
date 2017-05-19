---
title: Diagnostica intelligente di modifiche delle prestazioni delle app Web in Azure Application Insights | Microsoft Docs
description: Diagnosi automatica di modifiche improvvise nella telemetria delle prestazioni da parte di un&quot;app web.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bb836357af6d006c18db578164f02fa7bd043b45
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnosticare modifiche improvvise nella telemetria dell'app

*Questa funzionalità è in anteprima.*

Diagnosticare modifiche improvvise delle prestazioni e dell'uso dell'app Web con un solo clic. La funzionalità di diagnostica intelligente è disponibile quando si crea un grafico del tempo in [Analytics](app-insights-analytics.md) di [Application Insights](app-insights-overview.md). Quando si verifica un cambio insolito nella tendenza dei risultati, ad esempio un aumento o un calo di valori, la diagnostica intelligente identifica un modello di dimensioni e di valori correlati che potrebbero illustrare la modifica. Ciò consente di diagnosticare rapidamente il problema. 

In questo esempio, la diagnostica intelligente ha identificato un modello di valori di proprietà associato alla modifica e ha evidenziato la differenza tra i risultati con e senza il modello:

![Risultato di esempio della diagnostica di Analytics](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnosticare le modifiche ai dati

1.    Eseguire una query in Analytics e creare un grafico del tempo. 
2.    Fare clic su qualsiasi punto di picco evidenziato, se presente.
 
    ![Punto di picco](./media/app-insights-analytics-diagnostics/peak.png)

    La diagnostica impiega pochi secondi per individuare un modello.

3. La scheda con i risultati della diagnostica visualizza un modello che potrebbe spiegare le discontinuità dei dati.

    ![risultato](./media/app-insights-analytics-diagnostics/result.png)
 
    Il testo indica i valori di dimensione che sembrano essere in correlazione con lo spostamento. In questo esempio, lo spostamento è associato a una determinata richiesta e a una versione particolare del browser.

    Si noti anche la presenza di due componenti del grafico, con i filtri true e false. Il componente false visualizza una tendenza invariata. In altre parole, non c'è alcuna modifica nei risultati della telemetria, se si esclude la combinazione problematica di dimensioni identificate dalla diagnostica. Al contrario, i risultati in tale combinazione visualizzano modifiche notevoli all'interno dell'area di indagine evidenziata. Questo indica che la diagnostica ha trovato una combinazione di proprietà che spiega il cambio.

4.    Se il modello è complesso, è necessario passare il mouse su **Mostra tutto** per visualizzare le dimensioni.

    ![Mostra tutto](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.    In caso in cui la diagnostica non trovi alcun modello significativo da notificare, verrà visualizzata la pagina "Nessun risultato". A questo punto, è possibile modificare la query. Ad esempio, è possibile restringere l'intervallo di tempo creando contenitori nella query di Analytics, per un'analisi più dettagliata e possibilmente per ottenere risultati migliori.

Se si sa che una determinata pagina del sito Web ha un problema in un determinato browser, è possibile ora passare direttamente alla pagina con il problema ed eseguire un'indagine sui cambi recenti.

## <a name="try-the-demo"></a>Provare la demo

[Fare clic qui per visualizzare una dimostrazione](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) con dati di esempio.

## <a name="how-it-works"></a>Funzionamento

La diagnostica intelligente usa un algoritmo di Machine Learning avanzato senza supervisione nell'operazione [DiffPatterns](app-insights-analytics-reference.md#evaluate-diffpatterns). Esegue una ricerca di modelli candidati che potrebbero spiegare la modifica dei dati. Analizza l'impatto di ciascun modello candidato sulla metrica e visualizza il modello che ha la migliore correlazione con la modifica.

## <a name="no-diagnostic-points"></a>Nessun punto di diagnostica?

La diagnostica intelligente funziona solo quando vengono soddisfatti i criteri seguenti:

 * L'impostazione Smart Diagnostics è attivata. Verificare l'icona Impostazioni in Analytics.
 * L'opzione Smart Diagnostics delle impostazioni di Analytics è selezionata. 
 * Asse del tempo: l'asse x del grafico deve essere di tipo `datetime`.
 * Grafico a linee o ad area: la diagnostica funziona solo con questi tipi di grafico. Usare `| render timechart` o `| render areachart` alla fine della query oppure selezionare un grafico a linee o ad area nel selettore a discesa.
 * Discontinuità: ci deve essere una discontinuità significativa nei dati.
 * Punti sufficienti da analizzare.
 * Non più di una clausola di riepilogo nella query.
 * Nessuna clausola di progetto che contiene una definizione di nome prima della clausola di riepilogo.

 
 ## <a name="related-articles"></a>Articoli correlati

 * [Esercitazione su Analisi](app-insights-analytics-tour.md)
 * Il [rilevamento intelligente](app-insights-proactive-diagnostics.md) avvisa automaticamente se ci sono problemi di prestazioni.
