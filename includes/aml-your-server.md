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
ms.date: 03/05/2020
ms.openlocfilehash: 4bce52ba3320506b85949493407dded1d52415a3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673634"
---
1. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per installare Azure Machine Learning SDK per Python

1. Creare un'[area di lavoro di Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Scrivere un [file di configurazione](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```shell
    jupyter notebook
    ```
