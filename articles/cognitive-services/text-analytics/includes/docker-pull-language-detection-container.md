---
title: Pull Docker per il contenitore di rilevamento della linguaDocker pull for the Language Detection container
titleSuffix: Azure Cognitive Services
description: Comando pull Docker per il contenitore di rilevamento della linguaDocker pull command for Language Detection container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966700"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull Docker per il contenitore di rilevamento della linguaDocker pull for the Language Detection container

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore da Microsoft Container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il contenitore [Rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759) nell'hub Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
