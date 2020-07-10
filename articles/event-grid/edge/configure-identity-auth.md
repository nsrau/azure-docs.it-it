---
title: "Configurare l'identità: griglia di eventi di Azure IoT Edge | Microsoft Docs"
description: Configurare l'identità del modulo griglia di eventi
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171687"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurare l'identità per il modulo di griglia di eventi

Questo articolo illustra come configurare l'identità per la griglia in Edge. Per impostazione predefinita, il modulo di griglia di eventi presenta il proprio certificato di identità configurato dal daemon di sicurezza Internet. Griglia di eventi sul bordo presenta il certificato di identità con le chiamate in uscita quando recapita gli eventi. Un Sottoscrittore può quindi convalidare il modulo di griglia di eventi che ha inviato l'evento prima dell'accettazione.

Vedere la guida per la [sicurezza e l'autenticazione](security-authentication.md) per tutte le configurazioni possibili.

## <a name="always-present-identity-certificate"></a>Presentare sempre il certificato di identità
Ecco una configurazione di esempio per presentare sempre un certificato di identità per le chiamate in uscita. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Non presentare il certificato di identità
Ecco una configurazione di esempio per non presentare un certificato di identità per le chiamate in uscita. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
