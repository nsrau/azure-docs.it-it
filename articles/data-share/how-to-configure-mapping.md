---
title: Configurare un mapping dei set di dati
description: Configurare un mapping dei set di dati
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789241"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Come configurare un mapping di set di dati per una condivisione ricevuto nell'anteprima di condividere i dati in Azure

Questo articolo illustra come configurare un mapping di set di dati per una condivisione ricevuti con anteprima di condivisione dei dati in Azure. È opportuno eseguire questa operazione se è accettato un invito alla condivisione dei dati ma acconsentito esplicitamente a "Accept e configurare in seguito". In caso contrario, è possibile semplicemente modificare l'account di archiviazione di destinazione per i dati ricevuti. 

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione di dati ricevuti

Nel servizio di condivisione dei dati di Azure, passare alla condivisione di ricevuto e selezionare il **dettagli** scheda. 

![Mapping di set di dati](./media/dataset-mapping.png "mapping di set di dati") 

Selezionare la casella accanto al set di dati di cui si desidera assegnare a una destinazione e fare clic su **+ mapping di destinazione**. Potrebbe essere necessario annullare il mapping prima se è già stato configurato un account di archiviazione di destinazione e si vuole modificare il mapping a un account di archiviazione diverso. 

![Mappa di destinazione](./media/dataset-map-target.png "mappa di destinazione") 

## <a name="select-a-new-storage-account"></a>Selezionare un nuovo account di archiviazione 

Selezionare un account di archiviazione che si desidera i dati vengano posizionati nel. Si noti che tutti i dati già presenti in qualsiasi precedentemente eseguito il mapping degli account di archiviazione non verranno spostato automaticamente al nuovo account di archiviazione.

![Account di archiviazione di destinazione](./media/map-target.png "archiviazione di destinazione") 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come avviare la condivisione dei dati, il [condividere i dati](share-your-data.md) esercitazione.



