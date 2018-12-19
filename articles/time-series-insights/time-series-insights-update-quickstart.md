---
title: "Avvio rapido: Esplorare l'ambiente demo Anteprima di Azure Time Series Insights | Microsoft Docs"
description: Informazioni sull'ambiente demo Anteprima di Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276833"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Avvio rapido: Esplorare l'ambiente demo Anteprima di Azure Time Series Insights

Questo argomento di avvio rapido illustra come usare lo strumento di esplorazione Anteprima di Azure Time Series Insights in un ambiente di dimostrazione gratuito. Si imparerà a usare il Web browser per visualizzare grandi volumi di dati IoT industriali cronologici e verranno esaminate le funzionalità principali dello strumento di esplorazione Anteprima di Time Series Insights.

Time Series Insights offre una piattaforma end-to-end distribuita come servizio (PaaS, Platform as a Service). Può inserire, elaborare, archiviare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per la serie temporale per consentire l’analisi di dati improvvisati. Fornisce inoltre un'analisi operativa. Time Series Insights è una soluzione differenziata progettata su misura per soddisfare le esigenze specifiche di distribuzioni IoT industriali.

Nell'ambiente dimostrativo viene illustrata una società di generazione dell'energia elettrica, Contoso. Nell'ambiente, si utilizza Time Series Insights per trovare informazioni operative dettagliate nei dati di Contoso e condurre una breve analisi delle principali cause. Contoso dispone di due centrali con turbine eoliche, ciascuna con 10 turbine eoliche. Ogni turbina è dotata di 20 sensori che inviano dati ogni minuto all’hub IoT di Azure. I sensori raccolgono informazioni su condizioni meteo, inclinazione delle pale e posizione di imbardata, prestazioni del generatore, comportamento della trasmissione e monitoraggi di sicurezza.

Si utilizza Anteprima di Time Series Insights per analizzare i dati degli ultimi due anni del crescente set dati di Contoso, che al momento ha raggiunto 40 GB. Può agevolare la comprensione e la previsione dei guasti critici e dei problemi che rallentano la manutenzione.

Se non si ha una sottoscrizione di Azure, creare un  [account di Azure gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  prima di iniziare.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Esaminare lo strumento di esplorazione di Time Series Insights in un ambiente demo

1. Nel browser, accedere all’ [ambiente della centrale eolica Contoso](https://insights.timeseries.azure.com/preview/samples).  

1. Se viene richiesto, accedere allo strumento di esplorazione di Time Series Insights usando le credenziali dell'account Azure.

### <a name="demo-step-1"></a>Demo - passaggio 1

1. Esaminare la turbina eolica **W7** nell’**impianto 1 di Contoso**.  

    * **Azione**: Aggiornare l’intervallo di visualizzazione **1/1/17 20:00 a 3/10/17 20:00 (UTC)**, aggiungere il sensore **GeneratorSpeed** > **del sistema di generazione** > **W7** > **nell’impianto 1 di Contoso**, quindi visualizzare i risultati.

       ![Avvio rapido - passaggio 1][1]

1. Di recente, Contoso ha riscontrato un incendio nella turbina eolica **W7**. Esaminare in dettaglio questi dati. È possibile vedere che il sensore dell'allarme antincendio si è attivato durante l'incendio.

    * **Azione**: Aggionrare l’intervallo di visualizzazione **3/9/17 20:00 su 3/10/17 20:00 (UTC)** e aggiungere il sensore **FireAlert** > **del sistema di sicurezza**.

      ![Avvio rapido - passaggio 2][2]

1. Esaminare cosa altro è successo intorno al momento dell'incendio. Sia la pressione dell'olio che gli avvisi attivi hanno raggiunto valori di picco subito prima di incendio, ma a quel punto era troppo tardi per evitare il problema.

    * **Azione**: Aggiungere il **sensore**  > **HydraulicOilPressure** e il sensore **ActiveWarning** > **del sistema di passo**.

      ![Avvio rapido - passaggio 3][3]

1. Facendo zoom indietro, è possibile notare la presenza di segnali che conducono all'incendio. Per entrambi i sensori si sono verificate fluttuazioni. Questo problema si è quindi verificato in precedenza?

    * **Azione**: Aggiornare l’intervallo di visualizzazione **2/24/17 20:00 su 3/10/17 20:00 (UTC)**.

      ![Avvio rapido - passaggio 4][4]

1. Esaminando due interi anni di dati, è possibile notare un evento di incendio precedente con gli stessi segnali. Con questi dati è possibile creare sistemi per rilevare tempestivamente problemi simili.

    * **Azione**: Aggiornare l’intervallo di visualizzazione **1/1/16 su 12/31/17** (tutti i dati).

       ![Avvio rapido - passaggio 5][5]

### <a name="demo-step-2"></a>Demo - passaggio 2

1. Altri problemi sono più complessi e più difficili da diagnosticare. Time Series Insights offre una gamma di modalità che aiutano a monitorare i problemi complessi. In questo caso è possibile vedere un’interruzione del sensore di avviso su **W6** in **6/25**. Ma cosa succede effettivamente?

    * **Azione**: Rimuovere i sensori di corrente, aggiornare l’intervallo di visualizzazione **6/1/17 20:00 su 7/1/17 20:00 (UTC)**, quindi aggiungere il sensore **VoltageActuatorSwitchWarning** > **del sistema di sicurezza** > **W6** > **dell’impianto Contoso 1**.

       ![Avvio rapido - passaggio 6][6]

1. L'avviso indica un problema della tensione in uscita dal generatore. Qual è la causa? La potenzia di uscita complessiva del generatore sembra corretta se analizzata in base a un intervallo granulare. Aggregando i dati, tuttavia, è possibile notare un netto calo.

    * **Azione**: Rimuovere il sensore **VoltageActuatorSwitchWarning**, aggiungere il sensore **ActivePower** > **del sistema di generazione**, quindi aggiornare l’intervallo su **3d**.

       ![Avvio rapido - passaggio 7][7]

1. Spostandosi in avanti nel set di dati, è possibile vedere che non si tratta di un problema temporaneo. Continua a verificarsi.

    * **Azione**: Estendere l'intervallo di tempo verso destra.

       ![Avvio rapido - passaggio 8][8]

1. Esaminare i dati più in dettaglio. È possibile aggiungere altri punti dati dei sensori per visualizzare la tensione in base alla fase. Tuttavia, tutti i punti dati sono confrontabili. Inserire un indicatore per visualizzare i valori effettivi. Sembra che sia presente un problema con l'output della fase 3.

    * **Azione**: Aggiungere i **sensori**  > **GridVoltagePhase1**, **GridVoltagePhase2** e **GridVoltagePhase3**. Inserire un indicatore nell'ultimo punto dati nell'area visibile.

       ![Avvio rapido - passaggio 8][8]

1. Se si visualizzano tutti e tre i punti dati con la stessa scala, il calo della fase 3 appare ancora più evidente. A questo punto, è possibile segnalare il problema al team di manutenzione con un buon indizio sulle cause dell'avviso.  

    * **Azione**: Aggiornare la visualizzazione per sovrapporre tutti i sensori sulla stessa scala del grafico.

       ![Avvio rapido - passaggio 9][9]

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti a creare l'ambiente Anteprima di Time Series Insights:

> [!div class="nextstepaction"]
> [Pianificare l’ambiente Anteprima di Time Series Insights](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
