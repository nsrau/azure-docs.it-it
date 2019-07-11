---
title: Installare i contenitori di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Descrive in dettaglio le opzioni di configurazione grafico helm per il riconoscimento vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717242"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Per il riconoscimento vocale (grafico secondario: i grafici/speechToText)

Per ignorare il grafico "ombrello", aggiungere il prefisso `speechToText.` qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override di parametro corrispondente, ad esempio `speechToText.numberOfConcurrentRequest` esegue l'override `numberOfConcurrentRequest`.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Se il **vocale-** servizio è abilitato. | `false` |
| `numberOfConcurrentRequest` | Il numero di richieste simultanee per il **vocale-** service. Il grafico calcola automaticamente le risorse di CPU e memoria, in base a questo valore. | `2` |
| `optimizeForAudioFile`| Indica se il servizio deve ottimizzare per l'input audio tramite file audio. Se `true`, questo grafico dovrà allocare più risorse della CPU al servizio. | `false` |
| `image.registry`| Il **vocale-** Registro di immagini docker. | `containerpreview.azurecr.io` |
| `image.repository` | Il **vocale-** repository di immagini docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Il **vocale-** tag immagine docker. | `latest` |
| `image.pullSecrets` | I segreti di immagine per il pull il **vocale-** immagine docker. | |
| `image.pullByHash`| Indica se l'immagine docker viene effettuato il pull dall'hash. Se `true`, `image.hash` è obbligatorio. | `false` |
| `image.hash`| Il **vocale-** hash immagine docker. Utilizzato solo quando `image.pullByHash: true`.  | |
| `image.args.eula` (obbligatorio) | Indica il che accettazione della licenza. È l'unico valore valido `accept` | |
| `image.args.billing` (obbligatorio) | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica di sintesi vocale del portale di Azure. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio di Kubernetes il **vocale-** service. Vedere le [servizio Kubernetes i tipi di istruzioni](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto di provider di cloud. | `LoadBalancer` |
| `service.port`|  La porta dei **vocale-** service. | `80` |
| `service.autoScaler.enabled` | Se il [scalabilità automatica orizzontale di Pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitata. Se `true`, il `speech-to-text-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se il [Pod interruzioni del Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitata. Se `true`, il `speech-to-text-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |