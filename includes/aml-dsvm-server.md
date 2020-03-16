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
ms.openlocfilehash: e289cf7aea6e0ea46ff049f3ea8bf9e1517e8aaf
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673621"
---
1. [Creare un'area di lavoro di Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

    ```CLI
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