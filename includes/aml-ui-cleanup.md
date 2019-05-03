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
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028591"
---
>[!IMPORTANT]
>È possibile usare le risorse che è stato creato come prerequisito per altre esercitazioni di servizi di Azure Machine Learning e articoli sulle procedure.


### <a name="delete-everything"></a>Eliminare tutti gli elementi

Se non si intende usare tutto ciò che è stato creato, eliminare l'intero gruppo di risorse in modo da non incorrere in eventuali costi:

1. Nel portale di Azure, selezionare **gruppi di risorse** sul lato sinistro della finestra.
 
   ![Eliminare il gruppo di risorse nel portale di Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Nell'elenco, selezionare il gruppo di risorse creato.

1. Sul lato destro della finestra, selezionare il pulsante con puntini di sospensione (**...** ).

1. Selezionare **Elimina gruppo di risorse**.

L'eliminazione del gruppo di risorse elimina anche tutte le risorse che è stato creato nell'interfaccia visiva.  

### <a name="delete-only-the-compute-target"></a>Eliminare solo la destinazione di calcolo

La destinazione di calcolo che è stato creato in questo caso *automaticamente viene ridimensionato automaticamente* a zero nodi quando non è in uso. Questo serve a ridurre i costi. Se si desidera eliminare la destinazione di calcolo, eseguire questi passaggi:

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

    ![Eliminare la destinazione di calcolo](./media/aml-ui-cleanup/delete-compute-target.png)

1. Nel **calcolo** sezione dell'area di lavoro, selezionare la risorsa.

1. Selezionare **Elimina**.

### <a name="delete-individual-assets"></a>Eliminare singole risorse

Nell'interfaccia visiva in cui è stato creato l'esperimento, eliminare le singole risorse selezionandole e quindi selezionando il **eliminare** pulsante.

![Eliminare gli esperimenti](./media/aml-ui-cleanup/delete-experiment.png)
