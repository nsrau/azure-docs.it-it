---
title: Connect your environment to Power BI - Azure Time Series Insights | Microsoft Docs
description: Learn how to connect Azure Time Series Insights to Power BI to share, chart, and display data across your organization.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863843"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualize data from Time Series Insights in Power BI

Azure Time Series Insights is a platform for storing, managing, querying, and visualizing time-series data in the cloud. [Power BI](https://powerbi.microsoft.com) is a business analytics tool with rich visualization capabilities that allows you to share insights and results across your organization. Both services can now be integrated to get the best of both Time Series Insights' inherent visualization capabilities as well as Power BI's.

Si apprenderà come:

* Connect Time Series Insights to Power BI using the cloud connector
* Create visuals with your data in Power BI
* Publish the report to Power BI and share with the rest of your organization

By the end, you'll learn how to visualize time-series data through Azure Time Series Insights and enhance it with the strong data visualization and easy sharing capabilities of Power BI.

Make sure to sign up for a [free Azure subscription](https://azure.microsoft.com/free/) if you don't already have one.

## <a name="prerequisites"></a>Prerequisiti

* Download and install the latest version of [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Have or create an [Azure Time Series Insights Preview instance](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> The Power BI connector is presently supported in Time Series Insights Preview *pay-as-you-go* environments configured for **Warm Store**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Connect data from Time Series Insights to Power BI

To connect your Time Series Insights environment to Power BI, follow these steps:

1. Open Time Series Insights Explorer
1. Export data as a query or as raw data
1. Open Power BI Desktop
1. Load from Custom Query

### <a name="export-data-into-power-bi-desktop"></a>Export data into Power BI desktop

Per iniziare:

1. Open the Time Series Insights Preview Explorer and curate your data.
1. Dopo aver creato una vista soddisfatta, passare al menu a discesa **altre azioni** e selezionare **Connetti a Power bi**.

    [esportazione di ![Time Series Insights Preview Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Impostare i parametri all'interno di questa scheda:

   1. Consente di specificare un intervallo di tempo relativo da visualizzare. Se si è soddisfatti della visualizzazione esistente, lasciarlo come intervallo di tempo **esistente**.
   
   1. Scegliere tra **eventi non elaborati**e **aggregati** . 
   
       > [!NOTE]
       > È sempre possibile aggregare i dati in un secondo momento in Power BI, ma non è possibile ripristinare i dati non elaborati dopo l'aggregazione. 
       
       > [!NOTE]
       > È previsto un limite di numero di eventi 100-K per i dati a livello di evento non elaborato.

       [Connessione ![](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se non è stata configurata l'istanza di Time Series Insights per **warm Store**, verrà visualizzato un avviso.

       [avviso ![warm Store](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > È possibile configurare l'istanza esistente per **warm Store** nel portale di Azure.

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

1.  Per aggiungere un altro grafico all'area di disegno, selezionare un punto qualsiasi nell'area di disegno all'esterno del grafico a linee e ripetere il processo.

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

L'interfaccia rifletterà ora le modifiche desiderate applicate.  

## <a name="next-steps"></a>Fasi successive

* Per Azure Time Series Insights, vedere Concetti relativi a [Power bi Connector](https://docs.microsoft.com/power-bi/desktop-query-overview) .

* Scopri di più su [Power bi desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Leggere [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e [Time Series Insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
