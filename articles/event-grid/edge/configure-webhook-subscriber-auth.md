---
title: "Configurare l'autenticazione del Sottoscrittore webhook: griglia di eventi di Azure IoT Edge | Microsoft Docs"
description: Configurare l'autenticazione del Sottoscrittore webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992483"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurare l'autenticazione del Sottoscrittore webhook

Questa guida fornisce esempi delle possibili configurazioni del sottoscrittore del webhook per un modulo di griglia di eventi. Per impostazione predefinita, per i sottoscrittori del webhook sono accettati solo endpoint HTTPS. Il modulo di griglia di eventi rifiuterà se il Sottoscrittore presenta un certificato autofirmato.

## <a name="allow-only-https-subscriber"></a>Consenti solo Sottoscrittore HTTPS

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Consenti Sottoscrittore HTTPS con certificato autofirmato

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Impostare la proprietà `outbound:webhook:allowUnknownCA` `true` solo negli ambienti di test, in quanto è possibile utilizzare certificati autofirmati. Per i carichi di lavoro di produzione, è consigliabile impostare su **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Consenti Sottoscrittore HTTPS ignorando la convalida del certificato

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Impostare la proprietà `outbound:webhook:skipServerCertValidation` su `true` solo negli ambienti di test perché potrebbe non essere presente un certificato che deve essere autenticato. Per i carichi di lavoro di produzione, è consigliabile impostare su **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Consenti sia HTTP che HTTPS con certificati autofirmati

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Impostare la proprietà `outbound:webhook:httpsOnly` su `false` solo negli ambienti di test perché potrebbe essere necessario prima visualizzare un Sottoscrittore HTTP. Per i carichi di lavoro di produzione, è consigliabile impostare su **true**
