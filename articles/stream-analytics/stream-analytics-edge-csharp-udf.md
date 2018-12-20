---
title: Scrivere una funzione C# definita dall'utente per un processo Edge di Analisi di flusso di Azure in Visual Studio (anteprima)
description: Informazioni su come scrivere funzioni C# definite dall'utente per i processi Edge di Analisi di flusso in Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5597109a65a8af88bf286977d039656635565ed9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086905"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Esercitazione: scrivere una funzione C# definita dall'utente per un processo Edge di Analisi di flusso di Azure (anteprima)

Le funzioni C# definite dall'utente create in Visual Studio consentono di estendere il linguaggio di query di Analisi di flusso di Azure con le proprie funzioni. È possibile riutilizzare il codice esistente (incluse le DLL) e usare logica matematica o complessa con C#. Vi sono tre modi per implementare le funzioni definite dall'utente: file CodeBehind in un progetto di Analisi di flusso, funzioni definite dall'utente da un progetto C# locale o funzioni definite dall'utente da un pacchetto esistente da un account di archiviazione. Questa esercitazione usa il metodo CodeBehind per implementare una funzione C# di base. La funzionalità per le funzioni definite dall'utente per i processi Edge di Analisi di flusso di Azure è attualmente in anteprima e non deve essere usata nei carichi di lavoro di produzione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una funzione C# definita dall'utente con CodeBehind.
> * Testare il progetto Edge di Analisi di flusso in locale.
> * Pubblicare il processo Edge in Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che i prerequisiti seguenti siano disponibili:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installare gli [strumenti di Analisi di flusso per Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e i carichi di lavoro **Sviluppo di Azure** o **Elaborazione ed archiviazione dati**.
* Vedere la [guida esistente per lo sviluppo di processi Edge di Analisi di flusso](stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Creare un contenitore nell'account di archiviazione di Azure

Il contenitore creato verrà usato per archiviare il pacchetto C# compilato e distribuire il pacchetto al dispositivo IoT Edge. Usare un contenitore dedicato per ogni processo di Analisi di flusso. Non è supportato il riutilizzo dello stesso contenitore per più processi Edge di Analisi di flusso. Se è già disponibile un account di archiviazione con contenitori esistenti, è possibile usarli. In caso contrario, sarà necessario [creare un nuovo contenitore](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Creare un progetto Edge di Analisi di flusso in Visual Studio

1. Avviare Visual Studio.

2. Fare clic su **File > Nuovo > Progetto**.

3. Nell'elenco dei modelli a sinistra selezionare **Analisi di flusso** e quindi **Azure Stream Analytics Edge Application** (Applicazione Edge di Analisi di flusso di Azure).

4.  Inserire i valori appropriati per il progetto in **Nome**, **Percorso** e **Nome soluzione** e scegliere **OK**.

    ![Creare un progetto Edge di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Configurare il percorso del pacchetto dell'assembly

1. Aprire Visual Studio e passare a **Esplora soluzioni**.

2. Fare doppio clic sul file di configurazione del processo, `EdgeJobConfig.json`.

3. Espandere la sezione **Configurazione di codice definito dall'utente** e compilare la configurazione con i seguenti valori suggeriti:

    |**Impostazione**  |**Valore consigliato**  |
    |---------|---------|
    |Origine assembly  |  Riferimento al progetto locale o CodeBehind   |
    |Risorsa  |  Scegliere i dati dall'account corrente   |
    |Sottoscrizione  |  Scegliere la propria sottoscrizione.   |
    |Account di archiviazione  |  Scegliere l'account di archiviazione.   |
    |Contenitore  |  Scegliere il contenitore creato nell'account di archiviazione.   |

    ![Configurazione del progetto Edge di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Scrivere una funzione C# definita dall'utente con CodeBehind
Un file CodeBehind è un file C# associato a un singolo script di query Edge di Analisi di flusso di Azure. Gli strumenti di Visual Studio comprimeranno automaticamente il file CodeBehind e lo caricheranno nell'account di archiviazione di Azure dopo l'invio. Tutte le classi devono essere definite come *public* e tutti gli oggetti devono essere definiti come *static public*.

1. In **Esplora soluzioni** espandere **Script.asql** per trovare il file CodeBehind **Script.asaql.cs**.

2. Sostituire il codice con l'esempio seguente:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementare la funzione definita dall'utente

1. In **Esplora soluzioni** aprire il file **Script.asaql**.

2. Sostituire la query esistente con la seguente:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Test locale

1. Scaricare il [file di dati di esempio di simulatore di temperatura](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) Edge.

2. In **Esplora soluzioni** espandere **Input**, fare clic con il pulsante destro del mouse su **Input.json** e quindi scegliere **Aggiungi input locale**.

   ![Aggiungere un input locale al processo di Analisi di flusso in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Specificare il percorso del file di input locale per i dati di esempio scaricati e fare clic su **Salva**.

    ![Configurazione dell'input locale per il processo di Analisi di flusso in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Fare clic su **Esegui localmente** nell'editor di script. Dopo che l'esecuzione locale ha salvato correttamente i risultati di output, premere un tasto qualsiasi per visualizzare i risultati in formato tabella. 

    ![Eseguire il processo di Analisi di flusso di Azure in locale con Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. È anche possibile selezionare **Apri cartella risultati** per visualizzare i file non elaborati in formato JSON e CSV.

    ![Visualizzare i risultati del processo di Analisi di flusso di Azure locale con Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Eseguire il debug di una funzione definita dall'utente
È possibile eseguire il debug della funzione C# definita dall'utente in locale esattamente come si esegue il debug di codice C# standard. 

1. Aggiungere punti di interruzione nella funzione C#.

    ![Aggiungere punti di interruzione nella funzione di Analisi di flusso definita dall'utente in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Premere **F5** per avviare il debug. Il programma verrà interrotto in corrispondenza dei punti di interruzione nel modo previsto.

    ![Visualizzare i risultati di debug della funzione di Analisi di flusso definita dall'utente](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Pubblicare il processo in Azure
Dopo aver testato la query in locale, selezionare **Invia ad Azure** nell'editor di script per pubblicare il processo in Azure.

![Inviare il progetto Edge di Analisi di flusso in Azure da Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Distribuire nei dispositivi IoT Edge
Il processo di Analisi di flusso è ora pronto per essere distribuito come modulo di IoT Edge. Seguire la [guida introduttiva di IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) per creare un hub IoT, registrare un dispositivo IoT Edge, quindi installare e avviare il runtime di IoT Edge nel dispositivo. Seguire poi l'esercitazione per [distribuire il processo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) per distribuire il processo di Analisi di flusso come modulo IoT Edge. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una semplice funzione C# definita dall'utente con CodeBehind, quindi il processo è stato pubblicato in Azure e poi distribuito ai dispositivi IoT Edge tramite il portale dell'hub IoT. 

Per altre informazioni sulle diverse modalità per usare le funzioni C# definite dall'utente per i processi Edge di Analisi di flusso, continuare con questo articolo:

> [!div class="nextstepaction"]
> [Scrivere funzioni C# definite dall'utente per Analisi di flusso di Azure](stream-analytics-edge-csharp-udf-methods.md)
