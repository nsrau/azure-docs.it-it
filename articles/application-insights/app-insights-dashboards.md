<properties
	pageTitle="Utilizzo del portale Application Insights"
	description="Metriche, ricerca, dashboard e impostazioni nel portale Application Insights."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="06/03/2016"
	ms.author="awills"/>

# Navigazione e dashboard nel portale Application Insights

Dopo aver [impostato Application Insights nel progetto](app-insights-overview.md), i dati di telemetria sull'utilizzo e le prestazioni dell'applicazione verranno visualizzati nella risorsa di Application Insights del progetto nel [portale Azure](https://portal.azure.com).

## Trovare i dati di telemetria

Nel [portale di Azure](https://portal.azure.com) passare alla risorsa di Application Insights creata per la propria applicazione.

![Fare clic su Sfoglia, selezionare Application Insights, quindi fare clic sull'app.](./media/app-insights-dashboards/00-start.png)

La pagina della panoramica mostra alcuni dati di telemetria di base e include collegamenti ad altre informazioni. Il contenuto varia a seconda del tipo di app e può essere personalizzato.


## Pannello Panoramica dell'app

Il pannello (pagina) Panoramica dell'app mostra un riepilogo della metrica di diagnostica chiave dell'app e permette di accedere alle altre funzionalità del portale.

Fare clic su:

* **Qualsiasi grafico o riquadro** per visualizzare altri dettagli sui contenuti del grafico.
* **Impostazioni** per ottenere pannelli predefiniti di altre metriche, nonché pagine di configurazione.
* [**Esplora metriche**](app-insights-metrics-explorer.md) per creare pagine di metriche personalizzate.
* [**Cerca**](app-insights-diagnostic-search.md) per analizzare istanze specifiche di eventi, ad esempio richieste, eccezioni o tracce di log.
* [**Analisi**](app-insights-analytics.md) per eseguire query avanzate con i dati di telemetria.
* [**Flusso live**](app-insights-metrics-explorer.md#live-stream) per un set fisso di metriche quasi istantanee, utile quando si distribuisce una nuova compilazione o si esegue il debug.


![Route principali per visualizzare i dati di telemetria](./media/app-insights-dashboards/010-oview.png)


### Personalizzare il pannello Panoramica 

Scegliere gli elementi da visualizzare nella panoramica. In Personalizza è possibile inserire titoli di sezione, spostare riquadri e grafici, rimuovere elementi e aggiungere nuovi riquadri e grafici dalla raccolta.

![Fare clic su Edit. Spostare i grafici e i riquadri. Aggiungere i riquadri dalla raccolta. Fare quindi clic su Fine.](./media/app-insights-dashboards/020-customize.png)

## Dashboard

Il primo oggetto visualizzato dopo l'accesso al [portale di Microsoft Azure](https://portal.azure.com) è un dashboard, in cui è possibile raggruppare i grafici più importanti di tutte le risorse di Azure, inclusi i dati di telemetria di [Visual Studio Application Insights](app-insights-overview.md).
 

![Un dashboard personalizzato.](./media/app-insights-dashboards/30.png)

1. Fare clic nell'angolo superiore in qualsiasi momento per tornare al dashboard.
2. Fare clic su un grafico o un riquadro nel dashboard per visualizzare i dati più in dettaglio.
3. Usare la barra di spostamento per una panoramica completa di tutte le risorse.
4. Modificare, creare e condividere dashboard usando la barra degli strumenti del dashboard.

## Aggiungere elementi a un dashboard

I pannelli o i set di grafici particolarmente interessanti possono essere aggiunti al dashboard. Saranno visibili al successivo accesso.

![Per aggiungere un grafico, passare il mouse su di esso e fare clic su "..." nell'intestazione.](./media/app-insights-dashboards/33.png)

Si noti che i grafici sono raggruppati in riquadri e che un riquadro può contenere più di un grafico. Viene aggiunto al dashboard l'intero riquadro.

## Modificare un riquadro nel dashboard

Dopo aver aggiunto un riquadro al dashboard, è possibile modificarlo.

![Passare il mouse sopra un grafico per modificarlo.](./media/app-insights-dashboards/36.png)

1. Aggiungere un grafico al riquadro. 
2. Impostare la metrica, la dimensione group-by e lo stile (tabella, grafico) di un grafico.
3. Impostare le proprietà timespan e filter per i grafici nel riquadro.
4. Impostare il titolo del riquadro.

I riquadri aggiunti dai pannelli di Esplora metriche hanno più opzioni di modifica rispetto ai riquadri aggiunti da un pannello Panoramica.

Il riquadro originale aggiunto non è interessato dalle modifiche.


## Passare da un dashboard all'altro

È possibile salvare più dashboard e passare da un dashboard all'altro. Quando si aggiunge un grafico o un pannello, l'aggiunta viene eseguita nel dashboard corrente.

![Per passare da un dashboard all'altro, fare clic su Dashboard e selezionare un dashboard salvato. Per creare e salvare un nuovo dashboard, fare clic su Nuovo. Per modificare la disposizione, fare clic su Modifica.](./media/app-insights-dashboards/32.png)

È possibile ad esempio usare un dashboard per la visualizzazione a schermo intero nell'area del team e un altro dashboard per lo sviluppo generale.


Nel dashboard i pannelli vengono visualizzati sotto forma di riquadri: fare clic sul riquadro desiderato per passare al pannello. I grafici sono una replica dei grafici che si trovano nel percorso originale.

![Fare clic su un riquadro per aprire il pannello che rappresenta](./media/app-insights-dashboards/35.png)


## Condividere i dashboard con il team

Dopo aver creato un dashboard, è possibile condividerlo con altri utenti.


![Fare clic su Condividi nell'intestazione del dashboard](./media/app-insights-dashboards/41.png)

Altre informazioni su [ruoli e controllo di accesso](app-insights-resources-roles-access-control.md).

<!---HONumber=AcomDC_0608_2016-->