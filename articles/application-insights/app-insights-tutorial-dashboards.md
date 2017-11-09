---
title: Creare dashboard personalizzati in Azure Application Insights | Microsoft Docs
description: Esercitazione per la creazione di dashboard personalizzati per gli indicatori KPI con Azure Application Insights.
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0d2f98ca2fb39289b2916ddd24590924856507d6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Creare dashboard personalizzati per gli indicatori KPI con Azure Application Insights

È possibile creare più dashboard nel portale di Azure, ognuno dei quali include riquadri per la visualizzazione dei dati di più risorse di Azure tra i diversi gruppi di risorse e le sottoscrizioni.  È possibile aggiungere diversi grafici e visualizzazioni da Azure Application Insights per creare dashboard personalizzati in grado di offrire un quadro completo dell'integrità e delle prestazioni dell'applicazione.  Questa esercitazione illustra la creazione di un dashboard personalizzato che include più tipi di dati e visualizzazioni da Azure Application Insights.  Si apprenderà come:

> [!div class="checklist"]
> * Creare un dashboard personalizzato in Azure
> * Aggiungere un riquadro dalla Raccolta riquadri
> * Aggiungere metriche standard in Application Insights al dashboard 
> * Aggiungere un grafico della metrica personalizzato in Application Insights al dashboard
> * Aggiungere i risultati di una query di Analytics al dashboard 



## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard
Un unico dashboard può contenere risorse da più applicazioni, gruppi di risorse e sottoscrizioni.  Iniziare l'esercitazione creando un nuovo dashboard per l'applicazione.  

