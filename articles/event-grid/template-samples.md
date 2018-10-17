---
title: Esempi di modelli di Azure Resource Manager - Griglia di eventi | Microsoft Docs
description: Esempi di modelli di Azure Resource Manager per Griglia di eventi
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295581"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelli di Azure Resource Manager per Griglia di eventi

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Griglia di eventi.

| | |
|-|-|
|**Sottoscrizioni di Griglia di eventi**||
| [Argomento personalizzato e sottoscrizione con l'endpoint del webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Consente di distribuire un argomento personalizzato di Griglia di eventi. Consente di creare una sottoscrizione dell'argomento personalizzato che usa un endpoint del webhook. |
| [Sottoscrizione dell'argomento personalizzato con l'endpoint dell'hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Consente di creare una sottoscrizione di Griglia di eventi di un argomento personalizzato. La sottoscrizione usa un hub eventi per l'endpoint. |
| [Sottoscrizione di Azure o sottoscrizione del gruppo di risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Consente di sottoscrivere eventi per una sottoscrizione di Azure o del gruppo di risorse. Il gruppo di risorse specificato come destinazione durante la distribuzione è l'origine degli eventi. La sottoscrizione usa un webhook per l'endpoint. |
| [Account di archiviazione BLOB e sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per quell'account di archiviazione. |
| | |
