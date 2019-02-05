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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302208"
---
1. Completare la [guida introduttiva per Python di Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) per creare un'area di lavoro.  Eventualmente, è possibile ignorare la sezione **Usare il notebook**.
1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Aggiungere un file di configurazione dell'area di lavoro usando uno di questi metodi:
    * Copiare il file **aml_config\config.json** creato usando l'avvio rapido relativo ai prerequisiti nella directory clonata.
    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) nella directory clonata.
1. Avviare il server notebook dalla directory clonata.
    
    ```shell
    jupyter notebook
    ```