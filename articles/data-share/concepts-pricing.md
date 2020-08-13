---
title: Informazioni sui prezzi di condivisione dati di Azure
description: Informazioni sul funzionamento dei prezzi di condivisione dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136925"
---
# <a name="understand-azure-data-share-pricing"></a>Informazioni sui prezzi di condivisione dati di Azure

Per la condivisione basata su snapshot, la condivisione di dati di Azure viene addebitata per l'esecuzione dello snapshot del set di dati e Questo articolo illustra come stimare l'esecuzione di snapshot e snapshot del set di dati usando le informazioni sulla cronologia degli snapshot. Attualmente, il provider di dati viene fatturato per l'esecuzione dello snapshot e dello snapshot del set di dati.

## <a name="dataset-snapshot"></a>Snapshot del set di dati

Snapshot del set di dati è l'operazione per spostare il set di dati dall'origine alla destinazione. Quando si crea uno snapshot per una condivisione, lo snapshot di ogni set di dati all'interno della condivisione è un'operazione di snapshot del set di dati. Attenersi alla procedura seguente per visualizzare un elenco di snapshot del set di dati. 

1. In portale di Azure passare alla risorsa della condivisione dati.

1. Consente di selezionare una condivisione dalla condivisione inviata o dalla condivisione ricevuta.

1. Fare clic sulla scheda **cronologia** .

1. Fare clic sull'ora di inizio di uno snapshot.
 
    ![Cronologia snapshot](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Cronologia snapshot") 

1. Visualizzare l'elenco delle operazioni snapshot del set di dati. Ogni elemento linea corrisponde a un'operazione di snapshot del set di dati. In questo esempio sono presenti due snapshot del set di dati.

    ![Operazione di snapshot del set di dati](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Operazione di snapshot del set di dati")

## <a name="snapshot-execution"></a>Esecuzione di snapshot

L'esecuzione dello snapshot include le risorse necessarie per spostare un set di dati dall'origine alla destinazione. Per ogni operazione di snapshot del set di dati, l'esecuzione dello snapshot viene calcolata come numero di Vcore moltiplicato per la durata dello snapshot. Gli addebiti vengono rivalutati in base al minuto e arrotondati per eccesso. Il numero di vCore viene selezionato dinamicamente in base alla coppia di destinazione e al modello di dati. Attenersi alla procedura seguente per visualizzare l'ora di inizio, l'ora di fine e il vcore di snapshot usati per un'operazione di snapshot del set di dati.

1. In portale di Azure passare alla risorsa della condivisione dati.

1. Consente di selezionare una condivisione dalla condivisione inviata o dalla condivisione ricevuta.

1. Fare clic sulla scheda **cronologia** .

1. Fare clic sull'ora di inizio di uno snapshot.

    ![Cronologia snapshot](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Cronologia snapshot") 

1. Fare clic sullo stato di un'operazione di snapshot del set di dati.

    ![Stato dello snapshot del set di dati](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Stato dello snapshot del set di dati")

1. Visualizzare l'ora di inizio, l'ora di fine e la Vcore utilizzata per questa operazione di snapshot del set di dati. 

    ![Esecuzione di snapshot](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Esecuzione di snapshot")

## <a name="next-steps"></a>Passaggi successivi

- Ottenere le ultime informazioni sui prezzi- [prezzi di condivisione dati di Azure](https://azure.microsoft.com/pricing/details/data-share/)
- Usare il calcolatore dei prezzi di Azure per stimare i costi- [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)
