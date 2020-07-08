---
title: Variabili di ambiente
description: Impostare le variabili di ambiente
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 28362a81461b63440ad752071f11b3603a979995
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86100356"
---
Usando la chiave e l'endpoint della risorsa creata, creare due variabili di ambiente per l'autenticazione:

* `FORM_RECOGNIZER_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `FORM_RECOGNIZER_ENDPOINT`: l'endpoint della risorsa per l'invio delle richieste API. L'aspetto sarà simile al seguente: 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> Gli endpoint per le risorse non di valutazione create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usare le istruzioni seguenti per impostare le variabili di ambiente nel sistema operativo.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Dopo aver aggiunto le variabili di ambiente, chiudere e riaprire la finestra della console.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macos"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Dopo avere aggiunto le variabili di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.
***
