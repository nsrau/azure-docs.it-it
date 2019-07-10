---
title: Installare i contenitori di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Descrive in dettaglio le opzioni di configurazione grafico helm sintesi vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711494"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Sintesi vocale (grafico secondario: i grafici/textToSpeech)

Per ignorare il grafico "ombrello", aggiungere il prefisso `textToSpeech.` qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override di parametro corrispondente, ad esempio `textToSpeech.numberOfConcurrentRequest` esegue l'override `numberOfConcurrentRequest`.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Se il **sintesi vocale** servizio è abilitato. | `false` |
| `numberOfConcurrentRequest` | Il numero di richieste simultanee per il **sintesi vocale** servizio. Il grafico calcola automaticamente le risorse di CPU e memoria, in base a questo valore. | `2` |
| `optimizeForTurboMode`| Indica se il servizio necessita per ottimizzare per il testo di input tramite i file di testo. Se `true`, questo grafico dovrà allocare più risorse della CPU al servizio. | `false` |
| `image.registry`| Il **sintesi vocale** Registro di immagini docker. | `containerpreview.azurecr.io` |
| `image.repository` | Il **sintesi vocale** repository di immagini docker. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Il **sintesi vocale** tag immagine docker. | `latest` |
| `image.pullSecrets` | I segreti di immagine per il pull il **sintesi vocale** immagine docker. | |
| `image.pullByHash`| Indica se l'immagine docker viene effettuato il pull dall'hash. Se `true`, `image.hash` è obbligatorio. | `false` |
| `image.hash`| Il **sintesi vocale** hash immagine docker. Utilizzato solo quando `image.pullByHash: true`.  | |
| `image.args.eula` (obbligatorio) | Indica il che accettazione della licenza. È l'unico valore valido `accept` | |
| `image.args.billing` (obbligatorio) | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica di sintesi vocale del portale di Azure. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio di Kubernetes il **sintesi vocale** servizio. Vedere le [servizio Kubernetes i tipi di istruzioni](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto di provider di cloud. | `LoadBalancer` |
| `service.port`|  La porta dei **sintesi vocale** servizio. | `80` |
| `service.autoScaler.enabled` | Se il [scalabilità automatica orizzontale di Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitata. Se `true`, il `text-to-speech-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se il [Pod interruzioni del Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitata. Se `true`, il `text-to-speech-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |