---
title: Creare dashboard personalizzati in Azure Application Insights | Microsoft Docs
description: Esercitazione per la creazione di dashboard personalizzati per gli indicatori KPI con Azure Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 47d8eaff5f154e198c277ec2b63a2d09e66f7180
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900498"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Creare dashboard personalizzati per gli indicatori KPI con Azure Application Insights

È possibile creare più dashboard nel portale di Azure, ognuno dei quali include riquadri per la visualizzazione dei dati di più risorse di Azure tra i diversi gruppi di risorse e le sottoscrizioni.  È possibile aggiungere diversi grafici e visualizzazioni da Azure Application Insights per creare dashboard personalizzati in grado di offrire un quadro completo dell'integrità e delle prestazioni dell'applicazione. Questa esercitazione illustra la creazione di un dashboard personalizzato che include più tipi di dati e visualizzazioni da Azure Application Insights.  Si apprenderà come:

> [!div class="checklist"]
> * Creare un dashboard personalizzato in Azure
> * Aggiungere un riquadro dalla Raccolta riquadri
> * Aggiungere metriche standard in Application Insights al dashboard
> * Aggiungere un grafico della metrica personalizzato in Application Insights al dashboard
> * Aggiungere i risultati di una query di Log (Analisi) al dashboard



## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard
Un unico dashboard può contenere risorse da più applicazioni, gruppi di risorse e sottoscrizioni.  Iniziare l'esercitazione creando un nuovo dashboard per l'applicazione.  

1. Nel riquadro del dashboard selezionare **Nuovo dashboard**.

   ![Nuovo dashboard](media/tutorial-app-dashboards/1newdashboard.png)

