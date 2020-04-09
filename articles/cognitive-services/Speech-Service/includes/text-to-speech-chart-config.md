---
title: Installare i contenitori di riconoscimento vocaleInstall Speech containers
titleSuffix: Azure Cognitive Services
description: Vengono descritte le opzioni di configurazione del grafico helm di sintesi vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874347"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Sintesi vocale (sottografico: grafici/textToSpeech)

Per sostituire il grafico "ombrello", aggiungere il prefisso `textToSpeech.` su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override `textToSpeech.numberOfConcurrentRequest` del `numberOfConcurrentRequest`parametro corrispondente, ad esempio, esegue l'override di .

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Se il servizio di **sintesi vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio di **sintesi vocale.** Questo grafico calcola automaticamente le risorse della CPU e della memoria, in base a questo valore. | `2` |
| `optimizeForTurboMode`| Se il servizio deve essere ottimizzato per l'input di testo tramite file di testo. Se `true`, questo grafico allocherà più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro delle immagini docker di **sintesi vocale.** | `containerpreview.azurecr.io` |
| `image.repository` | Archivio immagini docker **sintesi vocale.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Tag dell'immagine docker di **sintesi vocale.** | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per estrarre l'immagine della finestra mobile **di sintesi vocale.** | |
| `image.pullByHash`| Se l'immagine docker viene tirata dall'hash. Se `true` `image.hash` è necessario , | `false` |
| `image.hash`| Hash dell'immagine docker **di sintesi vocale.** Utilizzato solo `image.pullByHash: true`quando .  | |
| `image.args.eula` (obbligatorio) | Indica che hai accettato la licenza. L'unico valore valido è`accept` | |
| `image.args.billing` (obbligatorio) | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina Panoramica vocale del portale di Azure.The billing endpoint URI value is available on the Azure portal's Speech Overview page. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Il tipo di servizio Kubernetes del servizio **di sintesi vocale.** Per ulteriori informazioni, consulta le istruzioni sui [tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) e verifica il supporto del provider cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio di **sintesi vocale.** | `80` |
| `service.annotations` | Annotazioni di **sintesi vocale** per i metadati del servizio. Le annotazioni sono coppie di valori chiave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se la [scalatrice automatica del pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitata. Se `true`, `text-to-speech-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se il [budget di interruzione Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true`, `text-to-speech-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |