<properties 
	pageTitle="Utilizzare Analytics: lo strumento di ricerca avanzato incluso in Application Insights" 
	description="Utilizzare Analytics: lo strumento di ricerca diagnostica incluso in Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>




# Uso di Analytics in Application Insights


[Analytics](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## Aprire Analytics

Nella home page dell'app in Application Insights fare clic su Analytics.

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics/001.png)

L'esercitazione in linea fornisce alcune informazioni su come procedere.

È disponibile una [panoramica più ampia qui](app-insights-analytics-tour.md).

## Scrivere query

Scrivere una query iniziando con uno dei nomi delle tabelle elencate a sinistra. Usare `|` per creare una pipeline di [operatori](app-insights-analytics-queries.md).


![](./media/app-insights-analytics-using/150.png)

* Non inserire righe vuote nella query.
* Nelle query è possibile usare interruzioni di riga.
* È possibile mantenere più query nella finestra, separate da righe vuote.
* Per eseguire una query, **posizionare il cursore all'interno o alla fine della query** e fare clic su Vai.


![](./media/app-insights-analytics-using/130.png)

* È possibile salvare e richiamare il contenuto della finestra della query.

![](./media/app-insights-analytics-using/140.png)

## Ordinare i risultati

È possibile scegliere le colonne che si desidera visualizzare. Espandere gli elementi per visualizzare tutti i valori della colonna restituiti.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] Fare clic sull'intestazione di una colonna per riordinare velocemente i risultati disponibili nel Web browser. Tuttavia, tenere presente che per un set di risultati di grandi dimensioni, il numero di righe scaricate nel browser è limitato. Pertanto, questa modalità di ordinamento non sempre illustra gli elementi effettivi massimi o minimi. A tale scopo, usare l'operatore [top](app-insights-analytics-queries.md#top-operator) o [sort](app-insights-analytics-queries.md#sort-operator).

Tuttavia, si consiglia di usare gli operatori [take](app-insights-analytics-queries.md#take-operator), [top](app-insights-analytics-queries.md#top-operator) o [summarize](app-insights-analytics-queries.md#summarize-operator) per evitare il download di tabelle di grandi dimensioni dal server. È comunque previsto un limite automatico di circa 10.000 righe per ogni query.


## Diagrammi

Selezionare il tipo di diagramma desiderato:

![](./media/app-insights-analytics-using/230.png)

Se sono presenti più colonne dei tipi corretti, è possibile scegliere gli assi x e y e una colonna di dimensioni per dividere i risultati in base a:

![](./media/app-insights-analytics-using/100.png)

Per impostazione predefinita, i risultati vengono inizialmente visualizzati in una tabella e si seleziona il diagramma manualmente. Per selezionare il diagramma è possibile usare la [direttiva render](app-insights-analytics-queries.md#render-directive) alla fine di una query.

## Eseguire l'esportazione in Excel

Dopo aver eseguito una query, è possibile scaricare un file con estensione csv. Fare clic su **Esporta, Excel**.

## Esportare in Power BI

1. Posizionare il cursore in una query e scegliere **Esporta, Power BI**.

    ![](./media/app-insights-analytics-using/240.png)

    Verrà scaricato un file di script M.

3. Copiare lo script del linguaggio M nell'editor di query avanzato di Power BI Desktop.
 * Aprire il file esportato.
 * In Power BI Desktop selezionare: **Recupera dati, Query vuota, Editor avanzato** e incollare lo script del linguaggio M.

    ![](./media/app-insights-analytics-using/250.png)

4. Se necessario, modificare le credenziali e procedere alla creazione del report.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0511_2016-->