1. Digitare un nome per il dashboard.
1. La **Raccolta riquadri** include un'ampia gamma di riquadri che è possibile aggiungere al dashboard.  Oltre ad aggiungere riquadri dalla raccolta, è possibile aggiungere grafici e altre visualizzazioni direttamente da Application Insights al dashboard.
1. Individuare il riquadro **Markdown** e trascinarlo nel dashboard.  Questo riquadro consente di aggiungere testo formattato con sintassi markdown, ideale per aggiungere un testo descrittivo al dashboard.
1. Aggiungere il testo alle proprietà del riquadro e ridimensionarlo nel canvas del dashboard.
    
    ![Riquadro Modifica markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Fare clic su **Fine personalizzazione** nella parte superiore dello schermo per uscire dalla modalità di personalizzazione del riquadro.

## <a name="add-health-overview"></a>Aggiungere una panoramica sull'integrità
Un dashboard contenente testo statico non è molto interessante. È consigliabile aggiungere un riquadro da Application Insights per mostrare le informazioni relative all'applicazione.  È possibile aggiungere riquadri di Application Insights dalla Raccolta riquadri oppure è possibile aggiungerli direttamente dalle schermate di Application Insights.  In questo modo è possibile configurare grafici e visualizzazioni con cui si ha già familiarità prima di aggiungerli al dashboard.  Per iniziare, aggiungere una panoramica sull'integrità standard per l'applicazione.  Ciò non richiede alcuna configurazione e consente una personalizzazione minima del dashboard.


1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
2. Nel riquadro **Panoramica** fare clic sull'icona a forma di puntina ![icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) per aggiungere il riquadro all'ultimo dashboard visualizzato.  
 
3. In alto a destra verrà visualizzata una notifica che indica che il riquadro è stato aggiunto al dashboard. Fare clic sul testo della notifica per tornare nel dashboard o usare il relativo riquadro **.**
4. Il riquadro è ora aggiunto al dashboard. Selezionare **Modifica** per cambiarne la posizione. Fare clic e trascinarlo e quindi fare clic su **Fine personalizzazione**. Il dashboard contiene ora un riquadro con alcune informazioni utili.

    ![Dashboard con il riquadro Panoramica sequenza temporale](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Aggiungere un grafico della metrica personalizzato
Il pannello **Metriche** consente di rappresentare graficamente una metrica raccolta da Application Insights nel corso del tempo con filtri e raggruppamento opzionali.  Come qualsiasi altro elemento in Application Insights, è possibile aggiungere questo grafico al dashboard,  ma questa operazione richiede prima una personalizzazione minima.

1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
1. Selezionare **Metriche**.  
2. È già stato creato un grafico vuoto e all'utente viene chiesto di aggiungere una metrica.  Aggiungere una metrica al grafico e, se necessario, aggiungere un filtro e un raggruppamento.  L'esempio seguente mostra il numero di richieste server raggruppate per esito positivo,  in modo da offrire un quadro sempre aggiornato delle richieste con esito positivo e con esito negativo.

    ![Aggiungere una metrica](media/tutorial-app-dashboards/metrics.png)

4. Selezionare **Aggiungi al dashboard** a destra. La visualizzazione verrà aggiunta all'ultimo dashboard usato.

3.  In alto a destra verrà visualizzata una notifica che indica che il riquadro è stato aggiunto al dashboard. Fare clic sul testo della notifica per tornare nel dashboard o usare il relativo pannello **.**

4. Il riquadro è ora aggiunto al dashboard. Selezionare **Modifica** per cambiarne la posizione. Fare clic e trascinarlo e quindi fare clic su **Fine personalizzazione**.

## <a name="add-logs-analytics-query"></a>Aggiungere una query di Log (Analisi)
Log (Analisi) di Azure Application Insights fornisce un linguaggio di query avanzato che consente di analizzare tutti i dati raccolti da Application Insights. Proprio come i grafici e altre visualizzazioni, è possibile aggiungere l'output di una query di Log al dashboard.

Dal momento che Log (Analisi) di Azure Application Insights è un servizio distinto, è necessario condividere il dashboard in modo che possa includere una query di Log. Quando si condivide un dashboard di Azure, lo si pubblica come risorsa di Azure, rendendolo disponibile ad altri utenti e risorse.  

1. Nella parte superiore della schermata del dashboard fare clic su **Condividi**.

    ![Pubblicare dashboard](media/tutorial-app-dashboards/8dashboard-share.png)

2. Lasciare invariato il **Nome del dashboard** e selezionare il **Nome sottoscrizione** per condividere il dashboard.  Fare clic su **Pubblica**.  Il dashboard è ora disponibile per altri servizi e sottoscrizioni.  Se si vuole, è possibile definire utenti specifici che devono avere accesso al dashboard.
1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
2. Fare clic su **Log (Analisi)** a sinistra sotto Monitoraggio per aprire il portale di Log (Analisi).
3. Digitare la query seguente, che restituisce le prime 10 pagine più richieste e il relativo conteggio delle richieste:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Fare clic su **Esegui** per convalidare i risultati della query.
5. Fare clic sull'icona a forma di puntina ![Icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) e selezionare il nome del dashboard. Il motivo per cui questa opzione richiede che l'utente selezioni un dashboard, a differenza dei passaggi precedenti in cui veniva usato l'ultimo dashboard, è che la console di Log (Analisi) è un servizio distinto che è necessario selezionare tra i dashboard condivisi disponibili.

5. Prima di tornare al dashboard, aggiungere un'altra query, ma questa volta eseguendone il rendering come grafico in modo da comprendere le diverse modalità di visualizzazione di una query di Log in un dashboard. Iniziare con la query seguente che riepiloga le prime 10 operazioni con più eccezioni.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selezionare **Grafico** e quindi specificare un **grafico ad anello** per visualizzare l'output.

    ![Grafico di Log (Analisi)](media/tutorial-app-dashboards/11querychart.png)

6. Fare clic sull'icona a forma di puntina ![Icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) in alto a destra per aggiungere il grafico al dashboard e questa volta selezionare il collegamento per tornare al dashboard.
4. I risultati delle query vengono aggiunti al dashboard nel formato selezionato.  Fare clic e trascinare ognuno di essi in posizione e quindi fare clic su **Fine personalizzazione**.
5. Selezionare l'icona a forma di matita ![Icona a forma di matita](media/tutorial-app-dashboards/pencil.png) in ogni titolo per assegnare un titolo descrittivo.

5. Fare clic su **Condividi** per ripubblicare le modifiche nel dashboard, che include ora un'ampia gamma di grafici e visualizzazioni di Application Insights.


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso come creare dashboard personalizzati, esaminare il resto della documentazione di Application Insights, che include anche un case study.

> [!div class="nextstepaction"]
> [Diagnostica completa](../../azure-monitor/app/devops.md)
