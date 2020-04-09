---
title: Esempi di docker del contenitore Estrazione frase chiaveKey Phrase Extraction container docker examples
titleSuffix: Azure Cognitive Services
description: Esempi di docker del contenitore Estrazione frase chiaveKey Phrase Extraction container docker examples
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1c079df3171d89da4ef40e5008aaeb08b6504e66
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878673"
---
### <a name="key-phrase-extraction-container-docker-examples"></a>Esempi di docker del contenitore Estrazione frase chiaveKey Phrase Extraction container docker examples

Gli esempi di docker seguenti riguardano il contenitore Estrazione frase chiave.

#### <a name="basic-example"></a>Esempio di base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Esempio di registrazione 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
