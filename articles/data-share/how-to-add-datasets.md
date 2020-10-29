---
title: Aggiungere set di dati a una condivisione dati di Azure esistente
description: Informazioni su come aggiungere set di dati a una condivisione dati esistente in condivisione dati di Azure e condividere con gli stessi destinatari.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910543"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Come aggiungere set di dati a una condivisione esistente nella condivisione dati di Azure

Questo articolo illustra come aggiungere set di dati a una condivisione dati preesistente usando la condivisione di dati di Azure. In questo modo è possibile condividere più dati con gli stessi destinatari senza dover creare una nuova condivisione.

Per informazioni su come aggiungere set di dati durante la creazione di una condivisione, vedere l'esercitazione su come [condividere i dati](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

In condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **set** di dati. Fare clic sul pulsante **+ Aggiungi set di DataSet** per aggiungere altri set di impostazioni.

![Screenshot mostra aggiungere set di impostazioni selezionati.](./media/how-to/how-to-add-datasets/add-datasets.png)

Nel pannello a destra selezionare il tipo di set di dati che si desidera aggiungere e quindi fare clic su **Avanti** . Selezionare la sottoscrizione e il gruppo di risorse dei dati che si desidera aggiungere. Utilizzando le frecce a discesa, trovare e quindi selezionare la casella accanto ai dati da aggiungere.

![Screenshot mostra il riquadro Aggiungi archiviazione BLOB in cui è possibile selezionare i dati.](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Una volta fatto clic su **Aggiungi set di impostazioni** , i set di impostazioni verranno aggiunti alla condivisione. Nota: uno snapshot deve essere attivato dagli utenti affinché possano visualizzare i nuovi set di impostazioni. Se sono state configurate impostazioni snapshot, i consumer visualizzeranno i nuovi set di impostazioni al termine del successivo snapshot pianificato. Senza configurare le impostazioni dello snapshot, il consumer deve attivare manualmente una copia completa o incrementale dei dati per ricevere gli aggiornamenti. Per ulteriori informazioni sugli snapshot, vedere [snapshot](terminology.md).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [aggiungere i destinatari a una condivisione dati esistente](how-to-add-recipients.md).