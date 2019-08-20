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
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846006"
---
- Installazione di Azure Machine Learning SDK per Python. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per eseguire queste operazioni:


1. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per eseguire queste operazioni:
    * Creare un ambiente Miniconda[Creare e gestire aree di lavoro del servizio Azure Machine Learning]
    * Installare Azure Machine Learning SDK per Python

1. Creare un'[area di lavoro del servizio Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Scrivere un [file di configurazione](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```shell
    jupyter notebook
    ```