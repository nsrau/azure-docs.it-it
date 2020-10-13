---
title: Creare dashboard personalizzati in Azure Application Insights | Microsoft Docs
description: Esercitazione per la creazione di dashboard personalizzati per gli indicatori KPI con Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperfq1
ms.openlocfilehash: 1a83385c7f384f7727a0fd10e238c6511950abfe
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612725"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Creare dashboard personalizzati per gli indicatori KPI con Azure Application Insights

È possibile creare più dashboard nel portale di Azure, ognuno dei quali include riquadri per la visualizzazione dei dati di più risorse di Azure tra i diversi gruppi di risorse e le sottoscrizioni.  È possibile aggiungere diversi grafici e visualizzazioni da Azure Application Insights per creare dashboard personalizzati in grado di offrire un quadro completo dell'integrità e delle prestazioni dell'applicazione. Questa esercitazione illustra la creazione di un dashboard personalizzato che include più tipi di dati e visualizzazioni da Azure Application Insights.

 Si apprenderà come:

> [!div class="checklist"]
> * Creare un dashboard personalizzato in Azure
> * Aggiungere un riquadro dalla Raccolta riquadri
> * Aggiungere metriche standard in Application Insights al dashboard
> * Aggiungere un grafico della metrica personalizzato in Application Insights al dashboard
> * Aggiungere i risultati di una query di Log (Analisi) al dashboard

## <a name="prerequisites"></a>Prerequisites

Per completare questa esercitazione:

- Distribuire un'applicazione .NET in Azure e [abilitare Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> Le autorizzazioni necessarie per usare i dashboard sono descritte in [Informazioni sul controllo di accesso per i dashboard](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

> [!WARNING]
> Se si sposta la risorsa di Application Insights in una sottoscrizione o un gruppo di risorse diverso, sarà necessario aggiornare manualmente il dashboard rimuovendo i riquadri precedenti e bloccando i nuovi riquadri dalla stessa risorsa di Application Insights in una nuova posizione.

Un unico dashboard può contenere risorse da più applicazioni, gruppi di risorse e sottoscrizioni.  Iniziare l'esercitazione creando un nuovo dashboard per l'applicazione.  

