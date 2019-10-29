---
title: "Configurare l'identità: griglia di eventi di Azure IoT Edge | Microsoft Docs"
description: Configurare l'identità del modulo griglia di eventi
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992444"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurare l'identità per il modulo di griglia di eventi

Questo articolo fornisce esempi delle possibili configurazioni di identità per un modulo di griglia di eventi. Per impostazione predefinita, il modulo di griglia di eventi presenta il certificato di identità configurato dal daemon di sicurezza Internet. Un certificato di identità viene presentato dal modulo di griglia di eventi sulle chiamate in uscita, ovvero quando recapita gli eventi. Un Sottoscrittore di un evento di griglia di eventi può quindi scegliere di verificare che sia effettivamente il modulo di griglia di eventi che ha inviato l'evento prima di accettare l'evento.

Vedere la guida per la [sicurezza e l'autenticazione](security-authentication.md) per tutte le configurazioni possibili.

## <a name="always-present-identity-certificate"></a>Presentare sempre il certificato di identità
Ecco una configurazione di esempio per presentare sempre un certificato di identità per le chiamate in uscita. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Non presentare il certificato di identità
Ecco una configurazione di esempio per non presentare un certificato di identità per le chiamate in uscita. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
