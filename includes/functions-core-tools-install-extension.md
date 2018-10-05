---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044510"
---
Quando si sviluppano funzioni in locale, è possibile installare le estensioni necessarie tramite Strumenti di base di Funzioni di Azure dal terminale o da un prompt dei comandi.

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il seguente comando nella cartella del progetto.

```bash
func extensions install
```

Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari, li installa e ricompila il progetto di estensione. Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.

Se si vuole installare una versione specifica di un pacchetto o si vogliono installare i pacchetti prima di modificare il file *function.json*, usare il comando `func extensions install` con il nome del pacchetto, come illustrato nell'esempio seguente:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Sostituire `<target_version>` con una specifica versione del pacchetto, come `3.0.0-beta5`. Le versioni valide sono elencate nelle pagine dei singoli pacchetti in [NuGet.org](https://nuget.org).