1. Nell'elenco a discesa del menu a sinistra nel portale di Azure selezionare **Dashboard**.

    ![Elenco a discesa del menu del portale di Azure](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. Nel riquadro del dashboard selezionare **Nuovo dashboard** e quindi **Dashboard vuoto**.

   ![Nuovo dashboard](media/tutorial-app-dashboards/new-dashboard.png)

3. Digitare un nome per il dashboard.
4. La **Raccolta riquadri** include un'ampia gamma di riquadri che è possibile aggiungere al dashboard.  Oltre ad aggiungere riquadri dalla raccolta, è possibile aggiungere grafici e altre visualizzazioni direttamente da Application Insights al dashboard.
5. Individuare il riquadro **Markdown** e trascinarlo nel dashboard.  Questo riquadro consente di aggiungere testo formattato con sintassi markdown, ideale per aggiungere un testo descrittivo al dashboard. Per altre informazioni, vedere [Usare un riquadro markdown nei dashboard di Azure per visualizzare il contenuto personalizzato](../../azure-portal/azure-portal-markdown-tile.md).
6. Aggiungere il testo alle proprietà del riquadro e ridimensionarlo nel canvas del dashboard.

    [![Riquadro Modifica markdown](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Fare clic su **Fine personalizzazione** nella parte superiore della schermata per uscire dalla modalità di personalizzazione del riquadro.

## <a name="add-health-overview"></a>Aggiungere una panoramica sull'integrità

Un dashboard contenente testo statico non è molto interessante. È consigliabile aggiungere un riquadro da Application Insights per mostrare le informazioni relative all'applicazione. È possibile aggiungere riquadri di Application Insights dalla Raccolta riquadri oppure è possibile aggiungerli direttamente dalle schermate di Application Insights. In questo modo è possibile configurare grafici e visualizzazioni con cui si ha già familiarità prima di aggiungerli al dashboard.  Per iniziare, aggiungere una panoramica sull'integrità standard per l'applicazione.  Ciò non richiede alcuna configurazione e consente una personalizzazione minima del dashboard.


1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
2. Nel riquadro **Panoramica** selezionare l'icona a forma di puntina ![icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) per aggiungere il riquadro a un dashboard.
3. Nella scheda "Aggiungi al dashboard" selezionare il dashboard a cui aggiungere il riquadro o crearne uno nuovo.
 
3. In alto a destra verrà visualizzata una notifica che indica che il riquadro è stato aggiunto al dashboard.  Fare clic su **Aggiunto al dashboard** nella notifica per tornare al dashboard o usare il relativo riquadro.
4. Il riquadro è ora aggiunto al dashboard. Selezionare **Modifica** per cambiarne la posizione. Selezionarlo e trascinarlo nella posizione desiderata e quindi fare clic su **Fine personalizzazione**. Il dashboard contiene ora un riquadro con alcune informazioni utili.

    [![Dashboard in modalità di modifica](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Aggiungere un grafico della metrica personalizzato

Il pannello **Metriche** consente di rappresentare graficamente una metrica raccolta da Application Insights nel corso del tempo con filtri e raggruppamento opzionali.  Come qualsiasi altro elemento in Application Insights, è possibile aggiungere questo grafico al dashboard,  ma questa operazione richiede prima una personalizzazione minima.

1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
1. Selezionare **Metriche**.  
2. È già stato creato un grafico vuoto e all'utente viene chiesto di aggiungere una metrica.  Aggiungere una metrica al grafico e, se necessario, aggiungere un filtro e un raggruppamento.  L'esempio seguente mostra il numero di richieste server raggruppate per esito positivo,  in modo da offrire un quadro sempre aggiornato delle richieste con esito positivo e con esito negativo.

    [![Aggiungere una metrica](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Selezionare **Aggiungi al dashboard** a destra.

3.  In alto a destra verrà visualizzata una notifica che indica che il riquadro è stato aggiunto al dashboard. Fare clic su **Aggiunto al dashboard** nella notifica per tornare al dashboard o usare la relativa scheda.

4. Il riquadro è ora aggiunto al dashboard. Selezionare **Modifica** per cambiarne la posizione. Selezionarlo e trascinarlo nella posizione desiderata e quindi fare clic su **Fine personalizzazione**.

## <a name="add-logs-query"></a>Aggiungere la query sui log

Log di Azure Application Insights fornisce un linguaggio di query avanzato che consente di analizzare tutti i dati raccolti da Application Insights. Proprio come i grafici e altre visualizzazioni, è possibile aggiungere l'output di una query di Log al dashboard.

1. Selezionare la risorsa **Application Insights** nella schermata iniziale.
2. A sinistra, in "monitoraggio" selezionare **Log** per aprire la scheda Log.
3. Digitare la query seguente, che restituisce le prime 10 pagine più richieste e il relativo conteggio delle richieste:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Fare clic su **Esegui** per convalidare i risultati della query.
5. Selezionare l'icona Aggiungi ![Icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) e selezionare il nome del dashboard.

5. Prima di tornare al dashboard, aggiungere un'altra query scegliendo il rendering come grafico in modo da comprendere le diverse modalità di visualizzazione di una query su log in un dashboard. Iniziare con la query seguente che riepiloga le prime 10 operazioni con più eccezioni.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selezionare **Grafico** e quindi specificare un **grafico ad anello** per visualizzare l'output.

    [![Grafico ad anello con la query precedente](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Selezionare l'icona Aggiungi ![Icona a forma di puntina](media/tutorial-app-dashboards/pushpin.png) in alto a destra per aggiungere il grafico al dashboard e quindi tornare al dashboard.
7. I risultati delle query vengono aggiunti al dashboard nel formato selezionato. Selezionare e trascinare ogni risultato nella posizione desiderata e quindi fare clic su **Fine personalizzazione**.
8. Selezionare l'icona a forma di matita ![Icona a forma di matita](media/tutorial-app-dashboards/pencil.png) in ogni titolo per assegnare un titolo descrittivo.

## <a name="share-dashboard"></a>Condividi dashboard

1. Nella parte superiore del dashboard selezionare **Condividi** per pubblicare le modifiche.
2. Se si vuole, è possibile definire utenti specifici che devono avere accesso al dashboard. Per altre informazioni, vedere [Condividere i dashboard di Azure tramite il Controllo degli accessi in base al ruolo](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Selezionare **Pubblica**.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come creare dashboard personalizzati, esaminare il resto della documentazione di Application Insights, che include anche un case study.

> [!div class="nextstepaction"]
> [Diagnostica completa](../app/devops.md)
