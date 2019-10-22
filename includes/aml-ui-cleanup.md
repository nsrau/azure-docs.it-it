---
title: File di inclusione
description: File di inclusione
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692254"
---
>[!IMPORTANT]
>È possibile usare le risorse create come prerequisiti per altre esercitazioni e procedure dettagliate relative al servizio Azure Machine Learning.

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminare l'intero gruppo di risorse per evitare addebiti:

1. Nel portale di Azure, selezionare **Gruppi di risorse** nella parte sinistra della finestra.
 
   ![Eliminare il gruppo di risorse nel portale di Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Nella parte destra della finestra selezionare il pulsante con i puntini di sospensione ( **...** ).

1. Selezionare **Elimina gruppo di risorse**.

Se si elimina il gruppo di risorse, vengono eliminate anche tutte le risorse create nell'interfaccia visiva grafica.  

### <a name="delete-only-the-compute-target"></a>Eliminare solo la destinazione di calcolo

La destinazione di calcolo creata qui *viene ridimensionata automaticamente* a zero nodi quando non viene usata, allo scopo di ridurre gli addebiti. Se si vuole eliminare la destinazione di calcolo, eseguire le operazioni seguenti:

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

    ![Eliminare la destinazione di calcolo](./media/aml-ui-cleanup/delete-compute-target.png)

1. Nella sezione **Calcolo** dell'area di lavoro selezionare la risorsa.

1. Selezionare **Elimina**.

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Nell'interfaccia visiva grafica in cui è stato creato l'esperimento, eliminare le singole risorse selezionandole e quindi selezionando il pulsante **Elimina**. La registrazione dei set di dati nell'area di lavoro può essere annullata selezionando ogni set di dati e quindi **Annulla registrazione**.

![Eliminare gli asset](./media/aml-ui-cleanup/delete-asset.png)
