---
title: Configurare l'autenticazione client delle chiamate in arrivo - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurare i protocolli API esposti da Griglia di eventi in Edge Edge.Configure API protocols exposed by Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841792"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurare l'autenticazione client delle chiamate in arrivo

In questa guida vengono forniti esempi delle possibili configurazioni di autenticazione client per il modulo Griglia di eventi. Il modulo Griglia di eventi supporta due tipi di autenticazione client:

* Chiave della firma di accesso condiviso (SAS) basata
* Basato su certificati

Per tutte le configurazioni possibili, vedere Guida alla [sicurezza e all'autenticazione.](security-authentication.md)

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificati, senza certificati autofirmatiEnable certificate-based client authentication, no self-signed certificates

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificati, consentire i certificati autofirmatiEnable certificate-based client authentication, allow self-signed certificates

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Impostare la proprietà **inbound__clientAuth__clientCert__allowUnknownCA** **su true** solo negli ambienti di test, in quanto in genere è possibile utilizzare certificati autofirmati. Per i carichi di lavoro di produzione, è consigliabile impostare questa proprietà su **false** e i certificati di un'autorità di certificazione (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Abilitare l'autenticazione client basata su certificati e chiave sasEnable certificate-based and sas-key client authentication

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>L'autenticazione client basata su chiave SAS consente a un modulo perimetrale non IoT di eseguire operazioni di gestione e runtime, presupponendo ovviamente che le porte API siano accessibili all'esterno della rete IoT Edge.SAS key-based client authentication allows a non-IoT edge module to do management and runtime operations assuming course the API ports are accessible outside the IoT Edge network.
