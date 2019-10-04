---
title: Processi Edge di Analitica di Stream in strumenti di Azure Stream Analitica per Visual Studio
description: Questo articolo descrive come creare, eseguire il debug e creazione di Analitica di Stream in dispositivi perimetrali IoT processi con gli strumenti di Stream Analitica per Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 1601bf6c73d9f3450959773c85385bc8ef907a66
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329952"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Sviluppare processi Edge di Analisi di flusso usando gli strumenti di Visual Studio

In questa esercitazione descrive come usare gli strumenti di Stream Analitica per Visual Studio. Informazioni su come creare ed eseguire il debug, i processi Edge di Analitica Stream. Dopo aver creato e testato il processo, è possibile passare al portale di Azure per distribuirlo ai dispositivi. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Installare [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), o [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata.  

* Seguire le [istruzioni di installazione](stream-analytics-tools-for-visual-studio-edge-jobs.md) per installare gli strumenti di Analisi di flusso per Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Creare un progetto Edge di analisi di flusso 

In Visual Studio selezionare **File** > **Nuovo** > **Progetto**. Passare all'elenco **Modelli** a sinistra > espandere **Analisi di flusso di Azure** > **Stream Analytics Edge** (Analisi di flusso - Edge)  > **Azure Stream Analytics Edge Application** (Applicazione Edge di Analisi di flusso). Specificare un nome per il progetto, il percorso e il nome della soluzione, quindi scegliere **OK**.

![Nuovo progetto di Stream Analitica Edge in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Dopo avere creato il progetto, passare a **Esplora soluzioni** per visualizzare la gerarchia di cartelle.

![Visualizzazione Esplora soluzioni del processo Edge di Analisi di flusso](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Scegliere la sottoscrizione corretta

1. Nel menu **Visualizza** di Visual Studio selezionare **Esplora server**.  

2. Fare clic con il pulsante destro del mouse su **Azure** > Selezionare **Connetti a sottoscrizione di Microsoft Azure** > e quindi accedere con l'account di Azure.

## <a name="define-inputs"></a>Definire gli input

1. In **Esplora soluzioni** espandere il nodo **Input**. Dovrebbe essere presente un input denominato **EdgeInput.json**. Fare doppio clic per visualizzare le relative impostazioni.  

2. Impostare Tipo di origine su **Flusso dati**. Quindi impostare Origine su **Hub Edge**, Formato di serializzazione eventi su **Json** e Codifica su **UTF8**. Facoltativamente, è possibile rinominare l'**Alias di input**. In questo esempio viene lasciato invariato. Se si rinomina l'alias di input, usare il nome specificato quando si definisce la query. Selezionare **Salva** per salvare le impostazioni.  
   ![Configurazione dell'input processo di Analisi di flusso](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definire gli output

1. In **Esplora soluzioni** espandere il nodo **Output**. Dovrebbe essere presente un input denominato **EdgeOutput.json**. Fare doppio clic per visualizzare le relative impostazioni.  

2. Assicurarsi di impostare Sink da selezionare **Hub di Edge**, impostare il formato di serializzazione eventi **Json**, impostare la codifica a **UTF8**e impostare formato **matrice**. Facoltativamente, è possibile rinominare l'**Alias di output**. In questo esempio viene lasciato invariato. Se si rinomina l'alias di output, usare il nome specificato quando si definisce la query. Selezionare **Salva** per salvare le impostazioni. 
   ![Configurazione dell'output processo di Analisi di flusso](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definire la query di trasformazione

I processi di Analitica Stream distribuiti negli ambienti di Stream Analitica IoT Edge supportano la maggior parte delle [riferimenti al linguaggio di Query Analitica Stream](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Tuttavia, le operazioni seguenti non sono ancora supportate per i processi Edge di Analitica Stream: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Altri operatori | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>JavaScript UDF</li><li>Aggregazioni definite dall'utente (UDA)</li><li>GetMetadataPropertyValue</li><li>Uso di più di 14 aggregati in un unico passaggio</li></ul>   |

Quando si crea un processo di Stream Analitica Edge nel portale, il compilatore visualizzerà automaticamente un avviso se non si usa un operatore supportato.

Da Visual Studio definire la query di trasformazione seguente nell'editor di query (**file script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testare il processo in locale

Per testare la query in locale, è necessario caricare i dati di esempio. È possibile ottenere dati di esempio scaricando i dati di registrazione dal [repository GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e salvandoli nel computer locale. 

1. Per caricare i dati di esempio, fare clic con il pulsante destro del mouse sul file **EdgeInput.json** e scegliere **Aggiungi input locale**  

2. Nella finestra popup > fare clic su **Sfoglia** per passare ai dati di esempio nel percorso locale > scegliere **Salva**.
   ![Configurazione di input locale in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Verrà aggiunto automaticamente un file denominato **local_EdgeInput.json** nella cartella degli input.  
4. è possibile eseguirlo in locale o inviare ad Azure. Per testare la query, selezionare **esecuzione locale**.  
   ![Opzioni di esecuzione processo di Analisi di flusso in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. La finestra del prompt dei comandi mostra lo stato del processo. Quando il processo viene eseguito correttamente, viene creata una cartella dal nome simile a "2018-02-23-11-31-42" nel percorso della cartella del progetto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Passare al percorso della cartella per visualizzare i risultati nella cartella locale:

   È anche possibile accedere al portale di Azure e verificare che il processo sia stato creato. 

   ![Cartella dei risultati del processo di Analisi di flusso](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Inviare il processo ad Azure

1. Prima di inviare il processo ad Azure, è necessario connettersi alla sottoscrizione di Azure. Aprire **Esplora server** > fare clic con il pulsante destro del mouse su **Azure** > **Connetti alla sottoscrizione di Microsoft Azure** > accedere alla sottoscrizione di Azure.  

2. Per inviare il processo ad Azure, passare all'editor di query > selezionare **Invia a Azure**.  

3. Verrà visualizzata una finestra popup. Scegliere di aggiornare un processo di Stream Analitica Edge esistente o crearne uno nuovo. Quando si aggiorna un processo esistente, che sostituirà tutte la configurazione del processo, in questo scenario, un nuovo processo verrà pubblicata. Selezionare **Create a New Azure Stream Analytics Job** (Crea un nuovo progetto di Analisi di flusso di Azure) > immettere un nome per il processo simile a **MyASAEdgeJob** > scegliere **Sottoscrizione**, **Gruppo di risorse** e **Percorso** > Selezionare **Invia**.

   ![Inviare il processo di Analisi di flusso in Azure da Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   A questo punto è stato creato il processo Edge di Analitica Stream. È possibile vedere il [eseguire i processi su Esercitazione di IoT Edge](stream-analytics-edge.md) per informazioni su come distribuirla ai dispositivi. 

## <a name="manage-the-job"></a>Gestire il processo 

È possibile visualizzare lo stato e il diagramma del processo da Esplora server. Dal **Analitica Stream** nelle **Esplora Server**, espandere la sottoscrizione e il gruppo di risorse in cui è stato distribuito il processo Edge di Analitica Stream. È possibile visualizzare il processo MyASAEdgeJob con lo stato **Creato**. Espandere il nodo del processo e fare doppio clic su di esso per aprire la visualizzazione del processo.

![Opzioni di gestione dei processi di Esplora server](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
La finestra di visualizzazione di processo offre la possibilità di eseguire operazioni quali l'aggiornamento del processo, l'eliminazione del processo e l'apertura del processo dal portale di Azure.

![Diagramma del processo e altre opzioni in Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Invio di commenti e suggerimenti al team tramite questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
