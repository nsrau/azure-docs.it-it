---
title: "Configurare l'autenticazione del Sottoscrittore webhook: griglia di eventi di Azure IoT Edge | Microsoft Docs"
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841731"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurare l'autenticazione dei sottoscrittori di webhook

Questa guida fornisce esempi delle possibili configurazioni del sottoscrittore del webhook per un modulo di griglia di eventi. Per impostazione predefinita, per i sottoscrittori del webhook sono accettati solo endpoint HTTPS. Il modulo di griglia di eventi rifiuterà se il Sottoscrittore presenta un certificato autofirmato.

## <a name="allow-only-https-subscriber"></a>Consenti solo Sottoscrittore HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Consenti Sottoscrittore HTTPS con certificato autofirmato

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
>Impostare la proprietà `outbound__webhook__allowUnknownCA` `true` solo negli ambienti di test, in quanto è possibile utilizzare certificati autofirmati. Per i carichi di lavoro di produzione, è consigliabile impostare su **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Consenti Sottoscrittore HTTPS ignorando la convalida del certificato

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
>Impostare la proprietà `outbound__webhook__skipServerCertValidation` su `true` solo negli ambienti di test perché potrebbe non essere presente un certificato che deve essere autenticato. Per i carichi di lavoro di produzione, è consigliabile impostare su **false**

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
>Impostare la proprietà `outbound__webhook__httpsOnly` su `false` solo negli ambienti di test perché potrebbe essere necessario prima visualizzare un Sottoscrittore HTTP. Per i carichi di lavoro di produzione, è consigliabile impostare su **true**
