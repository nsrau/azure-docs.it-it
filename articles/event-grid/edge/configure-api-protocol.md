---
title: Configurare protocolli API-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Configurare i protocolli API esposti da griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: f39968476f2fecf655e6c69bea2ff19304d2b465
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992522"
---
# <a name="configure-event-grid-api-protocols"></a>Configurare i protocolli API di griglia di eventi

Questa guida fornisce esempi delle possibili configurazioni di protocollo di un modulo di griglia di eventi. Il modulo di griglia di eventi espone l'API per le operazioni di gestione e di Runtime. Nella tabella seguente vengono acquisiti i protocolli e le porte.

| Protocollo | Port | Descrizione |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Disabilitato per impostazione predefinita. Utile solo durante i test. Non adatto per carichi di lavoro di produzione.
| HTTPS | 4438 | Default

Vedere la guida per la [sicurezza e l'autenticazione](security-authentication.md) per tutte le configurazioni possibili.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Esporre HTTPS ai moduli di Internet delle cose nella stessa rete perimetrale

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Abilitare HTTPS ad altri moduli e carichi di lavoro non-Internet

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
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
> La sezione **PortBindings** consente di eseguire il mapping delle porte interne alle porte dell'host contenitore. Questa funzionalità consente di raggiungere il modulo di griglia di eventi dall'esterno della rete IoT Edge contenitore, se il dispositivo Edge è raggiungibile pubblicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Esporre HTTP e HTTPS ai moduli di Internet delle cose nella stessa rete perimetrale

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Abilitare HTTP e HTTPS ad altri moduli e carichi di lavoro non-Internet

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
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
> Per impostazione predefinita, ogni modulo delle cose è parte del runtime IoT Edge creato dalla rete Bridge. Consente la comunicazione tra i diversi moduli dell'it nella stessa rete. **PortBindings** consente di eseguire il mapping di una porta interna del contenitore nel computer host in modo da consentire a chiunque di accedere alla porta del modulo di griglia di eventi dall'esterno.

>[!IMPORTANT]
> Mentre le porte possono essere rese accessibili all'esterno della rete IoT Edge, l'autenticazione client impone a chi è effettivamente consentito effettuare chiamate al modulo.
