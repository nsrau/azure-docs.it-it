---
title: Eseguire test locali delle query di Analisi di flusso di Azure con Visual Studio
description: Questo articolo descrive come eseguire test locali delle query con gli strumenti di Analisi di flusso di Azure per Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: cc4372a8a8a8f39bf4f77f637b459c5281b8852d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299526"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Eseguire test locali delle query di Analisi di flusso con Visual Studio

È possibile usare gli strumenti di Analisi di flusso di Azure per Visual Studio per eseguire test locali dei processi di Analisi di flusso con dati di esempio.

Per imparare a creare un processo di Analisi di flusso con Visual Studio, usare questa [Guida introduttiva](stream-analytics-quick-create-vs.md).

## <a name="test-your-query"></a>Testare la query

Nel progetto di Analisi di flusso di Azure fare doppio clic su **Script.asaql** per aprire lo script nell'editor. È possibile compilare la query per verificare la presenza di errori di sintassi. L'editor di query supporta IntelliSense, i colori sintassi e l'indicatore di errore.

![Editor di query](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Add local input (Aggiungi input locale)

Per convalidare la query rispetto ai dati statici locali, fare clic con il pulsante destro del mouse sull'input e scegliere **Aggiungi input locale**.
   
![Add local input (Aggiungi input locale)](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Nella finestra popup selezionare i dati di esempio dal percorso locale e fare clic su **Salva**.
   
![Add local input (Aggiungi input locale)](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Verrà aggiunto automaticamente un file denominato **local_EntryStream.json** nella cartella degli input.
   
![Elenco di file della cartella di input locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Nell'editor di query fare clic su **Esecuzione locale**. oppure premere il tasto F5.
   
![Esecuzione locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
L'output può essere visualizzato in formato tabella direttamente da Visual Studio.

![Output in formato tabella](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

È possibile trovare il percorso di output dall'output della console e premere un tasto qualsiasi per aprire la cartella dei risultati.
   
![Esecuzione locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Controllare i risultati nella cartella locale.
   
![Risultato nella cartella locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Input di esempio
È anche possibile raccogliere i dati di input dalle origini di input in un file locale. Fare clic con il pulsante destro del mouse sul file di configurazione degli input e selezionare **Dati di esempio**. 

![Dati di esempio](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

È possibile campionare solo lo streaming di dati da Hub eventi o hub IoT. Non sono supportate altre origini di input. Nella finestra di dialogo popup immettere il percorso locale in cui salvare i dati di esempio e selezionare **Esempio**.

![Configurazione dati di esempio](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Nella finestra **Output** viene visualizzato lo stato dell'operazione. 

![Output dei dati di esempio](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Esercitazione: Distribuire un processo di Analisi di flusso di Azure con CI/CD tramite Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Eseguire integrazione e sviluppo in modo continuo con gli strumenti di Analisi di flusso](stream-analytics-tools-for-visual-studio-cicd.md)