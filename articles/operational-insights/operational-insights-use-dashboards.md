<properties
	pageTitle="Dashboard di Operational Insights"
	description="Articolo informativo sull'utilizzo di base dei dashboard in Operational Insights"
	services="operational-insights"
	documentationCenter=""
	authors="ehissey"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="evanhi"/>

# Dashboard di Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Questa guida contiene informazioni sul modo in cui i dashboard di Operational Insights consentono di visualizzare tutte le ricerche log salvate, permettendo all'utente di avere una panoramica dell'ambiente mediante un unico strumento.

![Dashboard di esempio](./media/operational-insights-use-dashboards/example-dash.png)

## Come si crea un dashboard?

Per iniziare, passare a Azure Operational Insights Overview facendo clic sul pulsante Overview nel riquadro di spostamento sinistro. Sulla sinistra verrà visualizzato il riquadro "My Dashboard". Fare clic per eseguire il drill-down nel dashboard.

![Panoramica](./media/operational-insights-use-dashboards/overview.png)



## Aggiunta di un riquadro

I riquadri dei dashboard vengono compilati in base alle ricerche log salvate. Operational Insights viene fornito con diverse ricerche log salvate predefinite che ne consentono un utilizzo immediato. Nelle illustrazioni seguenti viene mostrato come iniziare.

![Illustrazione](./media/operational-insights-use-dashboards/pictorial.png)

Nella visualizzazione My Dashboard fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Nel pannello visualizzato nella parte destra della pagina sono disponibili tutte le ricerche log salvate dell'area di lavoro.

![Aggiunta di riquadri 1](./media/operational-insights-use-dashboards/add-tile1.png)

Per visualizzare una ricerca log salvata sotto forma di riquadro, è sufficiente trascinarla nello spazio vuoto a sinistra. Quando viene trascinata, la ricerca si trasforma in un riquadro.

![Aggiunta di riquadri 2](./media/operational-insights-use-dashboards/add-tile2.png)

![Aggiunta di riquadri 3](./media/operational-insights-use-dashboards/add-tile3.png)


## Modifica di un riquadro

Nella visualizzazione My Dashboard fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Fare clic sul riquadro che si desidera modificare. Il riquadro di destra cambia per consentire la modifica e fornisce diverse opzioni: ![Modifica di un riquadro](./media/operational-insights-use-dashboards/edit-tile.png)

### Tipi di visualizzazione dei riquadri#
È possibile scegliere tra due tipi di visualizzazioni dei riquadri:

**Grafico a barre** <p> ![Grafico a barre](./media/operational-insights-use-dashboards/bar-chart.png)

Viene visualizzata una cronologia dei risultati della ricerca log salvata o un elenco dei risultati per campo, a seconda che i risultati vengano aggregati o meno in base a un campo.

**Metrica** <p> ![Metrica](./media/operational-insights-use-dashboards/metric.png)

I risultati totali della ricerca log vengono visualizzati sotto forma di numero in un riquadro. È possibile impostare un valore soglia che, quando viene raggiunto, determina l'evidenziazione del riquadro.

### Soglia
È possibile creare un valore soglia in un riquadro usando la visualizzazione Metrica. Selezionare le opzioni appropriate per creare un valore soglia nel riquadro. Specificare se si desidera evidenziare il riquadro quando il valore è sopra o sotto la soglia selezionata e quindi immettere il valore soglia.

## Organizzazione del dashboard
Per organizzare il dashboard, passare alla visualizzazione My Dashboard e fare clic sul pulsante con l'ingranaggio 'customize' nella parte inferiore della pagina per avviare la modalità di personalizzazione. Fare clic e trascinare il riquadro nella posizione desiderata.

![Organizzazione del dashboard](./media/operational-insights-use-dashboards/organize.png)

## Rimozione di un riquadro
Per rimuovere un riquadro, passare alla visualizzazione My Dashboard e fare clic sul pulsante con l'ingranaggio **customize** nella parte inferiore della pagina per avviare la modalità di personalizzazione. Selezionare il riquadro che si desidera rimuovere e quindi fare clic su **Remove Tile** nel riquadro di destra. ![Rimozione di un riquadro](./media/operational-insights-use-dashboards/remove-tile.png)

<!---HONumber=August15_HO6-->