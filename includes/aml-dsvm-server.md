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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486122"
---
1. [Creare un'area di lavoro di Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Aggiungere un file di configurazione dell'area di lavoro alla directory clonata usando uno di questi metodi:

    * Nel [portale di Azure](https://ms.portal.azure.com) selezionare **Scarica config.json** nella sezione **Panoramica** dell'area di lavoro. 

    ![Scarica config.json](./media/aml-dsvm-server/download-config.png)

    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) nella directory clonata.

1. Avviare il server notebook dalla directory clonata.

    ```bash
    jupyter notebook
    ```