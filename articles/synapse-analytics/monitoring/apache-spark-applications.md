---
title: Monitorare applicazioni Apache Spark
description: Usare Azure sinapsi Studio per monitorare le applicazioni Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 20c4216e7af0cb4100204ebae1b484a53e3c50a6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971349"
---
# <a name="use-the-azure-synapse-studio-to-monitor-your-apache-spark-applications"></a>Usare Azure sinapsi Studio per monitorare le applicazioni di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Apache Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool di Apache Spark nell'area di lavoro.

Questo articolo illustra come monitorare le applicazioni Apache Spark, consentendo di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento più recenti.

Questa esercitazione illustra le attività seguenti:

* Eseguire il monitoraggio dell'applicazione Apache Spark
* Visualizza applicazione Apache Spark completata
* Visualizza Apache Spark applicazione annullata
* Debug non riuscito Apache Spark applicazione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'esercitazione, verificare che siano soddisfatti i requisiti seguenti:

- Un'area di lavoro di Azure sinapsi Studio. Per istruzioni, vedere [creare un'area di lavoro di Azure sinapsi Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Un pool di Apache Spark.

## <a name="monitor-running-apache-spark-application"></a>Eseguire il monitoraggio dell'applicazione Apache Spark

Aprire **monitoraggio**, quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark in esecuzione, selezionare l'applicazione Apache Spark di invio e visualizzare i dettagli. Se il Apache Spark applicazione è ancora in esecuzione, è possibile monitorare lo stato di avanzamento.

  ![Seleziona processo in esecuzione](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Controllare le attività, **lo stato**e la **durata totali** **completati**.

2. Annullare l'applicazione Apache Spark.

3. Aggiornare la query del log.

4. Visualizzare il grafico.

5. Controllare le informazioni di **Riepilogo** .

6. Controllare i **log**. Le informazioni sul log sono vuote durante l'esecuzione.

    ![Visualizza processo in esecuzione](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Visualizza applicazione Apache Spark completata

Aprire **monitoraggio**, quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark completate, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

  ![Selezionare il processo completato](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Controllare le attività, **lo stato**e la **durata totali** **completati**.

2. Aggiornare la query del log.

3. Aprire il collegamento del server di cronologia di Apache Spark facendo clic su **Server cronologia Spark**.

4. Controllare le informazioni di **Riepilogo** facendo clic sull'icona nel grafico.

5. Controllare i **log**. È possibile selezionare diversi tipi di log nell'elenco a discesa ed è possibile scaricare le informazioni di log facendo clic su **Scarica log**.

6. È possibile visualizzare una panoramica del processo nel grafico del processo generato. Per impostazione predefinita, il grafico Mostra tutti i processi. È possibile filtrare la visualizzazione in base all' **ID del processo**.

7. Per impostazione predefinita, è selezionata la visualizzazione **stato** . È possibile controllare il flusso di dati selezionando **Leggi** o **scritto** nell'elenco a discesa **Visualizza** .

8. Per riprodurre il processo, selezionare **riproduzione**. È possibile selezionare **Arresta** in qualsiasi momento per arrestare.

9. Usare lo scorrimento del mouse per eseguire lo zoom avanti e indietro nel grafico del processo oppure selezionare **zoom per adattarlo** allo schermo.

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
    
11. Fare clic sul grafico, quindi i dettagli per la fase vengono visualizzati.

   ![dettagli per la fase](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Visualizza Apache Spark applicazione annullata

Aprire **monitoraggio**, quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark annullate, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

 ![Selezionare il processo annullato](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Controllare le attività, **lo stato**e la **durata totali** **completati**.

2. Aggiornare la query di log.

3. Aprire il collegamento Server cronologia Apache facendo clic su **Server cronologia Spark**.

4. Visualizzare il grafico.

5. Controllare le informazioni di **Riepilogo** .

6. Controllare i **log**. È possibile selezionare diversi tipi di log nell'elenco a discesa ed è possibile scaricare le informazioni di log facendo clic su **Scarica log**.

   ![Visualizza processo annullato](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debug non riuscito Apache Spark applicazione

Aprire **monitoraggio**, quindi selezionare **Apache Spark applicazioni**. Per visualizzare i dettagli relativi alle applicazioni Apache Spark non riuscite, selezionare l'applicazione Apache Spark e visualizzare i dettagli.

![selezione processo non riuscito](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Controllare le attività, **lo stato**e la **durata totali** **completati**.

2. Aggiornare la query del log.

3. Aprire il collegamento del server di cronologia di Apache Spark facendo clic su **Server cronologia Spark**.

4. Visualizzare il grafico.

5. Controllare le informazioni di **Riepilogo** .

6. Controllare le informazioni sull'errore.

   ![informazioni sul processo non riuscite](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare la pipeline di esecuzione di Azure sinapsi Studio](how-to-monitor-pipeline-runs.md) .  
