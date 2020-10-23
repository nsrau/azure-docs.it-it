---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/16/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b8a61586acd888301350277d924f3d4fe176b4c8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148217"
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
