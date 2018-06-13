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
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246271"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelli di Azure Resource Manager per Griglia di eventi

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Griglia di eventi.

| | |
|-|-|
|**Sottoscrizioni di Griglia di eventi**||
| [Argomento personalizzato e sottoscrizione con l'endpoint del webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Consente di distribuire un argomento personalizzato di Griglia di eventi. Consente di creare una sottoscrizione dell'argomento personalizzato che usa un endpoint del webhook. |
| [Sottoscrizione dell'argomento personalizzato con l'endpoint dell'hub eventi](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Consente di creare una sottoscrizione di Griglia di eventi di un argomento personalizzato già esistente. La sottoscrizione usa un hub eventi per l'endpoint. |
| [Sottoscrizione del gruppo di risorse](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Consente di sottoscrivere eventi per un gruppo di risorse. Il gruppo di risorse specificato come destinazione durante la distribuzione è l'origine degli eventi. La sottoscrizione usa un hub eventi per l'endpoint. |
| [Account di archiviazione BLOB e sottoscrizione](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per quell'account di archiviazione. |
| | |
