---
title: Connettere l'ambiente a Power BI-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come connettere Azure Time Series Insights a Power BI per condividere, visualizzare grafici e visualizzare i dati nell'intera organizzazione.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e218877ee0d29e493a31091ccbce406ed888f8cc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114734"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualizzare i dati da Time Series Insights in Power BI

Azure Time Series Insights è una piattaforma per l'archiviazione, la gestione, l'esecuzione di query e la visualizzazione di dati di serie temporali nel cloud. [Power bi](https://powerbi.microsoft.com) è uno strumento di analisi aziendale con funzionalità di visualizzazione avanzate che consente di condividere informazioni dettagliate e risultati nell'intera organizzazione. È ora possibile integrare entrambi i servizi per sfruttare al meglio le funzionalità di visualizzazione intrinseche di Time Series Insights e di Power BI.

Si apprenderà come:

* Connettersi Time Series Insights a Power BI tramite il connettore Cloud
* Creare oggetti visivi con i dati in Power BI
* Pubblicare il report per Power BI e condividere con il resto dell'organizzazione

Alla fine, si apprenderà come visualizzare i dati delle serie temporali tramite Azure Time Series Insights e migliorarli con la visualizzazione dei dati avanzata e le funzionalità di condivisione semplici di Power BI.

Assicurarsi di iscriversi per ottenere una [sottoscrizione gratuita di Azure](https://azure.microsoft.com/free/) , se non ne è già disponibile una.

## <a name="prerequisites"></a>prerequisiti

* Scaricare e installare la versione più recente di [Power bi desktop](https://powerbi.microsoft.com/downloads/)
* Avere o creare un' [istanza di Azure Time Series Insights GA](time-series-insights-get-started.md) o un' [istanza di anteprima Azure Time Series Insights](time-series-insights-update-how-to-manage.md)

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Connettere i dati da Time Series Insights a Power BI

Per connettere l'ambiente di Time Series Insights al Power BI, attenersi alla procedura seguente:

1. Apri Esplora Time Series Insights                      
1. Esportare dati come query o come dati non elaborati                       
1. Apri Power BI Desktop
1. Carica da query personalizzata

### <a name="export-data-into-power-bi-desktop"></a>Esportare i dati in Power BI desktop

Attività iniziali

1. Aprire Esplora Time Series Insights Preview e curare i dati.
1. Dopo aver creato una vista soddisfatta, passare al menu a discesa **altre azioni** e fare clic su **Connetti a Power bi**. 

    [esportazione di ![Time Series Insights Preview Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Impostare i parametri all'interno di questa scheda:

   1. Consente di specificare un intervallo di tempo relativo da visualizzare. Se si è soddisfatti della visualizzazione esistente, lasciarlo come intervallo di tempo **esistente**. 
   1. Scegliere tra **eventi non elaborati**e **aggregati** . 
   
       > [!NOTE]
       > È sempre possibile aggregare i dati in un secondo momento in Power BI, ma non è possibile ripristinare i dati non elaborati dopo l'aggregazione. 
       
       > [!NOTE]
       > È previsto un limite di numero di eventi 100-K per i dati a livello di evento non elaborato.

       [Connessione ![](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se non è stata configurata l'istanza di Time Series Insights per warm Store, verrà visualizzato un avviso.

       [Connessione ![](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > È possibile configurare l'istanza esistente per warm Store nel portale di Azure.

1. Selezionare **copia query negli Appunti**.
1. A questo punto, avviare Power BI Desktop.
1. In Power BI Desktop nella scheda **Home** selezionare **recuperare i dati** nell'angolo in alto a sinistra, quindi **altro**.

    [elenco a discesa Home ![](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Cercare **Time Series Insights**, selezionare **Azure Time Series Insights (beta)** , quindi **Connetti**.

    [![connettersi Power BI a Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    In alternativa, passare alla scheda **Azure** , selezionare **Azure Time Series Insights (beta)** , quindi **Connetti**.
    
1. Viene visualizzata una finestra di dialogo che richiede l'autorizzazione per la connessione a risorse di terze parti. Selezionare **continua**.

    [![scegliere Crea query personalizzata](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Nel menu a discesa in **origine dati**scegliere **Crea query personalizzata**. Incollare gli Appunti nel campo facoltativo **query personalizzata (facoltativo)** di seguito, quindi fare clic su **OK**.

    [![passare la query personalizzata e selezionare OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. La tabella dati verrà ora caricata. Premere **carica** per caricare i Power bi.

    [![esaminare i dati caricati nella tabella e selezionare carica](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se questi passaggi sono stati completati, passare alla sezione successiva.

## <a name="create-a-report-with-visuals"></a>Creare un report con gli oggetti visivi

Ora che i dati sono stati importati in Power BI, è possibile creare un report con oggetti visivi.

1. Sul lato sinistro della finestra verificare di aver selezionato la visualizzazione **report** .

    [![selezionare la visualizzazione report](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Nella colonna **visualizzazioni** selezionare l'oggetto visivo scelto. Ad esempio, selezionare **grafico a linee**. Verrà aggiunto un grafico a linee vuoto nell'area di disegno.
 
1.  Nell'elenco **campi** selezionare **timestamp** e trascinarlo nel campo **asse** per visualizzare gli elementi lungo l'asse X.

1.  Anche in questo caso, nell'elenco **campi** selezionare **TimeSeriesId** e trascinarlo nel campo **valori** per visualizzare gli elementi lungo l'asse Y.

    [![creare un grafico a linee](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Per aggiungere un altro grafico all'area di disegno, fare clic in un punto qualsiasi dell'area di disegno all'esterno del grafico a linee e ripetere il processo.

    [![creare grafici aggiuntivi da condividere](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Una volta creato il report, è possibile pubblicarlo in Power BI Reporting Services.

## <a name="advanced-editing"></a>Modifica avanzata

Se è già stato caricato un set di dati in Power BI ma si vuole modificare la query, ad esempio i parametri di data/ora o ID ambiente, è possibile eseguire questa operazione tramite la funzionalità di Editor avanzato di Power BI. Per altre informazioni, vedere la [documentazione Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Panoramica:

1. In Power BI Desktop selezionare **modifica query**.
1. Premere **Editor avanzato**.

    [![modificare le query nell'Editor avanzato](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modificare il payload JSON nel modo desiderato.
1. Selezionare **fine** e quindi **Chiudi & applica** nella **finestra dell'editor di Power query**.

Verranno applicate le modifiche desiderate.  

## <a name="next-steps"></a>Passaggi successivi

* Per Azure Time Series Insights, vedere Concetti relativi a [Power bi Connector](https://docs.microsoft.com/power-bi/desktop-query-overview) .

* Scopri di più su [Power bi desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Vedere [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e [Time Series Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
