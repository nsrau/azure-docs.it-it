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
ms.date: 09/26/2019
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476090"
---
1. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per installare Azure Machine Learning SDK per Python

1. Creare un'[area di lavoro di Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Scrivere un [file di configurazione](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```shell
    jupyter notebook
    ```
