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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391765"
---
1. Usare le istruzioni riportate in [Creare un'area di lavoro del servizio Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) per eseguire queste operazioni:
    * Creare un ambiente Miniconda
    * Installare Azure Machine Learning SDK per Python
    * Creare un'area di lavoro
    * Scrivere un file di configurazione dell'area di lavoro (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```shell
    jupyter notebook
    ```