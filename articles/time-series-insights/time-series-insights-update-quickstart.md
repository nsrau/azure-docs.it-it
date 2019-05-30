---
title: "Avvio rapido: Esplorare l'ambiente demo Anteprima di Azure Time Series Insights | Microsoft Docs"
description: Informazioni sull'ambiente demo Anteprima di Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: e35d46607e0a186c8a3a38669c68a6ea52711b51
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242078"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Avvio rapido: Esplorare l'ambiente demo Anteprima di Azure Time Series Insights

Questo argomento di avvio rapido consente di iniziare usare Anteprima di Azure Time Series Insights. La demo gratuita offre una panoramica delle funzionalità principali aggiunte ad Anteprima di Time Series Insights.

L'ambiente demo di Anteprima contiene una società fittizia, Contoso, che gestisce due centrali con turbine eoliche, ognuna con 10 turbine eoliche. Ogni turbina è dotata di 20 sensori che inviano dati ogni minuto all’hub IoT di Azure. I sensori raccolgono informazioni su condizioni meteo, inclinazione delle pale e posizione di imbardata, ma anche su prestazioni del generatore, comportamento della trasmissione e monitoraggi di sicurezza.

 Verrà illustrato come usare Time Series Insights per trovare informazioni operative dettagliate nei dati di Contoso. Si condurrà anche una breve analisi delle principali cause per agevolare la previsione dei guasti critici e la manutenzione.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Esaminare lo strumento di esplorazione di Time Series Insights in un ambiente demo

Lo strumento di esplorazione di Anteprima di Time Series Insights Preview consente di visualizzare i dati cronologici e di eseguire un'analisi delle cause radice. Attività iniziali

1. Creare un  [account Azure gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se non ne è ancora stato creato uno.

1. Passare all'ambiente  [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples).  

1. Se viene richiesto, accedere allo strumento di esplorazione di Time Series Insights usando le credenziali dell'account Azure.

## <a name="work-with-historical-data"></a>Usare i dati cronologici

1. Esaminare la turbina eolica **W7** in **Contoso Plant 1**.  

    * Aggiornare l'intervallo di visualizzazione impostando il periodo **dall'1/1/17 20:00 al 10/3/17 20:00 (UTC)** .
    * Selezionare il sensore **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed**. Esaminare quindi i valori risultanti.

      [![W7 in Contoso Plant 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Di recente, Contoso ha riscontrato un incendio nella turbina eolica **W7**. Ci sono diverse opinioni sulla causa diretta dell'incendio. Da un'ispezione più attenta risulta che il sensore dell'allarme antincendio si è attivato durante l'incendio.

    * Aggiornare l'intervallo di visualizzazione impostando il periodo **dal 9/3/17 20:00 al 10/3/17 20:00 (UTC)** .
    * Selezionare il sensore **Safety System** > **FireAlert**.

      [![Contoso ha riscontrato un incendio nella turbina eolica W7](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Esaminare gli altri eventi verificatisi al momento dell'incendio per capire cosa è accaduto. Sia la pressione dell'olio che gli avvisi attivi hanno raggiunto valori di picco subito prima dell'incendio.

    * Selezionare il sensore **Pitch System** > **HydraulicOilPressure**.
    * Selezionare il sensore **Pitch System** > **ActiveWarning**.

      [![Esaminare gli altri eventi verificatisi nello stesso momento](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. I sensori della pressione dell'olio e degli avvisi attivi hanno raggiunto valori di picco subito prima dell'incendio. Espandere la serie temporale visualizzata per verificare la presenza di altri segnali che conducono all'incendio. Per entrambi i sensori si sono verificate nel corso del tempo fluttuazioni coerenti indicanti un modello persistente e preoccupante.

    * Aggiornare l'intervallo di visualizzazione impostando il periodo **dal 24/2/17 20:00 al 10/3/17 20:00 (UTC)** .

      [![Anche i sensori della pressione dell'olio e degli avvisi attivi hanno raggiunto valori di picco](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. L'analisi di due anni di dati cronologici rivela che si era già verificato un incendio con le stesse fluttuazioni dei sensori.

    * Aggiornare l'intervallo di visualizzazione impostando il periodo **dall'1/1/16 al 31/12/17** (tutti i dati).

      [![Cercare modelli cronologici](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Usando Azure Time Series Insights e i dati di telemetria dei sensori, è emersa una tendenza problematica e a lungo termine nascosta nei dati cronologici. Con queste nuove informazioni dettagliate, è possibile:

> [!div class="checklist"]
> * Illustrare ciò che è effettivamente accaduto.
> * Risolvere il problema.
> * Implementare sistemi di notifica degli avvisi più efficienti.

## <a name="root-cause-analysis"></a>Analisi della causa radice

1. Alcuni scenari richiedono un'analisi sofisticata per scoprire anche i più piccoli indizi nei dati. Selezionare la turbina eolica **W6** in data **25/6**

    * Aggiornare l'intervallo di visualizzazione impostando il periodo **dall'1/6/17 20:00 all'1/7/17 20:00 (UTC)** .
    * Selezionare quindi il sensore **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

      [![Aggiornare l'intervallo di visualizzazione e selezionare W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. L'avviso indica un problema della tensione in uscita dal generatore. La potenza complessiva in uscita del generatore sta operando entro i normali parametri per l'intervallo corrente. Aumentando l'intervallo, emerge un altro modello indicante un netto calo.

    * Rimuovere il sensore **VoltageActuatorSwitchWarning**.
    * Selezionare il sensore **Generator System** > **ActivePower**.
    * Aggiornare l'intervallo impostandolo su **3d**.

      [![Aggiornare l'intervallo impostandolo su 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Espandendo l'intervallo di tempo, è possibile determinare se il problema si è risolto o se continua.

    * Estendere l'intervallo di tempo fino a 60 giorni.

      [![Estendere l'intervallo di tempo fino a 60 giorni](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. È possibile aggiungere altri punti dati del sensore per fornire un contesto più ampio. Visualizzando un numero più elevato di sensori, è possibile comprendere meglio il problema. Inserire un indicatore per visualizzare i valori effettivi. 

    * Selezionare i sensori **Generator System** > **GridVoltagePhase1**, **GridVoltagePhase2** e **GridVoltagePhase3**.
    * Inserire un indicatore nell'ultimo punto dati nell'area visibile.

      [![Inserire un indicatore](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    I tre sensori di tensione stanno funzionando in modo analogo ed entro parametri normali. Il sensore **GridVoltagePhase3** sembra essere la causa.

1. Con l'aggiunta di dati altamente contestuali, appare sempre più evidente che il problema è dovuto al calo della fase 3. È ora possibile segnalare il problema al team di manutenzione con un buon indizio sulle cause dell'avviso.  

    * Aggiornare la visualizzazione per sovrapporre tutti i sensori di **Generator System** sulla stessa scala del grafico.

       [![Aggiornare la visualizzazione per includere tutti i dati](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti a creare l'ambiente Anteprima di Time Series Insights:

> [!div class="nextstepaction"]
> [Pianificare l’ambiente Anteprima di Time Series Insights](time-series-insights-update-plan.md)

Esplorare la demo e le relative funzionalità:

> [!div class="nextstepaction"]
> [Strumento di esplorazione di Anteprima di Time Series Insights](time-series-insights-update-explorer.md)
