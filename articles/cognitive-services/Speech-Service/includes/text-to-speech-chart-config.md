---
title: Installare i contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Descrive le opzioni di configurazione del grafico Helm di sintesi vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971323"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Sintesi vocale (grafico secondario: grafici/textToSpeech)

Per eseguire l'override del grafico "Umbrella", aggiungere `textToSpeech.` il prefisso su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override del parametro corrispondente, `textToSpeech.numberOfConcurrentRequest` ad esempio override. `numberOfConcurrentRequest`

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Indica se il servizio **di sintesi vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio **di sintesi vocale** . Questo grafico calcola automaticamente le risorse di CPU e memoria in base a questo valore. | `2` |
| `optimizeForTurboMode`| Indica se il servizio deve essere ottimizzato per l'input di testo tramite file di testo. Se `true`, il grafico alloca più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro immagini Docker **per sintesi vocale** . | `containerpreview.azurecr.io` |
| `image.repository` | Archivio di immagini Docker **di sintesi vocale** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Tag dell'immagine Docker **di sintesi vocale** . | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per il pull dell'immagine Docker **di sintesi vocale** . | |
| `image.pullByHash`| Indica se viene eseguito il pull dell'immagine Docker in base all'hash. Se `true` ,`image.hash` è obbligatorio. | `false` |
| `image.hash`| Hash dell'immagine Docker **di sintesi vocale** . Utilizzato solo quando `image.pullByHash: true`.  | |
| `image.args.eula`necessaria | Indica che la licenza è stata accettata. L'unico valore valido è`accept` | |
| `image.args.billing`necessaria | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure. | |
| `image.args.apikey`necessaria | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio Kubernetes del servizio di sintesi **vocale** . Vedere le [istruzioni sui tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto del provider di servizi cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio di sintesi **vocale** . | `80` |
| `service.annotations` | Annotazioni di sintesi **vocale** per i metadati del servizio. Le annotazioni sono coppie chiave-valore. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Indica se il ridimensionamento automatico del [Pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitato. Se `true`, il `text-to-speech-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Indica se il [budget di interferenza Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true`, il `text-to-speech-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |