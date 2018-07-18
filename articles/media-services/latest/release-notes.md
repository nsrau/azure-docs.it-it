---
title: Note sulla versione di Servizi multimediali v3 | Microsoft Docs
description: Per stare al passo con gli sviluppi più recenti, questo articolo fornisce gli ultimi aggiornamenti relativi a Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782640"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Note sulla versione di Servizi multimediali v3 (anteprima) 

Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

* Versioni più recenti
* Problemi noti
* Correzioni di bug
* Funzionalità deprecate
* Modifiche pianificate

## <a name="may-07-2018"></a>07 maggio 2018

### <a name="net-sdk"></a>.Net SDK

Le funzionalità seguenti sono presenti in .Net SDK:

1. **Transforms** e **Jobs** per codificare o analizzare i contenuti multimediali. Per alcuni esempi, vedere [Eseguire lo streaming di file](stream-files-tutorial-with-api.md) e [Analyze](analyze-videos-tutorial-with-api.md) (Analizzare).
2. **StreamingLocators** per pubblicare e ed eseguire lo streaming dei contenuti ai dispositivi degli utenti finali
3. **StreamingPolicies** e **ContentKeyPolicies** per configurare il recapito della chiave e la protezione (Digital Rights Management) per la distribuzione dei contenuti.
4. **LiveEvents** e **LiveOutputs** per configurare l'inserimento e l'archiviazione dei contenuti in streaming live.
5. **Assets** per archiviare e pubblicare i contenuti multimediali in Archiviazione di Azure. 
6. **StreamingEndpoints** per configurare e ridimensionare la creazione dinamica dei pacchetti, la crittografia e lo streaming di contenuti multimediali live e on demand.

### <a name="known-issues"></a>Problemi noti

Problema noto:

Quando si invia un processo con un URL HTTPS (JobInputHttp) che punta ai contenuti di origine, assicurarsi che il server HTTP supporti la richiesta 'HEAD'. In caso contrario, il processo verrà rifiutato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica](media-services-overview.md)
