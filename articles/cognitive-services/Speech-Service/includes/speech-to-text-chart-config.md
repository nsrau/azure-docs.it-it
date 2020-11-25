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
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002281"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Sintesi vocale (Sottografico: grafici/speechToText)

Per eseguire l'override del grafico "Umbrella", aggiungere il prefisso `speechToText.` su qualsiasi parametro per renderlo più specifico. Ad esempio, eseguirà l'override del parametro corrispondente, ad esempio `speechToText.numberOfConcurrentRequest` override `numberOfConcurrentRequest` .

|Parametro|Descrizione|Predefinito|
| -- | -- | -- |
| `enabled` | Indica se il servizio **di riconoscimento vocale** è abilitato. | `false` |
| `numberOfConcurrentRequest` | Numero di richieste simultanee per il servizio **di riconoscimento vocale** . Questo grafico calcola automaticamente le risorse di CPU e memoria in base a questo valore. | `2` |
| `optimizeForAudioFile`| Indica se il servizio deve essere ottimizzato per l'input audio tramite file audio. Se `true` , il grafico alloca più risorse della CPU al servizio. | `false` |
| `image.registry`| Registro delle immagini Docker **per il riconoscimento vocale** . | `containerpreview.azurecr.io` |
| `image.repository` | Repository di immagini Docker **con testo vocale** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Tag dell'immagine Docker **di testo** . | `latest` |
| `image.pullSecrets` | I segreti dell'immagine per il pull dell'immagine Docker **di sintesi vocale** . | |
| `image.pullByHash`| Indica se viene eseguito il pull dell'immagine Docker in base all'hash. Se `true` , `image.hash` è obbligatorio. | `false` |
| `image.hash`| Hash dell'immagine docker del **testo** . Utilizzato solo quando `image.pullByHash: true` .  | |
| `image.args.eula` (obbligatorio) | Indica che la licenza è stata accettata. L'unico valore valido è `accept` | |
| `image.args.billing` (obbligatorio) | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure. | |
| `image.args.apikey` (obbligatorio) | Si usa per rilevare le informazioni di fatturazione. ||
| `service.type` | Tipo di servizio Kubernetes del servizio di **riconoscimento vocale** . Vedere le [istruzioni sui tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) per altri dettagli e verificare il supporto del provider di servizi cloud. | `LoadBalancer` |
| `service.port`|  Porta del servizio di **riconoscimento vocale** . | `80` |
| `service.annotations` | Annotazioni **di sintesi vocale** per i metadati del servizio. Le annotazioni sono coppie chiave-valore. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Indica se il [ridimensionamento automatico del Pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitato. Se `true` , il `speech-to-text-autoscaler` verrà distribuito nel cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Indica se il [budget di interferenza Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se `true` , il `speech-to-text-poddisruptionbudget` verrà distribuito nel cluster Kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Analisi dei sentimenti (grafico secondario: grafici/speechToText)

A partire da v 2.2.0 del contenitore riconoscimento vocale e v 0.2.0 del grafico Helm, i parametri seguenti vengono usati per l'analisi dei sentimenti usando il API Analisi del testo.

|Parametro|Descrizione|Valori|Predefinito|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Indica se il servizio **di analisi del testo** è abilitato| true/false| `false`|
|`textanalytics.image.registry`| Registro immagini Docker **di analisi del testo**| registro immagini Docker valido| |
|`textanalytics.image.repository`| Archivio di immagini Docker **di analisi del testo**| repository di immagini Docker valido| |
|`textanalytics.image.tag`| Tag dell'immagine Docker **di analisi del testo**| Tag di immagine Docker valido| |
|`textanalytics.image.pullSecrets`| Immagini segrete per il pull dell'immagine Docker di **analisi del testo**| nome dei segreti validi| |
|`textanalytics.image.pullByHash`| Specifica se si sta effettuando il pull dell'immagine Docker in base all'hash.  Se `yes` , `image.hash` è necessario avere anche. Se `no` impostato su, impostarlo come ' false '. Il valore predefinito è `false`.| true/false| `false`|
|`textanalytics.image.hash`| Hash dell'immagine Docker **di analisi del testo** . Utilizzarlo solo con `image.pullByHash:true` .| hash dell'immagine Docker valido | |
|`textanalytics.image.args.eula`| Uno degli argomenti obbligatori per il contenitore **Text-Analytics** , che indica che è stata accettata la licenza. Il valore di questa opzione deve essere: `accept` .| `accept`, se si vuole usare il contenitore | |
|`textanalytics.image.args.billing`| Uno degli argomenti obbligatori per il contenitore **Text-Analytics** , che specifica l'URI dell'endpoint di fatturazione. Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure.|URI dell'endpoint di fatturazione valido||
|`textanalytics.image.args.apikey`| Uno degli argomenti obbligatori per il contenitore **Text-Analytics** , che consente di tenere traccia delle informazioni di fatturazione.| APIKEY valido||
|`textanalytics.cpuRequest`| CPU richiesta per il contenitore di **analisi del testo**| INT| `3000m`|
|`textanalytics.cpuLimit`| CPU limitata per il contenitore di **analisi del testo**| | `8000m`|
|`textanalytics.memoryRequest`| Memoria richiesta per il contenitore di **analisi del testo**| | `3Gi`|
|`textanalytics.memoryLimit`| Memoria limitata per il contenitore di **analisi del testo**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Il suffisso URI dell'analisi dei sentimenti, l'intero URI è nel formato "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Tipo di servizio di **analisi del testo** in Kubernetes. Vedere i [tipi di servizio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | tipo di servizio Kubernetes valido | `LoadBalancer` |
|`textanalytics.service.port`| La porta del servizio di **analisi del testo**| INT| `50085`|
|`textanalytics.service.annotations`| Le annotazioni che gli utenti possono aggiungere ai metadati del servizio **analisi del testo** . Ad esempio:<br/> **annotazioni**<br/>`   `**some/annotation1: value1**<br/>`  `**some/annotation2: value2** | annotazioni, una per ogni riga| |
|`textanalytics.serivce.autoScaler.enabled`| Indica se la funzionalità di [scalabilità automatica del Pod orizzontale](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) è abilitata. Se abilitata, `text-analytics-autoscaler` verrà distribuita nel cluster Kubernetes | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| Indica se il [budget di interferenza Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) è abilitato. Se abilitata, `text-analytics-poddisruptionbudget` verrà distribuita nel cluster Kubernetes| true/false| `true`|
