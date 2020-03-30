---
title: Configurare l'autenticazione del sottoscrittore webhook - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurare l'autenticazione dei sottoscrittori di webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841731"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurare l'autenticazione dei sottoscrittori di webhook

Questa guida fornisce esempi delle possibili configurazioni del sottoscrittore webhook per un modulo Griglia di eventi. Per impostazione predefinita, solo gli endpoint HTTPS vengono accettati per i sottoscrittori webhook. Il modulo Griglia di eventi verrà rifiutato se il sottoscrittore presenta un certificato autofirmato.

## <a name="allow-only-https-subscriber"></a>Consenti solo sottoscrittore HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Consenti sottoscrittore HTTPS con certificato autofirmato

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Impostare `outbound__webhook__allowUnknownCA` la `true` proprietà su solo in ambienti di test, come in genere si potrebbero usare certificati autofirmati. Per i carichi di lavoro di produzione è consigliabile impostarli su **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Consentire il sottoscrittore HTTPS ma ignorare la convalida del certificatoAllow HTTPS subscriber but skip certificate validation

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Impostare `outbound__webhook__skipServerCertValidation` la `true` proprietà solo in ambienti di test poiché potrebbe non essere presente un certificato che deve essere autenticato. Per i carichi di lavoro di produzione è consigliabile impostarli su **falseFor** production workloads we recommend them to be set to false

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Consenti sia HTTP che HTTPS con certificati autofirmati

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Impostare `outbound__webhook__httpsOnly` la `false` proprietà su solo in ambienti di test come si potrebbe voler visualizzare prima un sottoscrittore HTTP. Per i carichi di lavoro di produzione è consigliabile impostarli su **trueFor** production workloads we recommend them to be set to true
