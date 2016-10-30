<properties 
    pageTitle="Utilizzare Analytics - Il potente strumento di ricerca di Application Insights | Microsoft Azure" 
    description="Utilizzare Analytics: lo strumento di ricerca diagnostica incluso in Application Insights " 
    services="application-insights" 
    documentationCenter=""
    authors="danhadari" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="danha"/>



# <a name="using-analytics-in-application-insights"></a>Uso di Analytics in Application Insights


L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analytics.

* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.

## <a name="open-analytics"></a>Aprire Analytics

Nella home page dell'app in Application Insights fare clic su Analytics.

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics-using/001.png)

L'esercitazione inline fornisce alcune informazioni su come procedere.

È disponibile una [panoramica più ampia qui](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Eseguire query sui dati di telemetria

### <a name="write-a-query"></a>Scrivere una query

![Visualizzazione schema](./media/app-insights-analytics-using/150.png)

Iniziare con i nomi delle tabelle elencate a sinistra oppure con l'operatore [range](app-insights-analytics-reference.md#range-operator) o [union](app-insights-analytics-reference.md#union-operator). Usare `|` per creare una pipeline di [operatori](app-insights-analytics-reference.md#queries-and-operators). IntelliSense suggerisce gli operatori e alcuni elementi delle espressioni che è possibile usare.

Vedere la [panoramica del linguaggio di Analisi](app-insights-analytics-tour.md) e le [informazioni di riferimento sul linguaggio](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Eseguire una query

![Esecuzione di una query](./media/app-insights-analytics-using/130.png)

1. Nelle query è possibile usare interruzioni di riga.
2. Posizionare il cursore all'interno o alla fine della query da eseguire.
3. Fare clic su Vai per eseguire la query.
4. Non inserire righe vuote nella query. È possibile mantenere più query separate in un'unica scheda di query, separandole con righe vuote. Verrà eseguita solo quella con il cursore.

### <a name="save-a-query"></a>Salvare una query

![Salvataggio di una query](./media/app-insights-analytics-using/140.png)

1. Salvare il file di query corrente.
2. Aprire un file di query salvato.
3. Creare un nuovo file di query.


## <a name="see-the-details"></a>Visualizzare i dettagli

Espandere una riga dei risultati per visualizzarne l'elenco completo delle proprietà. È possibile espandere ulteriormente qualsiasi proprietà che sia un valore strutturato, ad esempio le dimensioni personalizzate o l'elenco in pila di un'eccezione.

![Espandere una riga](./media/app-insights-analytics-using/070.png)

 

## <a name="arrange-the-results"></a>Disporre i risultati

È possibile ordinare, filtrare, impaginare e raggruppare i risultati restituiti dalla query.

> [AZURE.NOTE] Le operazioni di ordinamento, raggruppamento e filtro nel browser non eseguono nuovamente la query, ma riorganizzano i risultati restituiti dall'ultima query. 
> 
> Per eseguire queste attività nel server prima che vengano restituiti i risultati, scrivere la query usando gli operatori [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) e [where](app-insights-analytics-reference.md#where-operator).

Selezionare le colonne da visualizzare, trascinare le intestazioni di colonna per ridisporle e ridimensionare le colonne trascinandone i bordi.

![Disporre le colonne](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Ordinare e filtrare elementi

Ordinare i risultati facendo clic sull'intestazione di una colonna. Fare clic di nuovo per applicare l'ordinamento opposto. Fare clic una terza volta per ripristinare l'ordine originale restituito dalla query.

Usare l'icona del filtro per perfezionare la ricerca.

![Ordinare e filtrare le colonne](./media/app-insights-analytics-using/040.png)



### <a name="group-items"></a>Raggruppare elementi

Per ordinare più di una colonna, usare il raggruppamento. Abilitare il raggruppamento e quindi trascinare le intestazioni di colonna nello spazio sopra la tabella.

![Gruppo](./media/app-insights-analytics-using/060.png)



### <a name="missing-some-results?"></a>Mancano alcuni risultati?

È previsto un limite di 10.000 righe di risultati restituiti dal portale. Se il numero di risultati supera il limite, verrà visualizzato un avviso. In tal caso, l'ordinamento dei risultati nella tabella non permette sempre di visualizzare i primo o gli ultimi risultati effettivi. 

È consigliabile evitare di raggiungere il limite. Usare operatori quali:

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [timestamp top 100 by](app-insights-analytics-reference.md#top-operator) 
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize ](app-insights-analytics-reference.md#summarize-operator) 



## <a name="diagrams"></a>Diagrammi

Selezionare il tipo di diagramma desiderato:

![Selezionare un tipo di diagramma](./media/app-insights-analytics-using/230.png)

Se sono presenti più colonne dei tipi corretti, è possibile scegliere gli assi X e Y e una colonna di dimensioni in base alla quale dividere i risultati.

Per impostazione predefinita, i risultati vengono inizialmente visualizzati in una tabella e si seleziona il diagramma manualmente. Per selezionare il diagramma, è possibile usare la [direttiva render](app-insights-analytics-reference.md#render-directive) alla fine di una query.

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard

Per aggiungere un diagramma o una tabella a uno dei [dashboard condivisi](app-insights-dashboards.md) , è sufficiente fare clic sulla puntina. Potrebbe essere necessario [aggiornare il pacchetto dei prezzi dell'app](app-insights-pricing.md) per attivare questa funzionalità. 

![Fare clic sulla puntina](./media/app-insights-analytics-using/pin-01.png)

Ciò significa che, quando si crea un dashboard per monitorare le prestazioni o l'utilizzo dei servizi Web, è possibile includere analisi piuttosto complesse insieme ad altre metriche. 

È possibile aggiungere una tabella al dashboard se contiene un massimo di quattro colonne. Verranno visualizzate solo le prime sette righe.


#### <a name="dashboard-refresh"></a>Aggiornamento del dashboard

Il grafico aggiunto al dashboard viene aggiornato automaticamente eseguendo di nuovo la query ogni mezz'ora circa.

#### <a name="automatic-simplifications"></a>Semplificazioni automatiche

In alcuni casi, determinate semplificazioni vengono applicate a un grafico quando lo si aggiunge a un dashboard.

Quando si aggiunge un grafico che visualizza molti bin discreti, in genere un grafico a barre, i bin meno popolati vengono raggruppati automaticamente in un unico bin denominato "other". Ad esempio, questa query:

    requests | summarize count_search = count() by client_CountryOrRegion

ha questo aspetto in Analisi:


![Grafico con una lunga coda](./media/app-insights-analytics-using/pin-07.png)

ma quando la si aggiunge un dashboard, è simile alla figura seguente:


![Grafico con bin limitati](./media/app-insights-analytics-using/pin-08.png)




## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

Dopo aver eseguito una query, è possibile scaricare un file con estensione csv. Fare clic su **Esporta in Excel**.

## <a name="export-to-power-bi"></a>Esportare in Power BI

1. Posizionare il cursore in una query e scegliere **Esporta in Power BI**.

    ![](./media/app-insights-analytics-using/240.png)

    Verrà scaricato un file di script M.

3. Copiare lo script del linguaggio M nell'editor di query avanzato di Power BI Desktop.
 * Aprire il file esportato.
 * In Power BI Desktop selezionare **Recupera dati, Query vuota, Editor avanzato** e incollare lo script del linguaggio M.

    ![](./media/app-insights-analytics-using/250.png)

4. Se necessario, modificare le credenziali e procedere alla creazione del report.

    ![](./media/app-insights-analytics-using/260.png)



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]




<!--HONumber=Oct16_HO2-->


