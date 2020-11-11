---
title: Variabili di ambiente di Rilevamento anomalie
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 5148114da7fcc654b1f0af431440ce74c7bc8d31
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371833"
---
### <a name="create-an-environment-variable"></a>Creare una variabile di ambiente

>[!NOTE]
> Gli endpoint per le risorse non di valutazione create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../../cognitive-services-custom-subdomains.md). 

Usando la chiave e l'endpoint della risorsa creata, creare due variabili di ambiente per l'autenticazione:

* `ANOMALY_DETECTOR_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `ANOMALY_DETECTOR_ENDPOINT`: l'endpoint della risorsa per l'invio delle richieste API. L'aspetto sarà simile al seguente: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Usare le istruzioni per il sistema operativo in uso.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macos"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.

***