---
title: Installare i contenitori di riconoscimento vocaleInstall Speech containers
titleSuffix: Azure Cognitive Services
description: Vengono descritte le opzioni di configurazione del grafico helm da voce al testo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971346"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Sintesi vocale (sottografico: grafici/speechToText)

Per sostituire il grafico "ombrello", aggiungere il prefisso `speechToText.` su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override `speechToText.numberOfConcurrentRequest` del `numberOfConcurrentRequest`parametro corrispondente, ad esempio, esegue l'override di .

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Se il servizio **di sintesi vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio **di sintesi vocale.** Questo grafico calcola automaticamente le risorse della CPU e della memoria, in base a questo valore. | `2` |
| `optimizeForAudioFile`| Se il servizio deve ottimizzare per l'input audio tramite file audio. Se `true`, questo grafico allocherà più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro delle immagini della finestra di dialogo **di sintesi vocale.** | `containerpreview.azurecr.io` |
| `image.repository` | Archivio immagini docker **vocale su testo.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Tag dell'immagine docker **vocale su testo.** | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per estrarre l'immagine della finestra mobile **di sintesi vocale.** | |
| `image.pullByHash`| Se l'immagine docker viene tirata dall'hash. Se `true` `image.hash` è necessario , | `false` |
| `image.hash`| Hash dell'immagine docker **da sintesi vocale.** Utilizzato solo `image.pullByHash: true`quando .  | |
| `image.args.eula` (obbligatorio) | Indica che hai accettato la licenza. L'unico valore valido è`accept` | |
| `image.args.billing` (obbligatorio) | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina Panoramica vocale del portale di Azure.The billing endpoint URI value is available on the Azure portal's Speech Overview page. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Il tipo di servizio Kubernetes del servizio **di sintesi vocale.** Per ulteriori informazioni, consulta le istruzioni sui [tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) e verifica il supporto del provider cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio **di sintesi vocale.** | `80` |
| `service.annotations` | Annotazioni **di sintesi vocale** per i metadati del servizio. Le annotazioni sono coppie di valori chiave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se la [scalatrice automatica del pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitata. Se `true`, `speech-to-text-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se il [budget di interruzione Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true`, `speech-to-text-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |