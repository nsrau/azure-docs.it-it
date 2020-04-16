---
title: Argomenti personalizzati in Griglia di eventi di AzureCustom topics in Azure Event Grid
description: Descrive gli argomenti personalizzati in Griglia di eventi di Azure.Describes custom topics in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394358"
---
# <a name="custom-topics-in-azure-event-grid"></a>Argomenti personalizzati in Griglia di eventi di AzureCustom topics in Azure Event Grid
Un argomento della griglia di eventi fornisce un endpoint in cui l'origine invia eventi. L'editore crea l'argomento di Griglia di eventi e decide se un'origine evento necessita di uno o più argomenti. L'argomento viene usato per una raccolta di eventi correlati. Per rispondere a determinati tipi di eventi, i sottoscrittori scelgono gli argomenti da sottoscrivere.

**Gli argomenti personalizzati** sono argomenti di applicazioni e di terze parti. Quando l'utente crea o gli viene assegnato l'accesso a un argomento personalizzato, tale argomento personalizzato viene visualizzato nella sottoscrizione. 

Quando si progetta l'applicazione, è possibile decidere il numero di argomenti da creare. Per soluzioni di grandi dimensioni, creare un **argomento personalizzato** per ogni categoria di **eventi correlati.** Ad esempio, si consideri un'applicazione che invia gli eventi correlati alla modifica di account utente e all'elaborazione degli ordini. È improbabile che un gestore eventi richieda entrambe le categorie di eventi. Creare due argomenti personalizzati e consentire ai gestori di eventi di sottoscrivere all'argomento di interesse. Per le soluzioni di dimensioni ridotte, è preferibile inviare tutti gli eventi a un singolo argomento. I sottoscrittori di eventi possono filtrare in base ai tipi di eventi desiderati.

## <a name="event-schema"></a>Schema di eventi
Per una panoramica dettagliata dello schema degli eventi, vedere Schema di eventi Griglia di eventi di Azure.For a detailed overview of event schema, see [Azure Event Grid event schema](event-schema.md). Per gli argomenti personalizzati, l'autore dell'evento determina l'oggetto **dati.** I dati di livello principale devono contenere gli stessi campi degli eventi standard definiti dalle risorse.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Nelle sezioni seguenti vengono forniti collegamenti a esercitazioni per creare argomenti personalizzati usando i modelli di portale di Azure, dell'interfaccia della riga di comando, PowerShell e Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Esercitazioni del portale di AzureAzure portal tutorials
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con il portale di Azure](custom-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi personalizzati. |
| [Guida introduttiva: instradare eventi personalizzati ad Archiviazione code di Azure](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |
| [Come inserire post sull'argomento personalizzato](post-to-custom-topic.md) | Descrive come pubblicare un evento in un argomento personalizzato. |


## <a name="azure-cli-tutorials"></a>Esercitazioni dell'interfaccia della riga di comando di AzureAzure CLI tutorial
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure](custom-event-quickstart.md) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi personalizzati. |
| [Interfaccia della riga di comando di Azure: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-cli-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [Interfaccia della riga di comando di Azure sottoscrive eventi per un argomento personalizzato](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|

## <a name="azure-powershell-tutorials"></a>Esercitazioni di Azure PowerShellAzure PowerShell tutorials
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con Azure PowerShell](custom-event-quickstart-powershell.md) | Illustra come usare Azure PowerShell per inviare gli eventi personalizzati. |
| [PowerShell: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-powershell-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [PowerShell: sottoscrivere eventi per un argomento personalizzato](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|

## <a name="arm-template-tutorials"></a>Esercitazioni sui modelli ARM
|Titolo  |Descrizione  |
|---------|---------|
| [Modello di Resource Manager: argomento personalizzato ed endpoint di webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Un modello di Resource Manager che crea un argomento personalizzato e una sottoscrizione per l'argomento personalizzato. Invia eventi a un webhook. |
| [Modello di Resource Manager: argomento personalizzato ed endpoint di Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Un modello di Resource Manager che crea una sottoscrizione per l'argomento personalizzato. Invia eventi in Hub eventi di Azure. |

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Argomenti di sistema](system-topics.md)
- [Domini](event-domains.md)