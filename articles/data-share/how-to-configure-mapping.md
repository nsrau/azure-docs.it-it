---
title: Configurare un mapping del set di dati nell'anteprima della condivisione dati di Azure
description: Informazioni su come configurare un mapping del set di dati per una condivisione ricevuta usando l'anteprima di condivisione dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169143"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Come configurare un mapping del set di dati per una condivisione ricevuta nell'anteprima della condivisione dati di Azure

Questo articolo illustra come configurare un mapping del set di dati per una condivisione ricevuta usando l'anteprima di condivisione dati di Azure. Questa operazione deve essere eseguita se è stato accettato un invito alla condivisione dati, ma si è scelto di accettare e configurare in un secondo momento. In caso contrario, è possibile che si desideri modificare solo l'account di archiviazione di destinazione per i dati ricevuti. 

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione di dati ricevuta

Nel servizio Condivisione dati di Azure passare alla condivisione ricevuta e selezionare la scheda **Dettagli** . 

![](./media/dataset-mapping.png "Mapping del set") di dati di mapping dei DataSet 

Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione, quindi fare clic su **+ mappa per destinazione**. Potrebbe essere necessario annullare prima se è già stato configurato un account di archiviazione di destinazione e si vuole modificare il mapping a un account di archiviazione diverso. 

![Mappa a mappa di destinazione](./media/dataset-map-target.png "a destinazione") 

## <a name="select-a-new-storage-account"></a>Selezionare un nuovo account di archiviazione 

Selezionare un account di archiviazione in cui si vogliono inserire i dati. Si noti che tutti i dati già esistenti in qualsiasi account di archiviazione precedentemente mappato non verranno spostati automaticamente nel nuovo account di archiviazione.

![](./media/map-target.png "Archiviazione di destinazione") dell'account di archiviazione di destinazione 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).



