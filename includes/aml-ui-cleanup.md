---
title: File di inclusione
description: File di inclusione
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 96ede63b097999247675364217cf458a268e54d9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929641"
---
>[!IMPORTANT]
>È possibile usare le risorse create come prerequisiti per altre esercitazioni e procedure dettagliate relative ad Azure Machine Learning.

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminare l'intero gruppo di risorse per evitare addebiti.

1. Nel portale di Azure, selezionare **Gruppi di risorse** nella parte sinistra della finestra.
 
   ![Eliminare il gruppo di risorse nel portale di Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Selezionare **Elimina gruppo di risorse**.

Se si elimina il gruppo di risorse, vengono eliminate anche tutte le risorse create nella finestra di progettazione. 

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Nella finestra di progettazione in cui è stato creato l'esperimento eliminare le singole risorse selezionandole e quindi selezionando il pulsante **Elimina**.

La destinazione di calcolo creata qui *viene ridimensionata automaticamente* a zero nodi quando non viene usata, Questa azione viene intrapresa per ridurre al minimo gli addebiti. Se si vuole eliminare la destinazione di calcolo, eseguire le operazioni seguenti:

![Eliminare gli asset](./media/aml-ui-cleanup/delete-asset.png)

La registrazione dei set di dati nell'area di lavoro può essere annullata selezionando ogni set di dati e quindi **Annulla registrazione**.

![Annulla registrazione del set di dati](./media/aml-ui-cleanup/unregister-dataset.png)

Per eliminare un set di dati, passare all'account di archiviazione tramite il portale di Azure o Azure Storage Explorer ed eliminare manualmente tali asset.


