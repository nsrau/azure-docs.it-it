---
title: Articolo introduttivo per gli strumenti di Visual Studio Code per Machine Learning in Azure | Microsoft Docs
description: Questo articolo illustra come iniziare a usare gli strumenti di Visual Studio Code per Machine Learning, dalla creazione di un esperimento al training di un modello e all'uso di un servizio Web.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 680c1afab1af31cfef51b1c82d2db49f452ba6ab
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI è un'estensione di sviluppo per la compilazione, il test e la distribuzioni di soluzioni per Deep Learning e intelligenza artificiale. Offre l'integrazione senza problemi con Azure Machine Learning e in particolare una visualizzazione della cronologia di esecuzione, con informazioni dettagliate sulle prestazioni dei training precedenti e metriche personalizzate. Offre una visualizzazione di esplorazione degli esempi, che consente di esplorare e avviare un nuovo progetto con [Microsoft Cognitive Toolkit (noto in precedenza come CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) e altri framework per Deep Learning. Fornisce infine una visualizzazione di esplorazione per le destinazioni di calcolo, che consente di inviare processi per eseguire il training dei modelli in ambienti remoti come Macchine virtuali di Azure o server Linux con GPU. 
 
## <a name="getting-started"></a>introduttiva 
Per iniziare, è prima di tutto necessario scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download). Dopo avere aperto Visual Studio Code, seguire questa procedura:
1. Fare clic sull'icona dell'estensione sulla barra delle attività. 
2. Cercare "Visual Studio Code Tools for AI". 
3. Fare clic sul pulsante **Installa**. 
4. Dopo l'installazione, fare clic sul pulsante **Ricarica**. 

Dopo il ricaricamento di Visual Studio Code, l'estensione risulta attivata. [Altre informazioni sull'installazione delle estensioni](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Esplorazione degli esempi di progetto
Visual Studio Code Tools for AI include una visualizzazione di esplorazione degli esempi, che semplifica l'individuazione di un esempio e consente di provare gli esempi con pochi clic. Per aprire la visualizzazione di esplorazione, seguire questa procedura:   
1. Aprire il riquadro comandi scegliendo Visualizza > **Riquadro comandi** oppure tramite **CTRL+MAIUSC+P**.
2. Immettere "AI Sample". 
3. Viene visualizzato un consiglio relativo ad "AI: Open Azure ML Sample Explorer". Selezionare l'esempio e premere INVIO. 

In alternativa, è possibile fare clic sull'icona della visualizzazione di esplorazione degli esempi.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Creazione di un nuovo progetto dalla visualizzazione di esplorazione degli esempi 
È possibile esaminare diversi esempi e ottenere altre informazioni su ogni esempio. Individuare l'esempio "Classifying Iris". Per creare un nuovo progetto basato su questo esempio, seguire questa procedura:
1. Fare clic sul pulsante di installazione nell'esempio di progetto. Notare i comandi richiesti, che illustrano in modo dettagliato la procedura di creazione di un nuovo progetto. 
2. Scegliere un nome per il progetto, ad esempio "Iris".
3. Scegliere un percorso per la cartella in cui creare il progetto, quindi premere INVIO. 
4. Selezionare un'area di lavoro esistente, quindi premere INVIO.

Verrà creato il progetto.

> [!TIP]
> Sarà necessario essere connessi per accedere alla risorsa di Azure. Dal terminale incorporato immettere "az login" e seguire le istruzioni. 

## <a name="submitting-experiment-with-the-new-project"></a>Invio di un esperimento con il nuovo progetto
Quando il nuovo progetto è aperto in Visual Studio Code, è possibile inviare un processo alla destinazione di calcolo diversa (locale e VM con Docker).
Visual Studio Code Tools for AI consente di inviare un esperimento in molti modi. 
1. Menu di scelta rapida (clic con il pulsante destro del mouse): **AI: Submit Job**.
2. Dal riquadro comandi: "AI: Submit Job".
3. In alternativa, è possibile eseguire il comando direttamente usando l'interfaccia della riga di comando di Azure oppure i comandi di Machine Learning tramite il terminale incorporato.

Aprire il file iris_sklearn.py, fare clic con il pulsante destro del mouse, quindi scegliere **AI: Submit Job**.
1. Selezionare la piattaforma: "Azure Machine Learning".
2. Selezionare la configurazione di esecuzione: "Docker-Python."

> [!NOTE]
> Se si invia un processo per la prima volta, viene visualizzato un messaggio analogo a "Non è stata trovata alcuna configurazione per Machine Learning, creazione...". Viene aperto un file JSON. Salvarlo (**CTRL+S**).

Dopo l'invio del processo, il terminale incorporato mostra lo stato delle esecuzioni. 

## <a name="view-list-of-jobs"></a>Visualizzare l'elenco dei processi
Dopo l'invio dei processi, è possibile elencare i processi dalla cronologia di esecuzione.
1. Aprire il riquadro comandi scegliendo Visualizza > **Riquadro comandi** oppure tramite **CTRL+MAIUSC+P**.
2. Immettere "AI List."
3. Viene visualizzato un consiglio relativo ad "AI: List Jobs". Selezionare e premere INVIO.
4. Selezionare la piattaforma "Azure Machine Learning".

Viene aperta la visualizzazione Elenco processi e vengono mostrate tutte le esecuzioni e alcune informazioni correlate.

## <a name="view-job-details"></a>Visualizza i dettagli dei processi
Con la visualizzazione Elenco processi ancora aperta, fare clic sulla prima esecuzione nell'elenco.
Per visualizzare informazioni dettagliate sui risultati di un processo, fare clic sul primo **ID processo**. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [How to configure Azure Machine Learning to work with an IDE (Come configurare Azure Machine Learning per usare un IDE)](./how-to-configure-your-IDE.md)

