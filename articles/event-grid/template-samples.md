---
title: Esempi di modelli di Azure Resource Manager - Griglia di eventi | Microsoft Docs
description: Questo articolo include un elenco di esempi di modelli di Azure Resource Manager per Griglia di eventi di Azure in GitHub.
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119056"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelli di Azure Resource Manager per Griglia di eventi

Per la sintassi e le proprietà JSON da usare in un modello, vedere i [tipi di risorsa Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Griglia di eventi.

## <a name="event-grid-subscriptions"></a>Sottoscrizioni di Griglia di eventi
- [Sottoscrizione e argomento personalizzati con endpoint webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid): consente di distribuire un argomento personalizzato di Griglia di eventi. Consente di creare una sottoscrizione dell'argomento personalizzato che usa un endpoint del webhook. 
- [Sottoscrizione di un argomento personalizzato con l'endpoint EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler): consente di creare una sottoscrizione di Griglia di eventi per un argomento personalizzato. La sottoscrizione usa un hub eventi per l'endpoint. 
- [Sottoscrizione di Azure o sottoscrizione del gruppo di risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook): consente di sottoscrivere gli eventi per un gruppo di risorse o una sottoscrizione di Azure. Il gruppo di risorse specificato come destinazione durante la distribuzione è l'origine degli eventi. La sottoscrizione usa un webhook per l'endpoint. 
- [Account di archiviazione BLOB e sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage): consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per tale account di archiviazione. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli esempi seguenti:

- [Esempi di PowerShell](powershell-samples.md)
- [Esempi dell'interfaccia della riga di comando](cli-samples.md)
