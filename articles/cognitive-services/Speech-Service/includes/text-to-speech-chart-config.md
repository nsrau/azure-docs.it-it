---
title: Installare i contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Descrive le opzioni di configurazione del grafico Helm di sintesi vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002282"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Sintesi vocale (grafico secondario: grafici/textToSpeech)

Per eseguire l'override del grafico "Umbrella", aggiungere il prefisso `textToSpeech.` su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override del parametro corrispondente, ad esempio `textToSpeech.numberOfConcurrentRequest` override `numberOfConcurrentRequest` .

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Indica se il servizio **di sintesi vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio **di sintesi vocale** . Questo grafico calcola automaticamente le risorse di CPU e memoria in base a questo valore. | `2` |
| `optimizeForTurboMode`| Indica se il servizio deve essere ottimizzato per l'input di testo tramite file di testo. Se `true` , il grafico alloca più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro immagini Docker **per sintesi vocale** . | `containerpreview.azurecr.io` |
| `image.repository` | Archivio di immagini Docker **di sintesi vocale** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Tag dell'immagine Docker **di sintesi vocale** . | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per il pull dell'immagine Docker **di sintesi vocale** . | |
| `image.pullByHash`| Indica se viene eseguito il pull dell'immagine Docker in base all'hash. Se `true` , `image.hash` è obbligatorio. | `false` |
| `image.hash`| Hash dell'immagine Docker **di sintesi vocale** . Utilizzato solo quando `image.pullByHash: true` .  | |
| `image.args.eula` (obbligatorio) | Indica che la licenza è stata accettata. L'unico valore valido è `accept` | |
| `image.args.billing` (obbligatorio) | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio Kubernetes del servizio di sintesi **vocale** . Vedere le [istruzioni sui tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto del provider di servizi cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio di sintesi **vocale** . | `80` |
| `service.annotations` | Annotazioni di sintesi **vocale** per i metadati del servizio. Le annotazioni sono coppie chiave-valore. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Indica se il [ridimensionamento automatico del Pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitato. Se `true` , il `text-to-speech-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Indica se il [budget di interferenza Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true` , il `text-to-speech-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |