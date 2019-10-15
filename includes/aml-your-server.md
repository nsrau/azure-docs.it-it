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
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841110"
---
1. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per installare Azure Machine Learning SDK per Python

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
