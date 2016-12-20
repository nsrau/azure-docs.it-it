---
title: Creare un dashboard personalizzato in Log Analytics | Documentazione Microsoft
description: Questa guida spiega in che modo i dashboard di Log Analytics visualizzano tutte le ricerche nei log salvate, offrendo un punto di vista unico su tutto l&quot;ambiente.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: afd1e5359edbdfdbd4a8afaac139f5877b306c2b


---
# <a name="create-a-custom-dashboard-in-log-analytics"></a>Creare un dashboard personalizzato in Log Analytics
Questa guida spiega in che modo i dashboard di Log Analytics visualizzano tutte le ricerche log salvate, offrendo un punto di vista unico su tutto l'ambiente.

![Dashboard di esempio](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Tutti i dashboard personalizzati creati nel portale di OMS sono anche disponibili nell'app OMS per dispositivi mobili. Vedere le pagine seguenti per altre informazioni sulle app.

* [App OMS per dispositivi mobili in Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [App OMS per dispositivi mobili in Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![dashboard mobile](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Come si crea un dashboard?
Per iniziare, passare alla pagina di panoramica di OMS. Sulla sinistra verrà visualizzato il riquadro **Dashboard**. Fare clic per eseguire il drill-down nel dashboard.

![Panoramica](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Aggiunta di un riquadro
I riquadri dei dashboard vengono compilati in base alle ricerche log salvate. OMS offre diverse ricerche nei log salvate predefinite che ne consentono un utilizzo immediato. La procedura seguente descrive come iniziare.

Nella visualizzazione Dashboard fare semplicemente clic su **Personalizza** per passare alla modalità di personalizzazione.

![Illustrazione](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Nel pannello visualizzato nella parte destra della pagina sono disponibili tutte le ricerche log salvate dell'area di lavoro. Per visualizzare una ricerca log salvata sotto forma di riquadro, passare il mouse su una ricerca salvata e quindi fare clic sul simbolo **più**.

![Aggiunta di riquadri 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Quando si fa clic sul segno **più** viene visualizzato un nuovo riquadro nella visualizzazione Dashboard.

![Aggiunta di riquadri 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Modifica di un riquadro
Nella visualizzazione Dashboard fare semplicemente clic su **Personalizza** per passare alla modalità di personalizzazione. Fare clic sul riquadro che si desidera modificare. Il pannello destro cambia per consentire la modifica e offre diverse opzioni:

![Modifica di un riquadro](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Modifica di un riquadro](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Tipi di visualizzazione dei riquadri
È possibile scegliere tra tre tipi di visualizzazione dei riquadri:

| tipo di grafico | funzione |
| --- | --- |
| ![Grafico a barre](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Visualizza una sequenza temporale dei risultati della ricerca log salvata sotto forma di grafico a barre o un elenco dei risultati per campo, a seconda che i risultati vengano aggregati o meno in base a un campo. |
| ![metrica](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Il risultato totale della ricerca nei log viene visualizzato sotto forma di numero in un riquadro. È possibile impostare un valore soglia che, quando viene raggiunto, determina l'evidenziazione del riquadro. |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |Visualizza una sequenza temporale dei risultati della ricerca log salvata con i relativi valori sotto forma di grafico a linee. |

### <a name="threshold"></a>Soglia
È possibile creare un valore soglia in un riquadro usando la visualizzazione Metrica. Selezionare le opzioni appropriate per creare un valore soglia nel riquadro. Specificare se si desidera evidenziare il riquadro quando il valore è sopra o sotto la soglia selezionata e quindi immettere il valore soglia.

## <a name="organizing-the-dashboard"></a>Organizzazione del dashboard
Per organizzare il dashboard, passare alla visualizzazione Dashboard e fare clic su **Personalizza** per passare alla modalità di personalizzazione. Fare clic e trascinare il riquadro nella posizione desiderata.

![Organizzazione del dashboard](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Rimozione di un riquadro
Per rimuovere un riquadro, passare alla visualizzazione Dashboard e fare clic su **Personalizza** per passare alla modalità di personalizzazione. Selezionare il riquadro che si vuole rimuovere e fare clic su **Rimuovi riquadro**nel pannello destro.

![Rimozione di un riquadro](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Passaggi successivi
* Creare [avvisi](log-analytics-alerts.md) in Log Analytics per generare notifiche e risolvere i problemi.




<!--HONumber=Nov16_HO3-->


