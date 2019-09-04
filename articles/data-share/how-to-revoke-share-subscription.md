---
title: Revocare una sottoscrizione di condivisione nell'anteprima della condivisione dati di Azure
description: Revocare una sottoscrizione di condivisione
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: dc14bb121d18179091aa5f6f2854b391e72632f1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258623"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Come revocare una sottoscrizione di condivisione di un consumer nell'anteprima della condivisione dati di Azure

Questo articolo illustra come revocare una sottoscrizione di condivisione da uno o più utenti usando l'anteprima della condivisione dati di Azure. In questo modo si impedisce a un consumer di attivare altri snapshot. Se il consumer non ha ancora attivato uno snapshot, non riceverà mai i dati dopo che la sottoscrizione di condivisione verrà revocata. Se in precedenza è stato attivato uno snapshot, i dati più recenti rimangono nel proprio account.

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

In anteprima condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **Condividi sottoscrizioni** .

![Revoca sottoscrizione di condivisione](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Selezionare le caselle accanto ai destinatari di cui si desidera eliminare le sottoscrizioni di condivisione e quindi fare clic su **revoca**. Il consumer non riceverà più gli aggiornamenti ai dati.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [monitorare le condivisioni dati](how-to-monitor.md).