---
title: Esempi di attività di Azure Container Registry
description: Attività di esempio di Azure Container Registry (attività ACR) per compilare, eseguire e applicare patch alle immagini del contenitore
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152853"
---
# <a name="acr-tasks-samples"></a>Esempi di attività ACR

Questo articolo contiene collegamenti ad esempi di file di `task.yaml` e Dockerfile associati per diversi scenari di [attività container Registry di Azure](container-registry-tasks-overview.md) (ACR). 

Per altri esempi, vedere il repository di [esempi di Azure][task-examples] .

## <a name="scenarios"></a>Scenari

* **Crea immagine** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Esegui contenitore** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Creazione e push di immagini** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare ed eseguire image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Creazione e push di più immagini** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Compilare e testare immagini in parallelo** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Creazione e push di immagini in più registri** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle attività di ACR:

* [Attività](container-registry-tasks-multi-step.md) in più passaggi: flussi di lavoro basati su attività ACR per la compilazione, il test e l'applicazione di patch alle immagini del contenitore nel cloud.
* [Task reference](container-registry-tasks-reference-yaml.md) (Riferimenti alle attività): tipi, proprietà e utilizzo dei passaggi di attività.
* [Repository cmd](https://github.com/AzureCR/cmd) : raccolta di contenitori come comandi per le attività ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
