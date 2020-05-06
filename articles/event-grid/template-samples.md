---
title: Esempi di modelli di Azure Resource Manager - Griglia di eventi | Microsoft Docs
description: Questo articolo include un elenco di esempi di modelli di Azure Resource Manager per Griglia di eventi di Azure in GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76720623"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelli di Azure Resource Manager per Griglia di eventi

Per la sintassi e le proprietà JSON da usare in un modello, vedere i [tipi di risorsa Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Griglia di eventi.

| | |
|-|-|
|**Sottoscrizioni di Griglia di eventi**||
| [Argomento personalizzato e sottoscrizione con l'endpoint del webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Consente di distribuire un argomento personalizzato di Griglia di eventi. Consente di creare una sottoscrizione dell'argomento personalizzato che usa un endpoint del webhook. |
| [Sottoscrizione dell'argomento personalizzato con l'endpoint dell'hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Consente di creare una sottoscrizione di Griglia di eventi di un argomento personalizzato. La sottoscrizione usa un hub eventi per l'endpoint. |
| [Sottoscrizione di Azure o sottoscrizione del gruppo di risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Consente di sottoscrivere eventi per una sottoscrizione di Azure o del gruppo di risorse. Il gruppo di risorse specificato come destinazione durante la distribuzione è l'origine degli eventi. La sottoscrizione usa un webhook per l'endpoint. |
| [Account di archiviazione BLOB e sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per quell'account di archiviazione. |
| | |
