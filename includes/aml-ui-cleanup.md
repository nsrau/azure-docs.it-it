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
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659874"
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

![Annulla registrazione del set di dati](./media/aml-ui-cleanup/unregister-dataset1225.png)

Per eliminare un set di dati, passare all'account di archiviazione tramite il portale di Azure o Azure Storage Explorer ed eliminare manualmente tali asset.


