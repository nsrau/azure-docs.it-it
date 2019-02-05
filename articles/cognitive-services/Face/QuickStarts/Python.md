---
title: "Guida introduttiva: Rilevare i visi in un'immagine con l'API REST di Azure e Python"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con Python per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 606f01059620443c8c42a24f416128d0856eff50
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213787"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Guida introduttiva: Rilevare i visi in un'immagine con l'API REST Viso e Python

In questa guida introduttiva si userà l'API REST Viso di Azure con Python per rilevare i visi umani in un'immagine. Lo script disegna cornici intorno ai visi, sovrapponendo informazioni su sesso ed età sull'immagine.

![Un uomo e una donna, con rettangoli disegnati intorno ai visi e informazioni su età e sesso visualizzate sull'immagine](../images/labelled-faces-python.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 


## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.

## <a name="run-the-jupyter-notebook"></a>Avviare il notebook Jupyter

È possibile eseguire questa guida introduttiva come Jupyter Notebook in [MyBinder](https://mybinder.org). Per avviare Binder, selezionare il pulsante seguente. Quindi, seguire le istruzioni contenute nel notebook.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>Passaggi successivi

Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni sugli scenari supportati.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
