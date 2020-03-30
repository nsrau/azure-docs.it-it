---
title: Connettere l'ambiente a Power BI - Informazioni dettagliate sulle serie temporali di Azure. Documenti Microsoft
description: Informazioni su come connettere Azure Time Series Insights a Power BI per condividere, grafico e visualizzare dati all'interno dell'organizzazione.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863843"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualizzare i dati da Time Series Insights in Power BI

Azure Time Series Insights è una piattaforma per l'archiviazione, la gestione, l'esecuzione di query e la visualizzazione dei dati di serie temporali nel cloud. [Power BI](https://powerbi.microsoft.com) è uno strumento di analisi aziendale con funzionalità di visualizzazione avanzate che consentono di condividere informazioni dettagliate e risultati all'interno dell'organizzazione. Entrambi i servizi possono ora essere integrati per ottenere il meglio dalle funzionalità di visualizzazione intrinseca di Time Series Insights e da power BI.

Si apprenderà come:

* Connettere time Series Insights a Power BI usando il connettore cloudConnect Time Series Insights to Power BI using the cloud connector
* Creare oggetti visivi con i dati in Power BICreate visuals with your data in Power BI
* Pubblicare il report in Power BI e condividerlo con il resto dell'organizzazione

Alla fine, imparerai a visualizzare i dati di serie temporali tramite Azure Time Series Insights e a migliorarlo con la visualizzazione dei dati forte e le semplici funzionalità di condivisione di Power BI.

Assicurarsi di iscriversi per una sottoscrizione gratuita di [Azure,](https://azure.microsoft.com/free/) se non ne è già disponibile.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare e installare la versione più recente di [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Disporre o creare un'istanza di [Azure Time Series Insights Preview](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Il connettore Power BI è attualmente supportato negli ambienti con pagamento in base al *consumo* di Time Series Insights Preview configurati per **Warm Store.**

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Connettere i dati da Time Series Insights a Power BI

Per connettere l'ambiente Time Series Insights a Power BI, eseguire la procedura seguente:To connect your Time Series Insights environment to Power BI, follow these steps:

1. Aprire Time Series Insights Explorer
1. Esportare i dati come query o come dati non elaboratiExport data as a query or as raw data
1. Aprire Power BI Desktop
1. Carica da query personalizzata

### <a name="export-data-into-power-bi-desktop"></a>Esportare dati nel desktop di Power BIExport data into Power BI desktop

Attività iniziali

1. Apri Time Series Insights Preview Explorer e cura i tuoi dati.
1. Dopo aver creato una visualizzazione di cui si è soddisfatti, passare al menu a discesa **Altre azioni** e selezionare Connetti a **Power BI**.

    [![Esportazione di Time Series Insights Preview Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Impostare i parametri all'interno di questa scheda:

   1. Specificare un intervallo di tempo relativo da visualizzare. Se sei soddisfatto della tua vista esistente, lascia questo come **Intervallo**di tempo esistente .
   
   1. Scegliere tra **Eventi aggregati** e Eventi **non elaborati**. 
   
       > [!NOTE]
       > È sempre possibile aggregare i dati in un secondo momento in Power BI, ma non è possibile ripristinare i dati non elaborati dopo l'aggregazione. 
       
       > [!NOTE]
       > Esiste un limite di numero di eventi di 100 K per i dati a livello di evento non elaborati.

       [![connettersi](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se non è stata configurata l'istanza di Time Series Insights per **Warm Store**, verrà visualizzato un avviso.

       [![Avviso del negozio caldo](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > È possibile configurare l'istanza esistente per **l'archivio caldo** nel portale di Azure.You may configure your existing instance for Warm Store in the Azure portal.

1. Selezionare **Copia query negli Appunti**.
1. A questo punto, avviare Power BI Desktop.
1. Nella scheda **Home** di Power BI Desktop selezionare **Ottieni dati** nell'angolo superiore sinistro e quindi **Altro.**

    [![Menu a discesa Home](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Cercare **Time Series Insights**, selezionare **Azure Time Series Insights (Beta)**, quindi **Connect**.

    [![Connettere Power BI a Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    In alternativa, passare alla scheda **Azure,** selezionare **Azure Time Series Insights (Beta)**, quindi **Connetti**.
    
1. Verrà visualizzata una finestra di dialogo di messaggio in cui viene richiesta l'autorizzazione per connettersi a risorse di terze parti. Selezionare **Continua**.

    [![Scegliere Crea query personalizzata](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Nel menu a discesa in **Origine dati**scegliere Crea **query personalizzata**. Incollare dagli Appunti nel campo **Query personalizzata (facoltativo)** facoltativo riportato di seguito, quindi premere **OK**.

    [![Passare la query personalizzata e selezionare OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. La tabella dati verrà caricata. Premere **Carica** per caricare in Power BI.

    [![Esaminare i dati caricati nella tabella e selezionare Carica](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se questi passaggi sono stati completati, passare alla sezione successiva.

## <a name="create-a-report-with-visuals"></a>Creare un report con gli oggetti visivi

Dopo aver importato i dati in Power BI, è necessario creare un report con oggetti visivi.

1. Sul lato sinistro della finestra, assicurarsi di aver selezionato la visualizzazione **Report.**

    [![Selezionare la visualizzazione Report](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Nella colonna **Visualizzazioni** selezionare l'oggetto visivo desiderato. Ad esempio, selezionare **Grafico a linee**. Verrà aggiunto un grafico a linee vuote all'area di disegno.
 
1.  Nell'elenco **Campi** selezionare **Timestamp** e trascinarlo nel campo **Asse** per visualizzare gli elementi lungo l'asse X.

1.  Anche in questo caso, nell'elenco **Campi** selezionare **TimeSeriesId** e trascinarlo nel campo **Valori** per visualizzare gli elementi lungo l'asse Y.

    [![Creare un grafico a linee](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Per aggiungere un altro grafico all'area di disegno, selezionare un punto qualsiasi dell'area di disegno all'esterno del grafico a linee e ripetere questo processo.

    [![Creare grafici aggiuntivi da condividere](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Dopo aver creato il report, è possibile pubblicarlo in Power BI Reporting Services.

## <a name="advanced-editing"></a>Modifica avanzata

Se è già stato caricato un set di dati in Power BI ma si vuole modificare la query, ad esempio i parametri data/ora o ID ambiente, è possibile eseguire questa operazione tramite la funzionalità Editor avanzato di Power BI. Per altre informazioni, vedere la documentazione di [Power BI.](https://docs.microsoft.com/power-bi/desktop-query-overview)

Come panoramica:

1. In Power BI Desktop selezionare **Modifica query.**
1. Premere **Editor avanzato**.

    [![Modificare le query nell'Editor avanzato](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modificare il payload JSON come desiderato.
1. Selezionare **Fine** e quindi **Chiudi & Applica** nella finestra **dell'editor di Power Query**.

L'interfaccia rifletterà ora le modifiche desiderate applicate.  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti relativi [ai connettori di Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) per Azure Time Series Insights.Read about Power BI connector concepts for Azure Time Series Insights.

* Altre informazioni sul desktop di [Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Leggere [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e Time Series Insights Preview [Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
