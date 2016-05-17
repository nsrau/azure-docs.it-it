<properties
	pageTitle="Creare un dashboard personalizzato in Log Analytics | Microsoft Azure"
	description="Questa guida spiega in che modo i dashboard di Log Analytics visualizzano tutte le ricerche nei log salvate, offrendo un punto di vista unico su tutto l'ambiente."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Creare un dashboard personalizzato in Log Analytics

Questa guida spiega in che modo i dashboard di Log Analytics visualizzano tutte le ricerche nei log salvate, offrendo un punto di vista unico su tutto l'ambiente.

![Dashboard di esempio](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## Come si crea un dashboard?

Per iniziare aprire la panoramica di OMS facendo clic sul pulsante Panoramica nel riquadro di spostamento sinistro. Sulla sinistra verrà visualizzato il riquadro "My Dashboard". Fare clic per eseguire il drill-down nel dashboard.

![Panoramica](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## Aggiunta di un riquadro

I riquadri dei dashboard vengono compilati in base alle ricerche log salvate. OMS offre diverse ricerche nei log salvate predefinite che ne consentono un utilizzo immediato. Nelle illustrazioni seguenti viene mostrato come iniziare.

![Illustrazione](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

Nella visualizzazione My Dashboard fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Nel pannello visualizzato nella parte destra della pagina sono disponibili tutte le ricerche log salvate dell'area di lavoro.

![Aggiunta di riquadri 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

Per visualizzare una ricerca log salvata sotto forma di riquadro, è sufficiente trascinarla nello spazio vuoto a sinistra. Quando viene trascinata, la ricerca si trasforma in un riquadro.

![Aggiunta di riquadri 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![Aggiunta di riquadri 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## Modifica di un riquadro

Nella visualizzazione My Dashboard fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Fare clic sul riquadro che si desidera modificare. Il pannello destro cambia per consentire la modifica e offre diverse opzioni:

![Modifica di un riquadro](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### Tipi di visualizzazione dei riquadri#
È possibile scegliere tra due tipi di visualizzazioni dei riquadri:

|tipo di grafico|funzione|
|---|---|
|![Grafico a barre](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|Mostra una cronologia dei risultati della ricerca nei log salvata o un elenco dei risultati per campo, a seconda che i risultati vengano aggregati o meno in base a un campo.
|![metrica](./media/log-analytics-dashboards/oms-dashboards-metric.png)|Il risultato totale della ricerca nei log viene visualizzato sotto forma di numero in un riquadro. È possibile impostare un valore soglia che, quando viene raggiunto, determina l'evidenziazione del riquadro.|

### Soglia
È possibile creare un valore soglia in un riquadro usando la visualizzazione Metrica. Selezionare le opzioni appropriate per creare un valore soglia nel riquadro. Specificare se si desidera evidenziare il riquadro quando il valore è sopra o sotto la soglia selezionata e quindi immettere il valore soglia.

## Organizzazione del dashboard
Per organizzare il dashboard, passare alla visualizzazione My Dashboard e fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Fare clic e trascinare il riquadro nella posizione desiderata.

![Organizzazione del dashboard](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## Rimozione di un riquadro
Per rimuovere un riquadro, passare alla visualizzazione My Dashboard e fare clic sul pulsante con l'ingranaggio **customize** nella parte inferiore della pagina per avviare la modalità di personalizzazione. Selezionare il riquadro che si vuole rimuovere e fare clic su **Rimuovi riquadro**nel pannello destro.

![Rimozione di un riquadro](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## Passaggi successivi

- Creare avvisi in Log Analytics per generare le notifiche e per la risoluzione dei problemi.

<!---HONumber=AcomDC_0504_2016-->