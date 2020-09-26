---
title: Configurare l'autenticazione client per le chiamate in ingresso-griglia di eventi di Azure IoT Edge | Microsoft Docs
description: Informazioni sulle possibili configurazioni di autenticazione client per il modulo di griglia di eventi.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290773"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurare l'autenticazione client delle chiamate in ingresso

Questa guida fornisce esempi delle possibili configurazioni di autenticazione client per il modulo di griglia di eventi. Il modulo griglia di eventi supporta due tipi di autenticazione client:

* Firma di accesso condiviso (SAS) basata su chiavi
* Basato su certificati

Vedere la guida per la [sicurezza e l'autenticazione](security-authentication.md) per tutte le configurazioni possibili.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificato, nessun certificato autofirmato

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Abilitare l'autenticazione client basata su certificati, consentire i certificati autofirmati

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
>Impostare la proprietà **inbound__clientAuth__clientCert__allowUnknownCA** su **true** solo negli ambienti di test, poiché in genere è possibile utilizzare certificati autofirmati. Per i carichi di lavoro di produzione, è consigliabile impostare questa proprietà su **false** e certificati da un'autorità di certificazione (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Abilitare l'autenticazione client basata sui certificati e sulla chiave SAS

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
>L'autenticazione client basata su chiave SAS consente a un modulo perimetrale non-Internet di eseguire operazioni di gestione e Runtime, presumendo che le porte API siano accessibili all'esterno della rete IoT Edge.
