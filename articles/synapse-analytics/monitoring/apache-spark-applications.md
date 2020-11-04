---
title: Monitorare Apache Spark applicazioni con sinapsi Studio
description: Usare sinapsi Studio per monitorare le applicazioni Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16b37258d922db59f520d4e30c45773f6d1108cf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341071"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Usare sinapsi Studio per monitorare le applicazioni di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Apache Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool di Apache Spark nell'area di lavoro.

Questo articolo illustra come monitorare le applicazioni Apache Spark, consentendo di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento più recenti.

Questa esercitazione illustra le attività seguenti:

* Eseguire il monitoraggio dell'applicazione Apache Spark
* Visualizza applicazione Apache Spark completata
* Visualizza Apache Spark applicazione annullata
* Debug non riuscito Apache Spark applicazione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'esercitazione, verificare che siano soddisfatti i requisiti seguenti:

- Un'area di lavoro di sinapsi Studio. Per istruzioni, vedere [creare un'area di lavoro di sinapsi Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Un pool di Apache Spark.

## <a name="view-apache-spark-applications"></a>Visualizza applicazioni Apache Spark 
È possibile visualizzare tutte le applicazioni Apache Spark da **monitoraggio**  ->  **Apache Spark applicazioni**.
   ![applicazioni Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Visualizza applicazione Apache Spark completata

Aprire **monitoraggio** , quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark completate, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

  ![Selezionare il processo completato](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Controllare le attività, **lo stato** e la **durata totali** **completati**.

2. Aggiornare la query del log.

3. Aprire il collegamento del server di cronologia di Apache Spark facendo clic su **Server cronologia Spark**.

4. Controllare le informazioni di **Riepilogo** .

5. Controllare i **log**. È possibile selezionare diversi tipi di log nell'elenco a discesa ed è possibile scaricare le informazioni di log facendo clic su **Scarica log** e selezionare la casella di controllo per filtrare gli **errori e gli avvisi** per filtrare gli errori e gli avvisi necessari.

6. È possibile visualizzare una panoramica del processo nel grafico del processo generato. Per impostazione predefinita, il grafico Mostra tutti i processi. È possibile filtrare la visualizzazione in base all' **ID del processo**.

7. Per impostazione predefinita, è selezionata la visualizzazione **stato** . È possibile controllare il flusso di dati selezionando lo **stato** di / **lettura** della / **Written** / **durata** scritta nell'elenco a discesa **Visualizza** .

8. Per riprodurre il processo, fare clic sul pulsante di **riproduzione** . È possibile fare clic sul pulsante **Arresta** in qualsiasi momento per arrestare.

9. Usare lo scorrimento o la barra di scorrimento del mouse per ingrandire e ridurre il grafico del processo. è anche possibile selezionare **zoom per adattarlo** allo schermo.

10. Nel nodo del grafico del processo vengono visualizzate le informazioni seguenti di ogni fase:

    * ID.

    * Nome o descrizione.

    * Numero totale di attività.

    * Dati letti: la somma delle dimensioni di input e delle dimensioni dei dati casuali letti.

    * Scrittura dei dati: la somma delle dimensioni di output e delle Scritture shuffle.

    * Tempo di esecuzione: tempo tra l'ora di inizio del primo tentativo e l'ora di completamento dell'ultimo tentativo.

    * Conteggio righe: somma di record di input, record di output, record di lettura casuale e record di scrittura casuale.

    * Stato.

     ![Visualizza processo completato](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Fare clic su **Visualizza dettagli** nel grafico, quindi i dettagli per la fase vengono visualizzati.

    ![dettagli per la fase](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Eseguire il monitoraggio dell'applicazione Apache Spark

Aprire **monitoraggio** , quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark in esecuzione, selezionare l'applicazione Apache Spark di invio e visualizzare i dettagli. Se il Apache Spark applicazione è ancora in esecuzione, è possibile monitorare lo stato di avanzamento.

   ![Seleziona processo in esecuzione](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Controllare le attività, **lo stato** e la **durata totali** **completati**.

2. **Annullare** l'applicazione Apache Spark.

3. **Aggiorna** Query di log.

4. Fare clic sul pulsante **dell'interfaccia utente di Spark** per passare alla pagina del processo Spark.

5. Visualizzare il grafico. È possibile visualizzare una panoramica del processo nel grafico del processo generato. Fare riferimento al passaggio 6, 7, 8, 9, 10 di [visualizzazione completata Apache Spark applicazione](#view-completed-apache-spark-application).

6. Controllare le informazioni di **Riepilogo** .

7. Controllare la diagnostica nella scheda **diagnostica** .

8. Controllare i **log** in questa scheda. È possibile selezionare diversi tipi di log nell'elenco a discesa ed è possibile scaricare le informazioni di log facendo clic su **Scarica log** e selezionare la casella di controllo per filtrare gli **errori e gli avvisi** per filtrare gli errori e gli avvisi necessari.

    ![Visualizza processo in esecuzione](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Visualizza Apache Spark applicazione annullata

Aprire **monitoraggio** , quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark annullate, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

 ![Selezionare il processo annullato](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Controllare le attività, **lo stato** e la **durata totali** **completati**.

2. Aggiornare la query di log.

3. Aprire il collegamento Server cronologia Apache facendo clic su **Server cronologia Spark**.

4. Visualizzare il grafico. È possibile visualizzare una panoramica del processo nel grafico del processo generato. Fare riferimento al passaggio 6, 7, 8, 9, 10 di [visualizzazione completata Apache Spark applicazione](#view-completed-apache-spark-application).

5. Controllare le informazioni di **Riepilogo** .

6. Controllare i **log**. È possibile selezionare diversi tipi di log nell'elenco a discesa ed è possibile scaricare le informazioni di log facendo clic su **Scarica** log e selezionare la casella di controllo per filtrare gli **errori e gli avvisi** per filtrare gli errori e gli avvisi necessari.

7. Fare clic su **Visualizza dettagli** nel grafico, quindi i dettagli per la fase vengono visualizzati.

   ![Visualizza processo annullato](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debug non riuscito Apache Spark applicazione

Aprire **monitoraggio** , quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark non riuscite, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

![selezione processo non riuscito](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Controllare le attività, **lo stato** e la **durata totali** **completati**.

2. Aggiornare la query del log.

3. Aprire il collegamento del server di cronologia di Apache Spark facendo clic su **Server cronologia Spark**.

4. Visualizzare il grafico. È possibile visualizzare una panoramica del processo nel grafico del processo generato. Fare riferimento al passaggio 6, 7, 8, 9, 10 di [visualizzazione Apache Spark applicazione completata](#view-completed-apache-spark-application)

5. Controllare le informazioni di **Riepilogo** .

6. Controllare le informazioni sull'errore.

   ![informazioni sul processo non riuscite](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni di pipeline con sinapsi Studio](how-to-monitor-pipeline-runs.md) .  
