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
ms.openlocfilehash: 02ef0d6c7c8ddc7088938d9c8ea379e3b97f3045
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158732"
---
1. [Creare un'area di lavoro del servizio Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Aggiungere un file di configurazione dell'area di lavoro alla directory clonata usando uno di questi metodi:

    * Nel [portale di Azure](https://ms.portal.azure.com) selezionare **Scarica config.json** nella sezione **Panoramica** dell'area di lavoro. 

    ![Scarica config.json](./media/aml-dsvm-server/download-config.png)

    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) nella directory clonata.


1. Avviare il server notebook dalla directory clonata.
    
    ```shell
    jupyter notebook
    ```