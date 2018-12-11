---
title: Esplorare l'ambiente demo di Azure Time Series Insights (anteprima) | Microsoft Docs
description: Informazioni sull'ambiente demo di Azure Time Series Insights (anteprima)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888784"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Esplorare l'ambiente demo di Azure Time Series Insights (anteprima)

Questo argomento di avvio rapido illustra come iniziare a usare la versione di anteprima di Azure Time Series Insights (TSI) Explorer in un ambiente di dimostrazione gratuito. Si imparerà a usare il Web browser per visualizzare grandi volumi di dati IoT industriali cronologici e verranno esaminate le funzionalità principali della versione di anteprima di Azure Time Series Insights Explorer.

Azure Time Series Insights offre una soluzione PaaS (piattaforma distribuita come servizio) end-to-end che consente di inserire, elaborare, e archiviare dati IoT ottimizzati per le serie temporali e notevolmente contestualizzati ed eseguire query su tali dati per attività di esplorazione dei dati ad hoc e analisi operativa. Time Series Insights è una soluzione differenziata progettata su misura per soddisfare le esigenze specifiche di distribuzioni IoT industriali.

Questo ambiente demo illustra un'azienda produttrice di elettricità, Contoso, che usa Time Series Insights per trovare informazioni dettagliate di utilità pratica nei dati e svolgere analisi della causa radice. Contoso gestisce due centrali con turbine eoliche, ognuna con 10 turbine, e ogni turbina ha 20 sensori che segnalano i dati ad all'hub IoT di Azure ogni minuto. I sensori raccolgono informazioni su condizioni meteo, inclinazione delle pale e posizione di imbardata, prestazioni del generatore, comportamento della trasmissione e monitoraggi di sicurezza.

Time Series Insights (anteprima) viene usato per analizzare il set di dati in continua crescita degli ultimi due anni, attualmente con dimensioni di 40 GB, per comprendere meglio e prevedere sia gli errori critici che i problemi di manutenzione con risoluzione lenta.

Se non si ha una sottoscrizione di Azure, creare un  [account di Azure gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  prima di iniziare.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Esplorare lo strumento di esplorazione di Time Series Insights in un ambiente demo

1. Nel browser passare a  [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Se viene richiesto, accedere a Time Series Insights Explorer usando le credenziali dell'account di Azure.

### <a name="demo-step-one"></a>Demo - passaggio 1

1. Esaminare la **turbina eolica 7 nella centrale eolica 1**.  

    * **Azione**: aggiornare l'intervallo di visualizzazione a `1/1/17 20:00 – 3/10/17 20:00 (UTC)` e aggiungere il sensore `Farm 1 > W7 > Generator > GeneratorSpeed`. Visualizzare quindi i valori risultanti.

       ![Avvio rapido - passaggio 1][1]

1. Di recente **Contoso ha riscontrato un incendio nella turbina eolica 7**. Esaminare in dettaglio questi dati. È possibile vedere che il sensore dell'allarme antincendio si è attivato durante il periodo dell'incendio.

    * **Azione**: aggiornare l'intervallo di visualizzazione a `3/9/17 20:00 – 3/10/17 20:00 (UTC)` e aggiungere il sensore `Safety > FireAlert`.

      ![Avvio rapido - passaggio 2][2]

1. Esaminare cosa altro è successo intorno al momento dell'incendio. Sia la pressione dell'olio che gli avvisi attivi hanno raggiunto valori di picco subito prima di incendio, ma a quel punto era troppo tardi per evitare il problema.

    * **Azione**: aggiungere i sensori `Pitch > HydraulicOilPressure` e `Pitch > ActiveWarning`.

      ![Avvio rapido - passaggio 3][3]

1. Facendo zoom indietro, è possibile notare la presenza di segnali che conducono all'incendio. Per entrambi i sensori si sono verificate fluttuazioni. Cosa è accaduto quindi in precedenza?

    * **Azione**: aggiornare l'intervallo di visualizzazione a `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Avvio rapido - passaggio 4][4]

1. Esaminando due interi anni di dati, è possibile notare un evento di incendio precedente con gli stessi segnali. Con questi dati è possibile creare sistemi per rilevare tempestivamente problemi simili.

    * **Azione**: aggiornare l'intervallo di visualizzazione a `1/1/16 – 12/31/17` (tutti i dati).

       ![Avvio rapido - passaggio 5][5]

### <a name="demo-step-two"></a>Demo - passaggio 2

1. Altri problemi sono più complessi e più difficili da diagnosticare. Time Series Insights offre una gamma di funzionalità che aiutano a monitorare i problemi complessi. In questo caso è possibile vedere un'interruzione del sensore avviso su `turbine #6` in data `6/25`. Ma cosa succede effettivamente?

    * **Azione**: rimuovere i sensori correnti. Aggiornare quindi l'intervallo di visualizzazione a `6/1/17 20:00 – 7/1/17 20:00 (UTC)` e aggiungere `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Avvio rapido - passaggio 6][6]

1. L'avviso indica un problema della tensione in uscita dal generatore. Qual è la causa? La potenzia di uscita complessiva del generatore sembra corretta se analizzata in base a un intervallo granulare. Aggregando i dati, tuttavia, è possibile notare un netto calo.

    * **Azione**: isolare `VoltageActuatorSwitchWarning` e aggiungere `Generator > ActivePower`, quindi aggiornare l'intervallo a `3d`.

       ![Avvio rapido - passaggio 7][7]

1. Spostandosi in avanti nel set di dati, è possibile vedere che non si tratta solo di un problema temporaneo. Il problema è continuativo.

    * **Azione**: estendere l'intervallo di tempo verso destra.

       ![Avvio rapido - passaggio 8][8]

1. Esaminare i dati più in dettaglio. È possibile usare altri punti dati dei sensori per visualizzare la tensione in base alla fase. Tuttavia, sembrano tutti simili. Inserire un indicatore per visualizzare i valori effettivi. Sembra che sia presente un problema con l'uscita della fase 3.

    * **Azione**: `Add Generator > GridVoltagePhase1, 2, & 3`. Inserire un indicatore nell'ultimo punto dati nell'area visibile.

       ![Avvio rapido - passaggio 8][8]

1. Se si visualizzano tutti e tre gli elementi con la stessa scala, il calo della fase 3 appare ancora più evidente. A questo punto, è possibile segnalare il problema al team di manutenzione con un buon indizio sulle cause dell'avviso.  

    * **Azione**: aggiornare la visualizzazione per sovrapporre tutti i sensori sulla stessa scala del grafico.

       ![Avvio rapido - passaggio 9][9]

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti a creare l'ambiente Azure Time Series Insights (anteprima) personalizzato:

> [!div class="nextstepaction"]
> [Pianificare l'ambiente Azure Time Series Insights (anteprima)](time-series-insights-update-plan.md)

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