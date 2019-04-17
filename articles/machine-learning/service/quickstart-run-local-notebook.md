---
title: 'Avvio rapido: eseguire un notebook nel proprio server notebook'
titleSuffix: Azure Machine Learning service
description: Introduzione al servizio Azure Machine Learning. Usare il proprio server notebook locale per provare l'area di lavoro.  L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: ade0444110dbb23e762a869dc79805872c0f0bf0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281132"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Usare il proprio server notebook per iniziare a usare Azure Machine Learning

Usare il proprio server notebook per eseguire il codice che registra i valori nell'[area di lavoro del servizio Machine Learning](concept-azure-machine-learning-architecture.md). L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning con il servizio Machine Learning.

Questo argomento di avvio rapido usa l'ambiente Python e il server di Jupyter Notebook. Per un avvio rapido senza installazione di SDK, vedere [Avvio rapido: Usare un server notebook basato sul cloud per iniziare a usare Azure Machine Learning](quickstart-run-cloud-notebook.md) 

Ecco una versione video di questa guida introduttiva:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

* Un server notebook Python 3.6 con Azure Machine Learning SDK installato
* Un'area di lavoro del servizio Azure Machine Learning
* Un file di configurazione dell'area di lavoro (**aml_config/config.json** ).

Tutti questi prerequisiti sono ottenibili in [Creare un'area di lavoro del servizio Azure Machine Learning](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Usare l'area di lavoro

Creare uno script o avviare un notebook nella stessa directory del file di configurazione dell'area di lavoro. Eseguire questo codice che usa le API di base dell'SDK per tenere traccia delle esecuzioni dell'esperimento.

1. Creare un esperimento nell'area di lavoro.
1. Registrare un singolo valore nell'esperimento.
1. Registrare un elenco di valori nell'esperimento.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Visualizzare i risultati registrati

Al termine è possibile visualizzare l'esecuzione dell'esperimento nel portale di Azure. Per stampare un URL che consente di passare ai risultati dell'ultima esecuzione, usare il codice seguente:

```python
print(run.get_portal_url())
```

Questo codice restituisce un collegamento che si può usare per visualizzare i valori registrati nel portale di Azure all'interno nel browser.

![Valori registrati nel portale di Azure](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Pulire le risorse 

>[!IMPORTANT]
>È possibile usare le risorse create qui come prerequisiti per altre esercitazioni e procedure dettagliate per il servizio Machine Learning.

Se non si prevede di usare le risorse create in questo articolo, eliminarle per evitare eventuali addebiti.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state create le risorse necessarie per eseguire esperimenti e distribuire modelli. È stato eseguito codice in un notebook ed è stata esaminata la cronologia di esecuzione del codice nell'area di lavoro nel cloud.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello di classificazione delle immagini](tutorial-train-models-with-aml.md)

È anche possibile esaminare alcuni [esempi più avanzati in GitHub](https://aka.ms/aml-notebooks) oppure visualizzare il [manuale dell'utente dell'SKD](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
