---
title: Esportare in Power BI da Application Insights | Documentazione Microsoft
description: Le query di Analisi possono essere visualizzate in Power BI.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 24ccafb4df95e0010416485199e19f81e1ae31aa
ms.openlocfilehash: 11017c7c0a761569892aebcd085d5d3fb2d67a69
ms.contentlocale: it-it
ms.lasthandoff: 02/14/2017


---
# <a name="feed-power-bi-from-application-insights"></a>Feed di Power BI da Application Insights
[Power BI](http://www.powerbi.com/) è una suite di strumenti di analisi business che consente di analizzare i dati e condividere informazioni dettagliate. Dashboard completi sono disponibili in tutti i dispositivi. È possibile combinare dati provenienti da diverse origini, incluse le query di Analytics di [Azure Application Insights](app-insights-overview.md).

Esistono tre metodi consigliati per esportare i dati di Application Insights in Power BI. Possono essere usati insieme o separatamente.

* [**Adattatore Power BI**](#power-pi-adapter): consente di impostare un dashboard di dati di telemetria completo dall'app. Il set di tabelle è predefinito, ma è possibile aggiungere query da qualsiasi altra origine.
* [**Esportazione di query di Analisi**](#export-analytics-queries): consiste di scrivere qualsiasi query con Analisi e poi di esportarla in Power BI. È possibile inserire questa query in un dashboard insieme a tutti gli altri dati.
* [**Esportazione continua e analisi di flusso**](app-insights-export-stream-analytics.md): l'impostazione è più impegnativa. È utile se si desidera conservare i dati per lunghi periodi. Altrimenti, si consiglia di usare gli altri metodi.

## <a name="power-bi-adapter"></a>Adattatore Power BI
Con questo metodo si crea un dashboard di dati di telemetria completo per l'utente. Il set di dati iniziale è predefinito, ma è possibile aggiungere altri dati.

### <a name="get-the-adapter"></a>Scaricare l'adattatore
1. Accedere a [Power BI](https://app.powerbi.com/).
2. Aprire **Get Data** (Ottieni dati), **Servizi**, **Application Insights**
   
    ![Scaricare da un'origine dati di Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Specificare i dettagli della risorsa di Application Insights.
   
    ![Scaricare da un'origine dati di Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Attendere uno o due minuti per il completamento dell'importazione dei dati.
   
    ![Adattatore Power BI](./media/app-insights-export-power-bi/010.png)

È possibile modificare il dashboard unendo i grafici di Application Insights con i grafici di altre origini e con le query di Analisi. È disponibile una raccolta di visualizzazioni nella quale è possibile ottenere più grafici e ogni grafico include parametri che possono essere impostati.

Dopo l'importazione iniziale, il dashboard e i report continuano a essere aggiornati ogni giorno. È possibile controllare la pianificazione dell'aggiornamento nel set di dati.

## <a name="export-analytics-queries"></a>Esportare query di Analisi
Questo percorso consente di scrivere tutte le query di Analisi desiderate e di esportarle in un dashboard di Power BI. È possibile aggiungerle al dashboard creato dall'adattatore.

### <a name="one-time-install-power-bi-desktop"></a>Operazione da eseguire una sola volta: installazione di Power BI Desktop
Per importare la query di Application Insights, usare la versione desktop di Power BI. Tuttavia, successivamente è possibile pubblicarla sul Web o nell'area di lavoro cloud di Power BI. 

Installare [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Esportare una query di Analisi
1. [Aprire Analisi e scrivere la query](app-insights-analytics-tour.md).
2. Testare e affinare la query fino a quando non si è soddisfatti dei risultati.

   **Verificare che la query venga eseguita correttamente in Analytics prima di esportarla.**
3. Nel menu **Esporta** scegliere **Power BI (M)**. Salvare il file di testo.
   
    ![Esportare la query di Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. In Power BI Desktop selezionare **Get Data (Ottieni dati), Query vuota** e quindi nell'editor di query andare su **Visualizza** e selezionare **Editor query avanzata**.

    Incollare lo script del linguaggio M esportato nell'Editor query avanzata.

    ![Editor query avanzata](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Potrebbe essere necessario fornire le credenziali per consentire a Power BI di accedere ad Azure. Andare su Account aziendale per accedere con l'account Microsoft.
   
    ![Specificare le credenziali di Azure per consentire a Power BI di eseguire la query di Application Insights](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Se è necessario verificare le credenziali, usare il comando di menu Impostazioni origine dati nell'editor di query. Assicurarsi di specificare le credenziali usate per Azure, che potrebbero essere diverse da quelle di Power BI.
2. Scegliere una visualizzazione per la query e selezionare i campi per l'asse x, l'asse y e le dimensioni di segmentazione.
   
    ![Selezionare la visualizzazione](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Pubblicare il report nell'area di lavoro cloud di Power BI. Da qui è possibile incorporare una versione sincronizzata in altre pagine Web.
   
    ![Selezionare la visualizzazione](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Aggiornare manualmente il report a intervalli oppure impostare un aggiornamento pianificato nella pagina Opzioni.

## <a name="about-sampling"></a>Informazioni sul campionamento
Se l'applicazione invia una grande quantità di dati, la funzionalità di campionamento adattivo può essere usata e inviare solo una percentuale dei dati di telemetria. La stessa considerazione vale se il campionamento è stato impostato manualmente nell'SDK o durante l'inserimento. [Altre informazioni sul campionamento.](app-insights-sampling.md)

## <a name="next-steps"></a>Passaggi successivi
* [Power BI - Informazioni](http://www.powerbi.com/learning/)
* [Esercitazione su Analisi](app-insights-analytics-tour.md)


