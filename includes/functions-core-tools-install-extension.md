---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
Quando si sviluppano funzioni in locale, è possibile installare le estensioni necessarie tramite Strumenti di base di Funzioni di Azure dal terminale o da un prompt dei comandi. Il comando `func extensions install` seguente consente di installare l'estensione dell'associazione Azure Cosmos DB:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Sostituire `<taget_version>` con una specifica versione del pacchetto. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org).
