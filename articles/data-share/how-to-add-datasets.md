---
title: Aggiungere set di dati a una condivisione dati di Azure esistenteAdd datasets to an existing Azure Data Share
description: Informazioni su come aggiungere set di dati a una condivisione dati esistente in Condivisione dati di Azure e condividere con gli stessi destinatari.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490548"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Come aggiungere set di dati a una condivisione esistente in Azure Data ShareHow to add datasets to an existing share in Azure Data Share

Questo articolo illustra come aggiungere set di dati a una condivisione dati preesistente usando La condivisione dati di Azure.This article explains how to add datasets to a pre-existing data share using Azure Data Share. Ciò consente di condividere più dati con gli stessi destinatari senza dover creare una nuova condivisione.

Per informazioni su come aggiungere set di dati durante la creazione di una condivisione, vedere l'esercitazione [Condividere dati.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

In Condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **Set di** dati. Fare clic sul pulsante Aggiungi **set di** dati per aggiungere altri set di dati.

![Aggiungere i set di dati](./media/how-to/how-to-add-datasets/add-datasets.png)

Nel riquadro a destra selezionare il tipo di set di dati che si desidera aggiungere e quindi fare clic su **Avanti**. Selezionare la sottoscrizione e il gruppo di risorse dei dati da aggiungere. Utilizzando le frecce a discesa, individuare e selezionare la casella accanto ai dati da aggiungere.

![Aggiungere i set di dati](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Dopo aver fatto clic su **Aggiungi set di dati**, i dataset verranno aggiunti alla condivisione. Nota: uno snapshot deve essere attivato dai consumer per poter visualizzare i nuovi set di dati. Se sono configurate impostazioni snapshot, i consumer vedranno i nuovi set di dati al termine del successivo snapshot pianificato. Senza le impostazioni dello snapshot configurate, il consumer deve attivare manualmente una copia completa o incrementale dei dati per ricevere gli aggiornamenti. Per ulteriori informazioni sugli snapshot, vedere [Istantanee](terminology.md).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni su come aggiungere destinatari a una [condivisione dati esistente.](how-to-add-recipients.md)