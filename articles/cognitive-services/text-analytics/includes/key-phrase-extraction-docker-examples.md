---
title: Esempi di docker del contenitore Estrazione frase chiaveKey Phrase Extraction container docker examples
titleSuffix: Azure Cognitive Services
description: Esempi di docker del contenitore Estrazione frase chiaveKey Phrase Extraction container docker examples
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: bc0375369db351038c7ac550cbe51415a0b3e069
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71148433"
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
