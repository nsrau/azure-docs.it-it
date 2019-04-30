---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845690"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passaggio 1: Passare al gateway di rete virtuale

1. Nel [portale di Azure](https://portal.azure.com) passare a **Tutte le risorse**. 
2. Per aprire la pagina del gateway di rete virtuale, passare al gateway di rete virtuale da eliminare e selezionarlo.

### <a name="step-2-delete-connections"></a>Passaggio 2: Eliminare le connessioni

1. Nella pagina del gateway di rete virtuale fare clic su **Connessioni** per visualizzare tutte le connessioni del gateway.
2. Fare clic su **...** sulla riga del nome della connessione, quindi selezionare **Elimina** nell'elenco a discesa.
3. Fare clic su **Sì** per confermare l'eliminazione della connessione. Se si dispone di più connessioni, eliminarle tutte.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passaggio 3: Eliminare il gateway di rete virtuale

Si noti che se in aggiunta alla configurazione S2S si ha una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.

1. Nella pagina del gateway di rete virtuale fare clic su **Panoramica**.
2. Nella pagina **Panoramica** fare clic su **Elimina** per eliminare il gateway.
