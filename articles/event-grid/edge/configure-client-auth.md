---
title: Configurare l'autenticazione client per le chiamate in ingresso-griglia di eventi di Azure IoT Edge | Microsoft Docs
description: Configurare i protocolli API esposti da griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992509"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurare l'autenticazione client delle chiamate in ingresso

Questa guida fornisce esempi delle possibili configurazioni di autenticazione client per il modulo di griglia di eventi. Il modulo griglia di eventi supporta due tipi di autenticazione client:-

* Firma di accesso condiviso (SAS) basata su chiavi
* Basato su certificati

Vedere la guida per la [sicurezza e l'autenticazione](security-authentication.md) per tutte le configurazioni possibili.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificato, nessun certificato autofirmato

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificati, consentire i certificati autofirmati

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Impostare la proprietà **inbound: clientAuth: clientCert: allowUnknownCA** su **true** solo negli ambienti di test perché è in genere consigliabile usare certificati autofirmati. Per i carichi di lavoro di produzione, è consigliabile impostare questa proprietà su **false** e certificati da un'autorità di certificazione (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Abilitare l'autenticazione client basata sui certificati e sulla chiave SAS

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>L'autenticazione client basata su chiave SAS consente a un modulo perimetrale non-Internet di eseguire operazioni di gestione e Runtime, presumendo che le porte API siano accessibili all'esterno della rete IoT Edge.
