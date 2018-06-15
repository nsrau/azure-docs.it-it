---
title: Esempi di interfaccia della riga di comando di Azure - Servizi multimediali di Azure | Microsoft Docs
description: Esempi di interfaccia della riga di comando di Azure per Servizi multimediali di Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: acc92662aa5b727656a8eda368ba6d78a87d9ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640890"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Esempi di interfaccia della riga di comando di Azure per Servizi multimediali di Azure

La tabella seguente include collegamenti a esempi dell'interfaccia della riga di comando di Azure per Servizi multimediali di Azure.

|  |  |
|---|---|
|**Account**||
| [Creare un account di Servizi multimediali di Azure.](./scripts/cli-create-account.md) | Crea un account di Servizi multimediali di Azure. Crea anche un'entità servizio che può essere usata per accedere alle API e gestire l'account a livello di codice. |
| [Reimpostare le credenziali dell'account](./scripts/cli-reset-account-credentials.md)|Reimposta le credenziali dell'account e ripristina le impostazioni di app.config.|
|**Asset**||
| [Creare gli asset](./scripts/cli-create-asset.md)|Crea un asset di Servizi multimediali dove caricare il contenuto.|
| [Caricare un file](./scripts/cli-upload-file-asset.md)|Carica un file locale in un contenitore di archiviazione.|
| [Pubblicare un asset](./scripts/cli-publish-asset.md)| Crea un localizzatore di streaming e recupera gli URL di streaming. |
| **Trasformazioni** e **processi**||
| [Creare trasformazioni](./scripts/cli-create-transform.md)|Mostra come creare trasformazioni. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe".<br/> È necessario verificare sempre se esiste già una trasformazione con il nome e il "recipe" desiderati. Se esiste, riutilizzarla. |
| [Creare processi](./scripts/cli-create-jobs.md)|Invia un processo a una semplice trasformazione di codifica usando URL HTTPs.|
| [Creare una Griglia di eventi](./scripts/cli-create-event-grid.md)|Crea una sottoscrizione di Griglia di eventi a livello di account per le modifiche dello stato del processo.|

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
