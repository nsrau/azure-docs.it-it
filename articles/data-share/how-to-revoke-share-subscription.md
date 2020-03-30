---
title: Revocare una sottoscrizione di condivisione nella condivisione dati di AzureRevoke a share subscription in Azure Data Share
description: Informazioni su come revocare una sottoscrizione di condivisione da un destinatario usando la condivisione dati di Azure.Learn how to revoke a share subscription from a recipient using Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476386"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Come revocare la sottoscrizione di condivisione di un consumer in Condivisione dati di AzureHow to revoke a consumer's share subscription in Azure Data Share

Questo articolo illustra come revocare una sottoscrizione di condivisione da uno o più consumer usando la condivisione dati di Azure.This article explains how to revoke a share subscription from one or more of your consumers using Azure Data Share. Ciò impedisce a un consumer di attivare altre istantanee. Se il consumer non ha ancora attivato uno snapshot, non riceverà mai i dati dopo la revoca della sottoscrizione di condivisione. Se in precedenza è stato attivato uno snapshot, i dati più recenti di cui dispongono rimarranno nel proprio account.

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

In Condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **Condividi sottoscrizioni.**

![Revoca sottoscrizione di condivisione](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Selezionare le caselle accanto ai destinatari le cui sottoscrizioni di condivisione si desidera eliminare e quindi fare clic su **Revoca**. Il consumer non otterrà più aggiornamenti ai propri dati.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni su come [monitorare le condivisioni dati](how-to-monitor.md).