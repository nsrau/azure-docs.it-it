---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395533"
---
1. Usare le istruzioni riportate in [Creare un'area di lavoro del servizio Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) per creare un ambiente Miniconda, creare un'area di lavoro e scrivere un file di configurazione dell'area di lavoro (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Aggiungere un file di configurazione dell'area di lavoro usando uno di questi metodi:
    * Copiare il file **aml_config/config.json** creato usando la guida di avvio rapido sui prerequisiti nella directory clonata.
    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Avviare il server notebook dalla directory clonata.
    
    ```shell
    jupyter notebook
    ```