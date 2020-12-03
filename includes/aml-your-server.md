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
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562507"
---
1. Usare le istruzioni riportate in [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py) per installare Azure Machine Learning SDK per Python

1. Creare un'[area di lavoro di Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Scrivere un [file di configurazione](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Avviare il server notebook dalla directory clonata.

    ```bash
    jupyter notebook
    ```