2.  Nella schermata principale del portale selezionare **Nuovo dashboard**.

    ![Nuovo dashboard](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Digitare un nome per il dashboard.
4. La **Raccolta riquadri** include un'ampia gamma di riquadri che è possibile aggiungere al dashboard.  Oltre ad aggiungere riquadri dalla raccolta, è possibile aggiungere grafici e altre visualizzazioni direttamente da Application Insights al dashboard.
5. Individuare il riquadro **Markdown** e trascinarlo nel dashboard.  Questo riquadro, che consente di aggiungere testo formattato in markdown, è lo strumento ideale per aggiungere un testo descrittivo al dashboard.
6. Aggiungere il testo alle proprietà del riquadro e ridimensionarlo nel canvas del dashboard.
    
    ![Riquadro Modifica markdown](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Fare clic su **Fine personalizzazione** nella parte superiore della schermata per uscire dalla modalità di personalizzazione del riquadro e quindi su **Pubblica modifiche** per salvare le modifiche.

    ![Dashboard con il riquadro Markdown](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Aggiungere una panoramica sull'integrità
Un dashboard contenente solo testo statico non è molto interessante. È consigliabile aggiungere un riquadro da Application Insights per mostrare le informazioni relative all'applicazione.  È possibile aggiungere riquadri di Application Insights dalla Raccolta riquadri oppure è possibile aggiungerli direttamente dalle schermate di Application Insights.  In questo modo è possibile configurare grafici e visualizzazioni con cui si ha già familiarità prima di aggiungerli al dashboard.  Per iniziare, aggiungere una panoramica sull'integrità standard per l'applicazione.  Ciò non richiede alcuna configurazione e consente una personalizzazione minima del dashboard.


1. Selezionare **Application Insights** nel menu di Azure e quindi selezionare l'applicazione.
2. In **Panoramica sequenza temporale** selezionare il menu di scelta rapida e scegliere **Aggiungi al dashboard**.  Il riquadro verrà aggiunto all'ultimo dashboard visualizzato.  

    ![Aggiungere il riquadro Panoramica sequenza temporale](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. Nella parte superiore della schermata fare clic su **Visualizza dashboard** per tornare al dashboard.
4. Il riquadro Panoramica sequenza temporale verrà aggiunto al dashboard.  Fare clic e trascinarlo in posizione e quindi fare clic su **Fine personalizzazione** e **Pubblica modifiche**.  Il dashboard contiene ora un riquadro con alcune informazioni utili.

    ![Dashboard con il riquadro Panoramica sequenza temporale](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Aggiungere un grafico della metrica personalizzato
Il pannello **Metriche** consente di rappresentare graficamente una metrica raccolta da Application Insights nel corso del tempo con filtri e raggruppamento opzionali.  Come qualsiasi altro elemento in Application Insights, è possibile aggiungere questo grafico al dashboard,  ma questa operazione richiede prima una personalizzazione minima.

1. Selezionare **Application Insights** nel menu di Azure e quindi selezionare l'applicazione.
1. Selezionare **Metriche**.  
2. È già stato creato un grafico vuoto e all'utente viene chiesto di aggiungere una metrica.  Aggiungere una metrica al grafico e, se necessario, aggiungere un filtro e un raggruppamento.  L'esempio seguente mostra il numero di richieste server raggruppate per esito positivo,  in modo da offrire un quadro sempre aggiornato delle richieste con esito positivo e con esito negativo.

    ![Aggiungere una metrica](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Selezionare il menu di scelta rapida per il grafico e scegliere **Aggiungi al dashboard**.  La visualizzazione verrà aggiunta all'ultimo dashboard usato.

    ![Aggiungere un grafico della metrica](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. Nella parte superiore della schermata fare clic su **Visualizza dashboard** per tornare al dashboard.

4. Il riquadro Panoramica sequenza temporale verrà aggiunto al dashboard.  Fare clic e trascinarlo in posizione e quindi fare clic su **Fine personalizzazione** e infine su **Pubblica modifiche**. 

    ![Dashboard con metriche](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Esplora metriche
**Esplora metriche** è simile a Metriche, anche se consente una personalizzazione notevolmente maggiore se aggiunto al dashboard.  La scelta del riquadro da usare per la rappresentazione grafica delle metriche dipende dalle specifiche preferenze ed esigenze.

1. Selezionare **Application Insights** nel menu di Azure e quindi selezionare l'applicazione.
1. Selezionare **Esplora metriche**. 
2. Fare clic per modificare il grafico, selezionare una o più metriche e, se necessario, una configurazione dettagliata.  L'esempio mostra un grafico a linee che tiene traccia del tempo di risposta medio della pagina.
3. Fare clic sull'icona della puntina in alto a destra per aggiungere il grafico al dashboard e quindi trascinarlo in posizione.

    ![Esplora metriche](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. Il riquadro Esplora metriche consente una maggiore personalizzazione una volta aggiunto al dashboard.  Fare clic con il pulsante destro del mouse sul riquadro e scegliere **Modifica titolo** per aggiungere un titolo personalizzato.  Proseguire e apportare altre personalizzazioni, se si vuole.

    ![Dashboard con Esplora metriche](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Il grafico di Esplora metriche è ora aggiunto al dashboard.

    ![Dashboard con Esplora metriche](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Aggiungere una query di Analytics
Analytics in Azure Application Insights fornisce un linguaggio di query avanzato che consente di analizzare tutti i dati raccolti da Application Insights.  Proprio come i grafici e altre visualizzazioni, è possibile aggiungere l'output di una query di Analytics al dashboard.   

Dal momento che Analytics in Azure Application Insights è un servizio distinto, è necessario condividere il dashboard in modo che possa includere una query di Analytics.  Quando si condivide un dashboard di Azure, lo si pubblica come una risorsa di Azure, rendendolo disponibile ad altri utenti e risorse.  

1. Nella parte superiore della schermata del dashboard fare clic su **Condividi**.

    ![Pubblicare dashboard](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Lasciare invariato il **Nome del dashboard** e selezionare il **Nome sottoscrizione** per condividere il dashboard.  Fare clic su **Pubblica**.  Il dashboard è ora disponibile per altri servizi e sottoscrizioni.  Se si vuole, è possibile definire utenti specifici che devono avere accesso al dashboard.
1. Selezionare **Application Insights** nel menu di Azure e quindi selezionare l'applicazione.
2. Fare clic su **Analytics** nella parte superiore della schermata per aprire il portale di Analytics.

    ![Avviare Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Digitare la query seguente, che restituisce le prime 10 pagine più richieste e il relativo conteggio delle richieste:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Fare clic su **Vai** per convalidare i risultati della query.
5. Fare clic sull'icona della puntina e selezionare il nome del dashboard.  Il motivo per cui questa opzione richiede che l'utente selezioni un dashboard, a differenza dei passaggi precedenti in cui veniva usato l'ultimo dashboard, è che la console di Analytics è un servizio distinto che richiede una selezione da tutti i dashboard condivisi disponibili.

    ![Aggiungere una query di Analytics](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Prima di tornare al dashboard, aggiungere un'altra query, eseguendone il rendering come grafico in modo da comprendere le diverse modalità di visualizzazione di una query di Analytics in un dashboard.  Iniziare con la query seguente che riepiloga le prime 10 operazioni con più eccezioni.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Selezionare **Grafico** e quindi specificare un **grafico ad anello** per visualizzare l'output.

    ![Grafico di Analytics](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Fare clic sull'icona della puntina per aggiungere il grafico al dashboard e questa volta selezionare il collegamento per tornare al dashboard.
4. I risultati delle query vengono aggiunti al dashboard nel formato selezionato.  Fare clic e trascinare ognuno di essi in posizione e quindi fare clic su **Modifica completata**.
5. Fare clic con il pulsante destro del mouse su ognuno dei riquadri e selezionare **Modifica titolo** per assegnare un titolo descrittivo.

    ![Dashboard con Analytics](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Fare clic su **Pubblica modifiche** per eseguire il commit delle modifiche nel dashboard, che include ora un'ampia gamma di grafici e visualizzazioni da Application Insights.


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come creare dashboard personalizzati, esaminare il resto della documentazione di Application Insights, che include anche un case study.

> [!div class="nextstepaction"]
> [Diagnostica completa](app-insights-devops.md)