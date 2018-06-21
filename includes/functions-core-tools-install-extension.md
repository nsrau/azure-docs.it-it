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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726888"
---
Quando si sviluppano funzioni in locale, è possibile installare le estensioni necessarie tramite Strumenti di base di Funzioni di Azure dal terminale o da un prompt dei comandi. 

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il comando `func extensions install` nella cartella del progetto. Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari e li installa.

Se si vuole installare una versione specifica di un pacchetto o si vogliono installare i pacchetti prima di modificare il file *function.json*, usare il comando `func extensions install` con il nome del pacchetto, come illustrato nell'esempio seguente:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Sostituire `<target_version>` con una specifica versione del pacchetto. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org).
