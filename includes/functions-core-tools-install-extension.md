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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063749"
---
Quando si sviluppano funzioni in locale, è possibile installare le estensioni necessarie tramite Strumenti di base di Funzioni di Azure dal terminale o da un prompt dei comandi. 

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il comando `func extensions install` nella cartella del progetto. Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari e li installa.

Se si vuole installare una versione specifica di un pacchetto o si vogliono installare i pacchetti prima di modificare il file *function.json*, usare il comando `func extensions install` con il nome del pacchetto, come illustrato nell'esempio seguente:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Sostituire `<target_version>` con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org).
