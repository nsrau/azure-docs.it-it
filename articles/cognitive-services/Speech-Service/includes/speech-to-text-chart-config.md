---
title: Installare i contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Descrive le opzioni di configurazione del grafico Helm per sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421895"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Sintesi vocale (Sottografico: grafici/speechToText)

Per eseguire l'override del grafico "Umbrella", aggiungere `speechToText.` il prefisso su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override del parametro corrispondente, `speechToText.numberOfConcurrentRequest` ad esempio `numberOfConcurrentRequest`override.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Indica se il servizio **di riconoscimento vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio **di riconoscimento vocale** . Questo grafico calcola automaticamente le risorse di CPU e memoria in base a questo valore. | `2` |
| `optimizeForAudioFile`| Indica se il servizio deve essere ottimizzato per l'input audio tramite file audio. Se `true`, il grafico alloca più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro delle immagini Docker **per il riconoscimento vocale** . | `containerpreview.azurecr.io` |
| `image.repository` | Repository di immagini Docker **con testo vocale** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Tag dell'immagine Docker **di testo** . | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per il pull dell'immagine Docker **di sintesi vocale** . | |
| `image.pullByHash`| Indica se viene eseguito il pull dell'immagine Docker in base all'hash. Se `true`, `image.hash` è obbligatorio. | `false` |
| `image.hash`| Hash dell'immagine docker del **testo** . Utilizzato solo quando `image.pullByHash: true`.  | |
| `image.args.eula` (obbligatorio) | Indica che la licenza è stata accettata. L'unico valore valido è`accept` | |
| `image.args.billing` (obbligatorio) | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio Kubernetes del servizio di **riconoscimento vocale** . Vedere le [istruzioni sui tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto del provider di servizi cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio di **riconoscimento vocale** . | `80` |
| `service.annotations` | Annotazioni **di sintesi vocale** per i metadati del servizio. Le annotazioni sono coppie chiave-valore. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Indica se il [ridimensionamento automatico del Pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitato. Se `true`, il `speech-to-text-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Indica se il [budget di interferenza Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true`, il `speech-to-text-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |