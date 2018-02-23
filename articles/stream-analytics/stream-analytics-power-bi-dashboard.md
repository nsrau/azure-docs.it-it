---
title: Dashboard di Power BI in Analisi di flusso di Azure | Microsoft Docs
description: Utilizzare un dashboard di Power BI streaming in tempo reale per raccogliere intelligence aziendali e analizzare i dati di volumi elevati di un processo di Analisi di flusso.
keywords: dashboard di analisi, dashboard in tempo reale
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: b446e2296f2747012849936b994c4a4a2044869e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Analisi di flusso e Power BI: dashboard di analisi in tempo reale per lo streaming dei dati
Analisi di flusso di Azure permette di usare uno dei principali strumenti di Business Intelligence, [Microsoft Power BI](https://powerbi.com/). Questo articolo illustra come creare strumenti di Business Intelligence usando Power BI come output per i processi di Analisi di flusso di Azure. Illustra anche come creare e usare un dashboard in tempo reale.

Questo articolo continua dall'esercitazione sul [rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md) in Analisi di flusso. Esso si basa sul flusso di lavoro creato in tale esercitazione e aggiunge un output di Power BI in modo che sia possibile visualizzare le chiamate telefoniche fraudolente rilevate da un processo di Analisi di flusso. 

È disponibile [un video](https://www.youtube.com/watch?v=SGUpT-a99MA) che illustra questo scenario.


## <a name="prerequisites"></a>prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Un account Azure.
* Un account per Power BI. È possibile usare un account aziendale o dell'istituto di istruzione.
* Una versione completata dell'esercitazione sul [rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md). L'esercitazione include un'app che genera metadati di telefonate fittizie. Nell'esercitazione si crea un hub eventi e si inviano i dati delle telefonate all'hub eventi. Si scrive una query che rileva le chiamate fraudolente (chiamate effettuate dallo stesso numero nello stesso momento da posizioni diverse). 


## <a name="add-power-bi-output"></a>Aggiungere l’output Power BI
Nell'esercitazione sul rilevamento delle frodi in tempo reale l'output viene inviato all'archiviazione BLOB di Azure. In questa sezione si aggiunge un output che invia informazioni a Power BI.

1. Nel portale di Azure aprire il processo di Analisi di flusso creato in precedenza. Se si usa il nome suggerito, il processo è denominato `sa_frauddetection_job_demo`.

2. Selezionare la casella **Output** al centro del dashboard del processo e quindi selezionare **+ Aggiungi**.

3. In **Alias di output** immettere `CallStream-PowerBI`. È possibile usare un nome diverso. In questo caso, tenerne traccia, poiché sarà necessario in un secondo momento. 

4. In **Sink** selezionare **Power BI**.

   ![Creare un output per Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Fare clic su **Autorizza**.

    Verrà visualizzata una finestra in cui è possibile specificare le credenziali di Azure per l'account aziendale o dell'istituto di istruzione. 

    ![Immettere le credenziali per l'accesso a Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Immettere le credenziali. È necessario considerare che quando si immettono le credenziali, si autorizza il processo di Analisi di flusso ad accedere all'area di Power BI.

7. Quando si torna al pannello **Nuovo output** immettere le informazioni seguenti:

    * **Area di lavoro del gruppo**: selezionare un'area di lavoro nel tenant Power BI in cui si desidera creare il set di dati.
    * **Nome set di dati**: immettere `sa-dataset`. È possibile usare un nome diverso. In questo caso prenderne nota perché servirà in un momento successivo.
    * **Nome tabella**: immettere `fraudulent-calls`. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati.

    ![Area di lavoro PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Se Power BI dispone di un set di dati e di una tabella con gli stessi nomi specificati nel processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
    > È consigliabile non creare in modo esplicito il set di dati e la tabella nell'account di Power BI. Vengono creati automaticamente quando il processo di Analisi di flusso viene avviato e inizia a generare output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati.
    >

8. Fare clic su **Crea**.

Il set di dati viene creato con le impostazioni seguenti:

* **defaultRetentionPolicy: BasicFIFO**: dati FIFO, con un massimo di 200.000 righe.
* **defaultMode: pushStreaming**: il set di dati supporta sia i riquadri in streaming che gli oggetti visivi tradizionali basati sui report, detti anche push.

Attualmente non è possibile creare set di dati con altri flag.

Per altre informazioni sui set di dati di Power BI, vedere le informazioni di riferimento sull'[API REST di Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-the-query"></a>Scrivere la query

1. Chiudere il pannello **Output** e tornare al pannello del processo.

2. Fare clic sulla casella **Query**. 

3. Immettere la query seguente. Questa query è simile alla query self-join che è stata creata nell'esercitazione sul rilevamento delle frodi. La differenza è che questa query invia i risultati al nuovo output creato (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se non è stato assegnato un nome all'input `CallStream` nell'esercitazione sul rilevamento delle frodi, sostituire il nome per `CallStream` nelle clausole **FROM** e **JOIN** della query.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Fare clic su **Save**.


## <a name="test-the-query"></a>Testare la query
Questa sezione è facoltativa ma consigliata. 

1. Se l'app TelcoStreaming non è attualmente in esecuzione, avviarla seguendo questa procedura:

    * Aprire una finestra di comando.
    * Passare alla cartella in cui si trovano il file telcogenerator.exe e il file modificato telcodatagen.exe.config.
    * Eseguire il comando seguente:

            telcodatagen.exe 1000 .2 2

2. Nel pannello **Query** fare clic sui puntini di sospensione accanto all'input `CallStream` e quindi selezionare **Campiona dati da input**.

3. Specificare che si desidera ottenere i dati di tre minuti e fare clic su **OK**. Attendere fino a quando non si riceve la notifica che i dati sono stati campionati.

4. Fare clic su **Test** e assicurarsi di ottenere i risultati.


## <a name="run-the-job"></a>Eseguire il processo

1. Assicurarsi che l'app TelcoStreaming sia in esecuzione.

2. Chiudere il pannello **Query**.

3. Nel pannello del processo fare clic su **Avvia**.

    ![Avviare il processo di analisi di flusso](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Il processo di Analisi di flusso inizia a cercare le chiamate fraudolente nel flusso in ingresso. Il processo crea anche il set di dati e la tabella in Power BI e inizia a inviare loro i dati relativi alle chiamate fraudolente.


## <a name="create-the-dashboard-in-power-bi"></a>Creare il dashboard in Power BI

1. Passare a [Powerbi.com](https://powerbi.com) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso produce risultati, si noterà che il set di dati è già creato:

    ![Streaming del set di dati in Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Nell'area di lavoro fare clic su **+&nbsp;Crea**.

    ![Pulsante Crea nell'area di lavoro di Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Creare un nuovo dashboard e denominarlo `Fraudulent Calls`.

    ![Creare un dashboard e assegnargli un nome nell'area di lavoro di Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Nella parte superiore della finestra, fare clic su **Aggiungi riquadro**, selezionare **DATI IN STREAMING PERSONALIZZATI**, quindi fare clic su **Avanti**.

    ![Set di dati di streaming personalizzato](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. In **YOUR DATSETS** (SET DI DATI PERSONALI) selezionare il set di dati interessato e quindi fare clic su **Avanti**.

    ![Set di dati di streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. In **Tipo di visualizzazione** selezionare **Scheda** e quindi nell'elenco **Campi** selezionare **fraudulentcalls**.

    ![Dettagli di visualizzazione per il nuovo riquadro](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Fare clic su **Avanti**.

8. Immettere i dettagli del riquadro, ad esempio un titolo e un sottotitolo.

    ![Titolo e sottotitolo per il nuovo riquadro](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Fare clic su **Apply**.

    Si dispone a questo punto di un contatore di frodi.

    ![Contatore di frodi](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Seguire di nuovo la procedura per aggiungere un riquadro (partendo dal passaggio 4). Questa volta eseguire le operazioni seguenti:

    * Quando si accede a **Tipo di visualizzazione** selezionare **Grafico a linee**. 
    * Aggiungere un asse e selezionare **windowend**. 
    * Aggiungere un valore e selezionare **fraudulentcalls**.
    * Per **Intervallo di tempo da visualizzare**  selezionare gli ultimi 10 minuti.

    ![Creare il riquadro per il grafico a linee](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Fare clic su **Avanti**, aggiungere un titolo e un sottotitolo e fare clic su **Applica**.

    Il dashboard di Power BI offre ora due visualizzazioni di dati sulle chiamate fraudolente in base al rilevamento nei dati di streaming.

    ![Dashboard di Power BI completo che mostra due riquadri per le chiamate fraudolente](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Altre informazioni su Power BI

Questa esercitazione mostra come creare solo alcuni tipi di visualizzazioni per un set di dati. Power BI consente di creare altri strumenti di business intelligence per clienti per l'organizzazione. Per altre informazioni, vedere le risorse seguenti:

* Per visualizzare un altro esempio di dashboard Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .
* Per altre informazioni sulla configurazione dell'output di Analisi di flusso in Power BI e sull'uso dei gruppi di Power BI, vedere la sezione [Power BI](stream-analytics-define-outputs.md#power-bi) dell'articolo relativo agli [output di Analisi di flusso](stream-analytics-define-outputs.md). 
* Per informazioni sull'utilizzo di Power BI in generale, vedere [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Informazioni sulle limitazioni e le procedure consigliate
Attualmente, è possibile chiamare Power BI approssimativamente una volta al secondo. Gli oggetti visivi di streaming supportano pacchetti da 15 KB. Oltre tale limite, gli oggetti visivi di streaming hanno esito negativo, ma il push continua a funzionare. A causa di queste limitazioni, Power BI è più adatto ai casi in cui Analisi di flusso di Azure riduce notevolmente il carico di dati. È consigliabile usare la finestra a cascata o la finestra di salto per assicurarsi che il push di dati avvenga al massimo una volta al secondo e che la query rientri nei requisiti di velocità effettiva.

Per calcolare il valore e visualizzare la finestra in secondi, è possibile usare questa equazione:

![Equazione 1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Ad esempio: 

* Sono presenti 1.000 dispositivi che inviano dati a intervalli di un secondo.
* Si usa lo SKU di Power BI Pro che supporta 1.000.000 righe l'ora.
* Si vuole pubblicare la quantità di dati medi per ogni dispositivo in Power BI.

L'equazione diventa quindi la seguente:

![Equazione 2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Con questa configurazione è possibile modificare la query originale come segue:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Rinnovare l'autorizzazione
Se la password è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account Power BI. Se Azure Multi-Factor Authentication è configurato nel tenant di Azure Active Directory (Azure AD), è anche necessario rinnovare l'autorizzazione di Power BI ogni due settimane. Se non viene rinnovata, si potrebbero verificare problemi come la mancanza di output dei processi o un `Authenticate user error` nei log delle operazioni.

Se si tenta di avviare un processo dopo che il token è scaduto, si verifica un errore e l'avvio del processo ha esito negativo. Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Per evitare la perdita di dati, fare clic sul collegamento **Rinnova autorizzazione** e riavviare il processo selezionando **Ora ultimo arresto**.

Dopo aver aggiornato l'autorizzazione con Power BI, viene visualizzato un avviso verde nell'area di autorizzazione che indica che il problema è stato risolto.

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
