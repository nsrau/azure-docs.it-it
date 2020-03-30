---
title: Configurare i protocolli API - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurare i protocolli API esposti da Griglia di eventi in Edge Edge.Configure API protocols exposed by Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841811"
---
# <a name="configure-event-grid-api-protocols"></a>Configurare i protocolli API griglia di eventi

Questa guida fornisce esempi delle possibili configurazioni di protocollo di un modulo Griglia di eventi. Il modulo Griglia di eventi espone l'API per le operazioni di gestione e runtime. Nella tabella seguente vengono acquisiti i protocolli e le porte.

| Protocollo | Porta | Descrizione |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Disattivato per impostazione predefinita. Utile solo durante il test. Non adatto per i carichi di lavoro di produzione.
| HTTPS | 4438 | Predefinito

Per tutte le configurazioni possibili, vedere Guida alla [sicurezza e all'autenticazione.](security-authentication.md)

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Esporre HTTPS ai moduli IoT nella stessa rete perimetrale

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Abilitare HTTPS ad altri moduli IoT e carichi di lavoro non IoTEnable HTTPS to other IoT modules and non-IoT workloads

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> La sezione **PortBindings** consente di eseguire il mapping delle porte interne alle porte dell'host contenitore. Questa funzionalità consente di raggiungere il modulo Griglia di eventi dall'esterno della rete contenitore IoT Edge, se il dispositivo edge IoT è raggiungibile pubblicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Esporre HTTP e HTTPS ai moduli IoT nella stessa rete perimetrale

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Abilitare HTTP e HTTPS ad altri moduli IoT e carichi di lavoro non IoTEnable HTTP and HTTPS to other IoT modules and non-IoT workloads

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Per impostazione predefinita, ogni modulo IoT fa parte del runtime IoT Edge creato dalla rete bridge. Consente a diversi moduli IoT sulla stessa rete di comunicare tra loro. **PortBindings** consente di eseguire il mapping di una porta interna del contenitore nel computer host, consentendo in tal modo a chiunque di accedere alla porta del modulo Griglia di eventi dall'esterno.

>[!IMPORTANT]
> Mentre le porte possono essere rese accessibili all'esterno della rete IoT Edge, l'autenticazione client impone a chi è effettivamente autorizzato a effettuare chiamate nel modulo.
