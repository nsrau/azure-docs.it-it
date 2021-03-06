---
title: Integrazione del dashboard di Power BI con Analisi di flusso di Azure
description: Questo articolo descrive come usare un dashboard di Power BI in tempo reale per visualizzare i dati al di fuori di un processo di Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4e3f31442c5fa645e27a640d8facf86aed20aa75
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006696"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Analisi di flusso e Power BI: un dashboard di analisi in tempo reale per il flusso di dati

Analisi di flusso di Azure permette di usare uno dei principali strumenti di Business Intelligence, [Microsoft Power BI](https://powerbi.com/). Questo articolo illustra come creare strumenti di Business Intelligence usando Power BI come output per i processi di Analisi di flusso di Azure. Si apprenderà anche come creare e usare un dashboard in tempo reale costantemente aggiornato dal processo di analisi di flusso.

Questo articolo continua dall'esercitazione sul [rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md) in Analisi di flusso. Esso si basa sul flusso di lavoro creato in tale esercitazione e aggiunge un output di Power BI in modo che sia possibile visualizzare le chiamate telefoniche fraudolente rilevate da un processo di Analisi di flusso. 

È disponibile [un video](https://www.youtube.com/watch?v=SGUpT-a99MA) che illustra questo scenario.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Un account Azure.
* Un account per Power BI Pro. È possibile usare un account aziendale o dell'istituto di istruzione.
* Una versione completata dell'esercitazione sul [rilevamento delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md). L'esercitazione include un'app che genera metadati di telefonate fittizie. Nell'esercitazione si crea un hub eventi e si inviano i dati delle telefonate all'hub eventi. Si scrive una query che rileva le chiamate fraudolente (chiamate effettuate dallo stesso numero nello stesso momento da posizioni diverse). 


## <a name="add-power-bi-output"></a>Aggiungere l’output Power BI
Nell'esercitazione sul rilevamento delle frodi in tempo reale l'output viene inviato all'archiviazione BLOB di Azure. In questa sezione si aggiunge un output che invia informazioni a Power BI.

1. Nel portale di Azure aprire il processo di Analisi di flusso creato in precedenza. Se si usa il nome suggerito, il processo è denominato `sa_frauddetection_job_demo`.

2. Nel menu a sinistra selezionare **Output** in **Topologia processo**. Selezionare quindi **+ Aggiungi** e scegliere **Power BI** dal menu a discesa.

3. Selezionare **+ Aggiungi** > **Power BI**. Compilare quindi il modulo con i dettagli seguenti e selezionare **autorizza** per usare la propria identità utente per connettersi a Power BI (il token è valido per 90 giorni). 

>[!NOTE]
>Per i processi di produzione, è consigliabile connettersi per [usare identità gestita per autenticare il processo di analisi di flusso di Azure per Power bi](./powerbi-output-managed-identity.md).

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Alias di output  |  CallStream-PowerBI  |
   |Nome del set di dati  |   sa-dataset  |
   |Nome tabella |  fraudulent-calls  |

   ![Configurare l'output Analisi di flusso di Azure](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Se Power BI dispone di un set di dati e di una tabella con gli stessi nomi specificati nel processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
   > È consigliabile non creare in modo esplicito il set di dati e la tabella nell'account di Power BI. Vengono creati automaticamente quando il processo di Analisi di flusso viene avviato e inizia a generare output in Power BI. Se la query del processo non genera alcun risultato, il set di dati e la tabella non vengono creati.
   >

4. Quando si seleziona **Autorizza**, viene visualizzata una finestra popup in cui viene chiesto di fornire le credenziali per l'autenticazione dell'account di Power BI. Una volta completato il processo di autorizzazione, fare clic su **Salva** per salvare le impostazioni.

8. Fare clic su **Crea**.

Il set di dati viene creato con le impostazioni seguenti:

* **defaultRetentionPolicy: BasicFIFO**: dati FIFO, con un massimo di 200.000 righe.
* **defaultMode: Hybrid:** il set di dati supporta sia i riquadri di streaming (noti anche come push) sia gli oggetti visivi tradizionali basati su report. Per il contenuto push, in questo caso i dati vengono continuamente aggiornati dal processo di analisi di flusso, senza la necessità di pianificare l'aggiornamento dal lato Power BI.

Attualmente non è possibile creare set di dati con altri flag.

Per altre informazioni sui set di dati di Power BI, vedere le informazioni di riferimento sull'[API REST di Power BI](/rest/api/power-bi/).


## <a name="write-the-query"></a>Scrivere la query

1. Chiudere il pannello **Output** e tornare al pannello del processo.

2. Fare clic sulla casella **Query**. 

3. Immettere la query seguente. Questa query è simile alla query self-join che è stata creata nell'esercitazione sul rilevamento delle frodi. La differenza è che questa query invia i risultati al nuovo output creato (`CallStream-PowerBI`). 

    >[!NOTE]
    >Se non è stato assegnato un nome all'input `CallStream` nell'esercitazione sul rilevamento delle frodi, sostituire il nome per `CallStream` nelle clausole **FROM** e **JOIN** della query.

   ```SQL
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
   ```

4. Fare clic su **Salva**.


## <a name="test-the-query"></a>Testare la query

Questa sezione è facoltativa ma consigliata. 

1. Se l'app TelcoStreaming non è attualmente in esecuzione, avviarla seguendo questa procedura:

    * Aprire il prompt dei comandi.
    * Passare alla cartella in cui si trovano il file telcogenerator.exe e il file modificato telcodatagen.exe.config.
    * Eseguire il comando seguente:

       `telcodatagen.exe 1000 .2 2`

2. Nella pagina **Query** del processo di Analisi di flusso fare clic sui puntini di sospensione accanto all'input `CallStream` e quindi selezionare **Carica dati di esempio da input**.

3. Specificare che si desidera ottenere i dati di tre minuti e fare clic su **OK**. Attendere fino a quando non si riceve la notifica che i dati sono stati campionati.

4. Fare clic su **Test** e rivedere i risultati.

## <a name="run-the-job"></a>Eseguire il processo

1. Verificare che l'app TelcoStreaming sia in esecuzione.

2. Passare alla pagina **Panoramica** del processo di Analisi di flusso e selezionare **Avvia**.

    ![Avviare il processo di analisi di flusso](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Il processo di Analisi di flusso inizia a cercare le chiamate fraudolente nel flusso in ingresso. Il processo crea anche il set di dati e la tabella in Power BI e inizia a inviare loro i dati relativi alle chiamate fraudolente.


## <a name="create-the-dashboard-in-power-bi"></a>Creare il dashboard in Power BI

1. Passare a [Powerbi.com](https://powerbi.com) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso produce risultati, si noterà che il set di dati è già creato:

    ![Posizione del set di dati di streaming in Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Nell'area di lavoro fare clic su **+&nbsp;Crea**.

    ![Pulsante Crea nell'area di lavoro di Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Creare un nuovo dashboard e denominarlo `Fraudulent Calls`.

    ![Creare un dashboard e assegnargli un nome nell'area di lavoro di Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Nella parte superiore della finestra, fare clic su **Aggiungi riquadro**, selezionare **DATI IN STREAMING PERSONALIZZATI**, quindi fare clic su **Avanti**.

    ![Riquadro del set di dati di streaming personalizzato in Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. In **YOUR DATSETS** (SET DI DATI PERSONALI) selezionare il set di dati interessato e quindi fare clic su **Avanti**.

    ![Il set di dati di streaming in Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. In **Tipo di visualizzazione** selezionare **Scheda** e quindi nell'elenco **Campi** selezionare **fraudulentcalls**.

    ![Dettagli di visualizzazione per il nuovo riquadro](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Fare clic su **Avanti**.

8. Immettere i dettagli del riquadro, ad esempio un titolo e un sottotitolo.

    ![Titolo e sottotitolo per il nuovo riquadro](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Fare clic su **Applica**.

    Si dispone a questo punto di un contatore di frodi.

    ![Contatore di frodi nel dashboard di Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Seguire di nuovo la procedura per aggiungere un riquadro (partendo dal passaggio 4). Questa volta eseguire le operazioni seguenti:

    * Quando si accede a **Tipo di visualizzazione** selezionare **Grafico a linee**. 
    * Aggiungere un asse e selezionare **windowend**. 
    * Aggiungere un valore e selezionare **fraudulentcalls**.
    * Per **Intervallo di tempo da visualizzare**  selezionare gli ultimi 10 minuti.

      ![Creare il riquadro per il grafico a linee in Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Fare clic su **Avanti**, aggiungere un titolo e un sottotitolo e fare clic su **Applica**.

     Il dashboard di Power BI offre ora due visualizzazioni di dati sulle chiamate fraudolente in base al rilevamento nei dati di streaming.

     ![Dashboard di Power BI completo che mostra due riquadri per le chiamate fraudolente](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>Informazioni sulle limitazioni e le procedure consigliate
Attualmente, è possibile chiamare Power BI approssimativamente una volta al secondo. Gli oggetti visivi di streaming supportano pacchetti da 15 KB. Oltre tale limite, gli oggetti visivi di streaming hanno esito negativo, ma il push continua a funzionare. A causa di queste limitazioni, Power BI è più adatto ai casi in cui Analisi di flusso di Azure riduce notevolmente il carico di dati. È consigliabile usare la finestra a cascata o la finestra di salto per assicurarsi che il push di dati avvenga al massimo una volta al secondo e che la query rientri nei requisiti di velocità effettiva.

Per calcolare il valore e visualizzare la finestra in secondi, è possibile usare questa equazione:

![Equazione per calcolare il valore e visualizzare la finestra in secondi](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Ad esempio:

* Sono presenti 1.000 dispositivi che inviano dati a intervalli di un secondo.
* Si usa lo SKU di Power BI Pro che supporta 1.000.000 righe l'ora.
* Si vuole pubblicare la quantità di dati medi per ogni dispositivo in Power BI.

L'equazione diventa quindi la seguente:

![Equazione basata su criteri di esempio](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Con questa configurazione è possibile modificare la query originale come segue:

```SQL
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
```

### <a name="renew-authorization"></a>Rinnovare l'autorizzazione
Se la password è stata modificata dopo la creazione o l'ultima autenticazione del processo, è necessario autenticare nuovamente l'account Power BI. Se Azure AD Multi-Factor Authentication è configurato nel tenant Azure Active Directory (Azure AD), è necessario rinnovare Power BI autorizzazione ogni due settimane. Se non viene rinnovata, si potrebbero verificare problemi come la mancanza di output dei processi o un `Authenticate user error` nei log delle operazioni.

Se si tenta di avviare un processo dopo che il token è scaduto, si verifica un errore e l'avvio del processo ha esito negativo. Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Power BI. Per evitare la perdita di dati, fare clic sul collegamento **Rinnova autorizzazione** e riavviare il processo selezionando **Ora ultimo arresto**.

Dopo aver aggiornato l'autorizzazione con Power BI, viene visualizzato un avviso verde nell'area di autorizzazione che indica che il problema è stato risolto.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Output di Analisi di flusso](stream-analytics-define-outputs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)
* [Usare l'identità gestita per autenticare il processo di analisi di flusso di Azure per Power BI](./powerbi-output-managed-identity.md)
