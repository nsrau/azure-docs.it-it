---
title: "Esercitazione: Riconoscere le emozioni espresse da un viso in un'immagine - API Emozioni, Python"
titlesuffix: Azure Cognitive Services
description: Usare Jupyter Notebook per imparare a usare l'API Emozioni con Python. Visualizzare i risultati usando librerie comuni.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237858"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Esercitazione: Usare l'API Emozioni con Jupyter Notebook e Python.

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/). 

Per iniziare a usare facilmente l'API Emozioni, il Jupyter Notebook collegato di seguito illustra come usare l'API in Python e come visualizzare i risultati tramite alcune librerie comuni.

[Collegarsi al notebook in GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Utilizzo di Jupyter Notebook

Per usare il notebook in modo interattivo, sarà necessario clonarlo ed eseguirlo in Jupyter. Per informazioni su come iniziare a usare i Jupyter Notebook interattivi, seguire le istruzioni all'indirizzo http://jupyter.readthedocs.org/en/latest/install.html.

Per usare il notebook, è necessaria una chiave di sottoscrizione per l'API Emozioni. Visitare la [pagina delle sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/) per effettuare l'iscrizione. Nella pagina di "Accesso" usare l'account Microsoft per accedere; sarà possibile eseguire la sottoscrizione e ottenere le chiavi gratuite. Dopo aver completato il processo di iscrizione, incollare la chiave nella sezione delle variabili qui di seguito. Funziona sia la chiave primaria che la chiave secondaria.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
