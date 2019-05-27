---
title: File di inclusione
description: File di inclusione
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123174"
---
>[!IMPORTANT]
>È possibile usare le risorse create come prerequisiti per altre esercitazioni e procedure dettagliate relative al servizio Azure Machine Learning.


### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminare l'intero gruppo di risorse per evitare addebiti:

1. Nel portale di Azure, selezionare **Gruppi di risorse** nella parte sinistra della finestra.
 
   ![Eliminare il gruppo di risorse nel portale di Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Nella parte destra della finestra selezionare il pulsante con i puntini di sospensione (**...**).

1. Selezionare **Elimina gruppo di risorse**.

Se si elimina il gruppo di risorse, vengono eliminate anche tutte le risorse create nell'interfaccia visiva grafica.  

### <a name="delete-only-the-compute-target"></a>Eliminare solo la destinazione di calcolo

La destinazione di calcolo creata qui *viene ridimensionata automaticamente* a zero nodi quando non viene usata, allo scopo di ridurre gli addebiti. Se si vuole eliminare la destinazione di calcolo, eseguire le operazioni seguenti:

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

    ![Eliminare la destinazione di calcolo](./media/aml-ui-cleanup/delete-compute-target.png)

1. Nella sezione **Calcolo** dell'area di lavoro selezionare la risorsa.

1. Selezionare **Elimina**.

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Nell'interfaccia visiva grafica in cui è stato creato l'esperimento, eliminare le singole risorse selezionandole e quindi selezionando il pulsante **Elimina**.

![Eliminare gli esperimenti](./media/aml-ui-cleanup/delete-experiment.png)
