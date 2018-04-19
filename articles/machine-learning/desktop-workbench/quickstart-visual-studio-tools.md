---
title: Articolo introduttivo per gli strumenti di Visual Studio per Machine Learning in Azure | Microsoft Docs
description: Questo articolo illustra come iniziare a usare gli strumenti di Visual Studio per Machine Learning, dalla creazione di un esperimento al training di un modello e all'uso di un servizio Web.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
Visual Studio Tools for AI è un'estensione di sviluppo per la compilazione, il test e la distribuzione di soluzioni per apprendimento avanzato e intelligenza artificiale. Offre l'integrazione senza problemi con Azure Machine Learning e in particolare una visualizzazione della cronologia di esecuzione, con informazioni dettagliate sulle prestazioni dei training precedenti e metriche personalizzate. Offre una visualizzazione di esplorazione degli esempi, che consente di esplorare e avviare un nuovo progetto con [Microsoft Cognitive Toolkit (noto in precedenza come CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) e altri framework per Deep Learning. Fornisce infine una visualizzazione di esplorazione per le destinazioni di calcolo, che consente di inviare processi per eseguire il training dei modelli in ambienti remoti come Macchine virtuali di Azure o server Linux con GPU. Offre anche un accesso facilitato ad [Azure Batch per intelligenza artificiale (anteprima)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Introduzione 
Per iniziare, è prima di tutto necessario scaricare e installare [Visual Studio](https://www.visualstudio.com/downloads/). Dopo avere aperto Visual Studio, seguire questa procedura:
1. Scegliere "Estensioni e aggiornamenti" dalla barra dei menu in Visual Studio.
2. Fare clic sulla scheda "Online" e selezionare "Search Visual Studio Marketplace" (Cerca in Visual Studio Marketplace).
3. Cercare "Visual Studio for AI". 
3. Fare clic sul pulsante **Download** . 
4. Dopo l'installazione riavviare Visual Studio. 

Dopo il ricaricamento di Visual Studio, l'estensione risulta attiva. [Altre informazioni sulla ricerca delle estensioni](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools for AI richiede Visual Studio 2015 o 2017, edizione Professional o Enterprise. Non supporta la versione OSX di Apple. 


## <a name="exploring-project-samples"></a>Esplorazione degli esempi di progetto
Visual Studio Tools for AI include una visualizzazione di esplorazione degli esempi, che semplifica l'individuazione di un esempio e consente di provare gli esempi con pochi clic. Per aprire la visualizzazione di esplorazione, seguire questa procedura:   
1. Scegliere **AI Tools** (Strumenti AI) dalla barra dei menu.
2. Fare clic su "Azure Machine Learning Gallery" (Raccolta di Azure Machine Learning).

Viene aperta una scheda con tutti gli esempi di Azure ML.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Creazione di un nuovo progetto dalla visualizzazione di esplorazione degli esempi 
È possibile esaminare diversi esempi e ottenere altre informazioni su ogni esempio. Individuare l'esempio "Classifying Iris". Per creare un nuovo progetto basato su questo esempio, seguire questa procedura:
1. Fare clic sul pulsante **installa** nell'esempio di progetto. Verrà aperta una nuova finestra di dialogo. 
2. Selezionare un gruppo di risorse, un account e un'area di lavoro.
3. È possibile lasciare il tipo di progetto impostato su Generale.
4. Immettere un percorso di progetto e un nome di progetto, quindi premere INVIO. 
5. Viene aperta una finestra di dialogo in cui viene chiesto di salvare una soluzione. Fare clic su Salva. 

Al termine, viene aperto un nuovo progetto in una nuova istanza di Visual Studio. 

> [!TIP]
> È necessario essere connessi per accedere alla risorsa di Azure. Dal terminale incorporato immettere "az login" e seguire le istruzioni. 

## <a name="submitting-experiment-with-the-new-project"></a>Invio di un esperimento con il nuovo progetto
Quando il nuovo progetto è aperto in Visual Studio, inviare un processo a una destinazione di calcolo (locale o VM con Docker).
Per inviare il processo, seguire questa procedura: 
1. Da Esplora soluzioni fare clic con il pulsante destro del mouse sul file che si vuole inviare e scegliere **Imposta come file di avvio**.
2. Selezionare il nome del progetto, fare clic con il pulsante destro del mouse e scegliere **Invia processo**.
3. Verrà aperta una nuova finestra di dialogo, in cui è possibile scegliere il cluster (o destinazione di calcolo) per eseguire lo script.
4. Fare clic su **Invia**

Dopo l'invio del processo, il terminale incorporato mostra lo stato delle esecuzioni.

## <a name="view-list-of-jobs"></a>Visualizzare l'elenco dei processi
Dopo l'invio del processo, è possibile elencare i processi dalla cronologia di esecuzione.
1. In **Esplora server** fare clic su **AI Tools** (Strumenti AI).
2. Selezionare quindi **Azure Machine Learning**
3. Fare clic sul menu **Processi**.

Esplora processi elenca tutti gli esperimenti inviati per questo progetto. 

## <a name="view-job-details"></a>Visualizza i dettagli dei processi
Con la visualizzazione Esplora processi aperta, fare clic sulla prima esecuzione nell'elenco.
Verranno caricati il pannello Riepilogo processi e il pannello Logs and Outputs (Log e output).

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [How to configure Azure Machine Learning to work with an IDE (Come configurare Azure Machine Learning per usare un IDE)](./how-to-configure-your-IDE.md)
