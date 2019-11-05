---
title: Revocare una sottoscrizione di condivisione in una condivisione dati di Azure
description: Informazioni su come revocare una sottoscrizione di condivisione da un destinatario usando la condivisione di dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476386"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Come revocare una sottoscrizione di condivisione di un consumer in una condivisione dati di Azure

Questo articolo illustra come revocare una sottoscrizione di condivisione da uno o più utenti usando la condivisione di dati di Azure. In questo modo si impedisce a un consumer di attivare altri snapshot. Se il consumer non ha ancora attivato uno snapshot, non riceverà mai i dati dopo che la sottoscrizione di condivisione verrà revocata. Se in precedenza è stato attivato uno snapshot, i dati più recenti rimangono nel proprio account.

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

In condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **Condividi sottoscrizioni** .

![Revoca sottoscrizione di condivisione](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Selezionare le caselle accanto ai destinatari di cui si desidera eliminare le sottoscrizioni di condivisione e quindi fare clic su **revoca**. Il consumer non riceverà più gli aggiornamenti ai dati.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [monitorare le condivisioni dati](how-to-monitor.md).