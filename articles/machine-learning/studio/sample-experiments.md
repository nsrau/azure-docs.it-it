---
title: Creare esperimenti da esempi di Machine Learning - Azure | Microsoft Docs
description: Informazioni su come usare gli esperimenti di esempio di Machine Learning per creare nuovi esperimenti con la raccolta di intelligenza artificiale di Azure e Azure Machine Learning.
keywords: esperimenti di esempio di Machine Learning, esperimento di esempio, esempio di Machine Learning, esempi di intelligenza artificiale
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2018
ms.openlocfilehash: 7cb5c869e4523333ce0651559632070e5a19163c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Creare esperimenti di Machine Learning da esempi funzionanti nella raccolta di intelligenza artificiale di Azure

Informazioni su come iniziare a usare gli esperimenti di esempio dalla [raccolta di intelligenza artificiale di Azure](https://gallery.cortanaintelligence.com/) invece di creare esperimenti di Machine Learning completamente nuovi. È possibile usare gli esempi per compilare la propria soluzione di Machine Learning.

Nella raccolta sono disponibili esperimenti di esempio del team di Microsoft Azure Machine Learning, oltre a esempi condivisi dalla community di Machine Learning. È anche possibile porre domande o inviare commenti sugli esperimenti disponibili.

Per informazioni su come usare la raccolta, guardare il video di 3 minuti [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) della serie [Analisi scientifica dei dati per principianti](data-science-for-beginners-the-5-questions-data-science-answers.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Trovare un esperimento da copiare nella raccolta di intelligenza artificiale per Azure
Per visualizzare gli esperimenti disponibili, passare a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/) e fare clic su **Experiments** (Esperimenti) nella parte superiore della pagina.

### <a name="find-the-newest-or-most-popular-experiments"></a>Trovare gli esperimenti più recenti o richiesti
In questa pagina è possibile visualizzare gli esperimenti **aggiunti di recente**, scorrere per esaminare quelli **più richiesti** o la versione più recente degli **esperimenti Microsoft più richiesti**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Cercare un esperimento che soddisfa requisiti specifici
Per esplorare tutti gli esperimenti:

1. Fare clic su **Browse all** (Esplora tutto) nella parte superiore della pagina.
2. In **Refine by** (Affina per) a sinistra nella sezione **Categories** (Categorie) selezionare **Experiment** (Esperimento) per visualizzare tutti gli esperimenti nella raccolta.
3. È possibile trovare esperimenti che soddisfano i requisiti in due modi diversi:
   * **Selezionare i filtri a sinistra.** Per esaminare ad esempio gli esperimenti che fanno uso di un algoritmo di rilevamento delle anomalie basato su PCA, in **Categories** (Categorie) fare clic su **Experiment** (Esperimento). In **Algorithms Used** (Algoritmi usati) fare clic su click **Show all** (Mostra tutto) e nella finestra di dialogo scegliere **PCA-Based Anomaly Detection** (Rilevamento anomalie basato su PCA). Potrebbe essere necessario scorrere per visualizzare l'opzione.<br></br>
     ![Selezione dei filtri](./media/sample-experiments/choose-an-algorithm.png)
   * **Usare la casella di ricerca.** Ad esempio, per trovare esperimenti Microsoft sul riconoscimento di cifre che usano un algoritmo di macchine a vettori di supporto a due classi, immettere "digit recognition" nella casella di ricerca. Selezionare quindi i filtri **Experiment** (Esperimento), **Microsoft content only** (Solo contenuto Microsoft) e **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi):<br></br>
     ![Usare la casella di ricerca](./media/sample-experiments/search-for-experiments.png)
4. Per altre informazioni su un esperimento, fare clic su di esso.
5. Per eseguire e/o modificare l'esperimento, fare clic su **Open in Studio** (Apri in Studio) nella pagina dell'esperimento. <br></br>

    ![Esperimento di esempio](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > Quando si apre un esperimento in Machine Learning Studio per la prima volta, è possibile provarlo gratuitamente o acquistare una sottoscrizione di Azure. [Confronto tra la versione di valutazione gratuita e il servizio a pagamento di Machine Learning Studio](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Creare un nuovo esperimento usando un esempio come modello
È anche possibile creare un nuovo esperimento in Machine Learning Studio usando come modello un esempio della raccolta.

1. Accedere a [Studio](https://studio.azureml.net) con le credenziali dell'account Microsoft e quindi fare clic su **Nuovo** per creare un esperimento.
2. Esplorare il contenuto e fare clic per selezionare un esempio.

Nell'area di lavoro di Machine Learning Studio verrà creato un nuovo esperimento usando l'esperimento di esempio come modello.

## <a name="next-steps"></a>Passaggi successivi
* [Importare dati da diverse origini](import-data.md)
* [Esercitazione con guida rapida per il linguaggio di programmazione R per Machine Learning](r-quickstart.md)
* [Distribuire un servizio Web di Machine Learning](publish-a-machine-learning-web-service.md)
