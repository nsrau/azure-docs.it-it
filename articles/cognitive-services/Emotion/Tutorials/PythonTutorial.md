---
title: Esercitazione per l'API Emozioni in Python | Microsoft Docs
description: Usare Jupyter Notebook per imparare a usare l'API Emozioni di Servizi cognitivi con Python. Visualizzare i risultati usando librerie comuni.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374092"
---
# <a name="emotion-api-using-python-tutorial"></a>Esercitazione per l'API Emozioni usando Python

> [!IMPORTANT]
> L'anteprima dell'API Video terminerà il 30 ottobre 2017. La nuova [anteprima API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) consente di estrarre facilmente informazioni dettagliate dai video e di ottimizzare le esperienze di individuazione del contenuto, ad esempio i risultati della ricerca, tramite l'individuazione di parole, visi, caratteri ed emozioni. [Altre informazioni](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

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
