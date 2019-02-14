---
title: 'Esercitazione: Eseguire operazioni sulle immagini - Python'
titlesuffix: Azure Cognitive Services
description: Informazioni sull'uso dell'API Visione artificiale con Python usando Jupyter Notebook. Visualizzare i risultati usando le librerie comuni.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: afb937f1be780c35ef44023acfedeb38a6b6a877
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875071"
---
# <a name="tutorial-computer-vision-api-python"></a>Esercitazione: API Visione artificiale, Python

Questa esercitazione illustra come usare l'API Visione artificiale di Python e come visualizzare i risultati usando alcune librerie comuni. Per eseguire l'esercitazione si userà Jupyter. Per informazioni su come iniziare a usare Jupyter Notebook interattivi, fare riferimento alla [documentazione di Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Prerequisiti

- [Python 2.7 o 3.5 e versioni successive](https://www.python.org/downloads/)
- Strumento [pip](https://pip.pypa.io/en/stable/installing/)
- [Jupyter Notebook](https://jupyter.org/install) installato

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Aprire il notebook dell'esercitazione in Jupyter 

1. Andare al repository GitHub [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Fare clic sul pulsante verde per clonare o scaricare il repository. 
3. Aprire un prompt dei comandi e passare alla cartella **Cognitive-Vision-Python\Jupyter Notebook**.
1. Assicurarsi di aver tutte le librerie necessarie eseguendo il comando `pip install requests opencv-python numpy matplotlib` dal prompt dei comandi.
1. Avviare Jupyter eseguendo il comando `jupyter notebook` dal prompt dei comandi.
1. Nella finestra Jupyter fare clic su _Computer Vision API Example.ipynb_ per aprire il notebook dell'esercitazione.

## <a name="run-the-tutorial"></a>Eseguire l'esercitazione

Per usare il notebook, è necessaria una chiave di sottoscrizione per l'API Visione artificiale. Visitare la [pagina delle sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/) per effettuare l'iscrizione. Nella pagina di **Accesso** usare l'account Microsoft per accedere; sarà possibile eseguire la sottoscrizione e ottenere le chiavi gratuite. Dopo aver completato il processo di iscrizione, incollare la chiave nella sezione `Variables` del notebook (riportata sotto). Può essere usata indifferentemente la chiave primaria o quella secondaria. Assicurarsi di racchiudere la chiave tra virgolette perché venga interpretata come una stringa.

È necessario anche accertarsi che il campo `_region` corrisponda all'area associata alla propria sottoscrizione.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

Quando si eseguirà l'esercitazione, sarà possibile aggiungere immagini da analizzare, sia da un URL sia dall'archiviazione locale. Lo script visualizzerà le immagini e le informazioni di analisi nel notebook.
