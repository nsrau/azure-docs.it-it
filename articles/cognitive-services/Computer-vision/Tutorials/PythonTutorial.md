---
title: 'Esercitazione: API Visione artificiale, Python'
titlesuffix: Azure Cognitive Services
description: Informazioni sull'uso dell'API Visione artificiale con Python usando Jupyter Notebook. Visualizzare i risultati usando le librerie comuni.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 59e88ecb253bc6da803ddf34a4a02229b99e259d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981712"
---
# <a name="tutorial-computer-vision-api-python"></a>Esercitazione: API Visione artificiale, Python

Questa esercitazione illustra come usare l'API Visione artificiale di Python e come visualizzare i risultati usando alcune librerie comuni. Usare Jupyter per eseguire l'esercitazione. Per informazioni su come iniziare a usare interattivo i Jupyter Notebook, fare riferimento alla [documentazione di Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Aprire il Tutorial Notebook in Jupyter 

1. Passare al [tutorial notebook in GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Fare clic sul pulsante verde per clonare o scaricare l'esercitazione. 
3. Aprire un prompt dei comandi e passare alla cartella _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Eseguire il comando **jupyter notebook** dal prompt dei comandi. Verrà avviato Jupyter.
5. Nella finestra Jupyter fare clic su _Computer Vision API Example.ipynb_ per aprire il Tutorial Notebook 

### <a name="running-the-tutorial"></a>Eseguire l'esercitazione

Per usare il notebook, è necessaria una chiave di sottoscrizione per l'API Visione artificiale. Visitare la [pagina delle sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/) per effettuare l'iscrizione. Nella pagina di "Accesso" usare l'account Microsoft per accedere; sarà possibile eseguire la sottoscrizione e ottenere le chiavi gratuite. Dopo aver completato il processo di iscrizione, incollare la chiave nella sezione delle variabili del notebook (riportate sotto). Può essere usata indifferentemente la chiave primaria o quella secondaria. Assicurarsi di racchiudere la chiave tra virgolette perché venga interpretata come una stringa.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
