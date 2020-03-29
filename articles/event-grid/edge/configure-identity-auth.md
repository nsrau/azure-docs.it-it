---
title: Configurare l'identità - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurare l'identità del modulo Griglia di eventi
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841766"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurare l'identità per il modulo Griglia di eventi

In questo articolo viene illustrato come configurare l'identità per la griglia sul server perimetrale. Per impostazione predefinita, il modulo Griglia di eventi presenta il proprio certificato di identità come configurato dal daemon di sicurezza IoT. Griglia di eventi sull'edge presenta il certificato di identità con le chiamate in uscita quando recapita gli eventi. Un sottoscrittore può quindi convalidare il modulo Griglia di eventi che ha inviato l'evento prima di accettare.

Per tutte le configurazioni possibili, vedere Guida alla [sicurezza e all'autenticazione.](security-authentication.md)

## <a name="always-present-identity-certificate"></a>Certificato di identità sempre presente
Ecco una configurazione di esempio per presentare sempre un certificato di identità nelle chiamate in uscita. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Non presentare un certificato di identità
Di seguito è riportata una configurazione di esempio per non presentare un certificato di identità nelle chiamate in uscita. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
