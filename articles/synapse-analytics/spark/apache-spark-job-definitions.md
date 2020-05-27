---
title: 'Esercitazione: Apache Spark per Azure Synapse Analytics: Definizione del processo Apache Spark per Synapse'
description: 'Esercitazione: Usare Azure Synapse Analytics per creare definizioni di processi Spark e inviarle ad Apache Spark per un pool di Azure Synapse Analytics.'
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587936"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Esercitazione: Usare Azure Synapse Analytics per creare definizioni di processi Apache Spark per i pool di Synapse Spark

Questa esercitazione illustra come usare Azure Synapse Analytics per creare definizioni di processi Spark e quindi inviarle a un pool di Synapse Spark. È possibile usare il plug-in in vari modi:

* Sviluppare e inviare una definizione di processo Spark in un pool di Synapse Spark.
* Visualizzare i dettagli del processo dopo l'invio.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Sviluppare e inviare una definizione di processo Spark in un pool di Synapse Spark.
> * Visualizzare i dettagli del processo dopo l'invio.

## <a name="prerequisites"></a>Prerequisiti

* Area di lavoro di Azure Synapse Analytics. Per le istruzioni, vedere [Creare un'area di lavoro di Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Introduzione

Prima di inviare la definizione di un processo Spark, è necessario essere il proprietario dei dati del BLOB di archiviazione del file system ADLS Gen2 che si vuole usare. Se non lo si è, è necessario aggiungere l'autorizzazione manualmente.

### <a name="scenario-1-add-permission"></a>Scenario 1: Aggiungere l'autorizzazione

1. Aprire [Microsoft Azure](https://ms.portal.azure.com), quindi aprire l'account di archiviazione.

2. Fare clic su **Contenitori**, quindi creare un **File system**. In questa esercitazione viene usato `sparkjob`.

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Aprire `sparkjob`, fare clic sulla scheda **Controllo di accesso (IAM)** , su **Aggiungi** e selezionare **Aggiungi assegnazione di ruolo**.

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Fare clic **Assegnazioni di ruolo**, immettere il nome utente e quindi verificare il ruolo dell'utente.

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: Preparare la struttura di cartelle

Prima di inviare una definizione di processo Spark, è necessario caricare i file in ADLS Gen2 e preparare la struttura di cartelle. Per archiviare i file, verrà usato il nodo di archiviazione in Synapse Studio.

1. Aprire [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Fare clic su **Dati**, selezionare **Account di archiviazione** e caricare i file pertinenti nel file system ADLS Gen2. Sono supportati Scala, Java, .NET e Python. Questa esercitazione usa l'esempio illustrato nella figura ai fini della dimostrazione. È possibile modificare la struttura del progetto nel modo desiderato.

    ![Impostare il valore della definizione di processo Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Creare una definizione di processo Spark

1. Aprire [Azure Synapse Analytics](https://web.azuresynapse.net/) e selezionare **Sviluppo**.

2. Selezionare **Definizioni di processo Spark** dal riquadro sinistro.

3. Fare clic sul nodo **Azioni** a destra di "Definizioni di processo Spark".

     ![Creare una nuova definizione di processo Spark](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Dall'elenco a discesa **Azioni** selezionare **New Spark job definition** (Nuova definizione di processo Spark)

     ![Creare una nuova definizione di processo Spark](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Nella finestra New Spark job definition (Nuova definizione di processo Spark) selezionare il linguaggio e quindi specificare le informazioni seguenti:  

   * Per **Linguaggio** selezionare **Spark(Scala)** .

    |  Proprietà   | Descrizione   |  
    | ----- | ----- |  
    |Nome definizione processo| Specificare un nome per la definizione di processo Spark.  In questa esercitazione viene usato `job definition sample`. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato.|  
    |File di definizione principale| File principale usato per il processo. Selezionare un file JAR dalla risorsa di archiviazione. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione. |
    |Nome della classe principale| Identificatore completo o classe principale inclusa nel file di definizione principale.|
    |Argomenti della riga di comando| Argomenti facoltativi per il processo.|
    |File di riferimento| File aggiuntivi usati come riferimento nel file di definizione principale. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
    |Pool Spark| Il processo verrà inviato al pool di Spark selezionato.|
    |Versione di Spark| Versione di Spark in esecuzione nel pool di Spark.|
    |Executors| Numero di executor da assegnare al pool di Spark specificato per il processo.|
    |Dimensioni executor| Numero di core e memoria da usare per gli executor specificati nel pool di Spark specificato per il processo.|  
    |Dimensioni driver| Numero di core e memoria da usare per il driver specificato nel pool di Spark specificato per il processo.|

    ![Impostare il valore della definizione di processo Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Per **Linguaggio** selezionare **PySpark(Python)** .

    |  Proprietà   | Descrizione   |  
    | ----- | ----- |  
    |Nome definizione processo| Specificare un nome per la definizione di processo Spark.  In questa esercitazione viene usato `job definition sample`. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato.|  
    |File di definizione principale| File principale usato per il processo. Selezionare un file PY dalla risorsa di archiviazione. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
    |Argomenti della riga di comando| Argomenti facoltativi per il processo.|
    |File di riferimento| File aggiuntivi usati come riferimento nel file di definizione principale. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
    |Pool Spark| Il processo verrà inviato al pool di Spark selezionato.|
    |Versione di Spark| Versione di Spark in esecuzione nel pool di Spark.|
    |Executors| Numero di executor da assegnare al pool di Spark specificato per il processo.|
    |Dimensioni executor| Numero di core e memoria da usare per gli executor specificati nel pool di Spark specificato per il processo.|  
    |Dimensioni driver| Numero di core e memoria da usare per il driver specificato nel pool di Spark specificato per il processo.|

    ![Impostare il valore della definizione di processo Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Per **Linguaggio** selezionare **.NET Spark(C#/F#)** .

    |  Proprietà   | Descrizione   |  
    | ----- | ----- |  
    |Nome definizione processo| Specificare un nome per la definizione di processo Spark.  In questa esercitazione viene usato `job definition sample`. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato.|  
    |File di definizione principale| File principale usato per il processo. Selezionare un file ZIP che contiene l'applicazione .NET per Spark (ovvero il file eseguibile principale, le DLL contenenti le funzioni definite dall'utente e altri file necessari) dalla risorsa di archiviazione. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
    |File eseguibile principale| File eseguibile principale nel file ZIP della definizione principale.|
    |Argomenti della riga di comando| Argomenti facoltativi per il processo.|
    |File di riferimento| File aggiuntivi necessari per i nodi di lavoro per l'esecuzione di .NET per l'applicazione Spark che non è incluso nel file ZIP di definizione principale (ovvero, file JAR dipendenti, DLL di funzioni definite dall'utente aggiuntive e altri file di configurazione). È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
    |Pool Spark| Il processo verrà inviato al pool di Spark selezionato.|
    |Versione di Spark| Versione di Spark in esecuzione nel pool di Spark.|
    |Executors| Numero di executor da assegnare al pool di Spark specificato per il processo.|
    |Dimensioni executor| Numero di core e memoria da usare per gli executor specificati nel pool di Spark specificato per il processo.|  
    |Dimensioni driver| Numero di core e memoria da usare per il driver specificato nel pool di Spark specificato per il processo.|

    ![Impostare il valore della definizione di processo Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selezionare **Pubblica** per salvare la definizione del processo Spark.

    ![Pubblicare una definizione di processo Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Inviare una definizione di processo Spark

Dopo aver creato una definizione di processo Spark, è possibile inviarla a un pool di Synapse Spark. Prima di eseguire gli esempi di questa parte, verificare di aver completato i passaggi descritti in **Attività iniziali**.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: Inviare la definizione di processo Spark

1. Per aprire una finestra di definizione di processo Spark, fare clic su di essa.

      ![Aprire la definizione di processo Spark da inviare ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Fare clic sull'icona **Invia** per inviare il progetto al pool di Spark selezionato. È possibile fare clic sulla scheda **Spark monitoring URL** (URL di monitoraggio Spark) per visualizzare la query su log dell'applicazione Spark.

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: Visualizzare lo stato di esecuzione del processo Spark

1. Fare clic su **Monitoraggio**, quindi selezionare l'opzione **Spark Applications** (Applicazioni Spark). Verrà visualizzata l'applicazione Spark inviata.

    ![Visualizzare l'applicazione Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Fare quindi clic sull'applicazione Spark per visualizzare la finestra **LogQuery** (Query su log). È possibile visualizzare lo stato di esecuzione del processo da **LogQuery** (Query su log).

    ![Visualizzare la finestra LogQuery (Query su log) per l'applicazione Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Verificare il file di output

 1. Fare clic su **Dati**, quindi selezionare **Account di archiviazione**. Una volta completata l'esecuzione, è possibile passare all'account di archiviazione ADLS Gen2 e controllare gli output generati.

    ![Visualizzare il file di output](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come usare Azure Synapse Analytics per creare definizioni di processi Spark e quindi inviarle a un pool di Synapse Spark. Successivamente, sarà possibile usare Azure Synapse Analytics per creare set di dati di Power BI e gestire i dati di Power BI. 

- [Connettersi ai dati in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualizzare con Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
