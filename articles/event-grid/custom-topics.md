---
title: Argomenti personalizzati in griglia di eventi di Azure
description: Vengono descritti gli argomenti personalizzati in griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113786"
---
# <a name="custom-topics-in-azure-event-grid"></a>Argomenti personalizzati in griglia di eventi di Azure
Un argomento di griglia di eventi fornisce un endpoint in cui l'origine invia eventi. L'editore crea l'argomento di Griglia di eventi e decide se un'origine evento necessita di uno o più argomenti. L'argomento viene usato per una raccolta di eventi correlati. Per rispondere a determinati tipi di eventi, i sottoscrittori scelgono gli argomenti da sottoscrivere.

Gli **argomenti personalizzati** sono argomenti dell'applicazione e di terze parti. Quando l'utente crea o gli viene assegnato l'accesso a un argomento personalizzato, tale argomento personalizzato viene visualizzato nella sottoscrizione. 

Quando si progetta l'applicazione, è possibile decidere il numero di argomenti da creare. Per le soluzioni di grandi dimensioni, creare un **argomento personalizzato** per **ogni categoria di eventi correlati**. Ad esempio, si consideri un'applicazione che invia gli eventi correlati alla modifica di account utente e all'elaborazione degli ordini. È improbabile che un gestore eventi richieda entrambe le categorie di eventi. Creare due argomenti personalizzati e consentire ai gestori di eventi di sottoscrivere all'argomento di interesse. Per le soluzioni di dimensioni ridotte, è preferibile inviare tutti gli eventi a un singolo argomento. I sottoscrittori di eventi possono filtrare in base ai tipi di eventi desiderati.

## <a name="event-schema"></a>Schema di eventi
Per una panoramica dettagliata dello schema di eventi, vedere [schema di eventi di griglia di eventi di Azure](event-schema.md). Per gli argomenti personalizzati, l'autore di eventi determina l'oggetto **dati** . I dati di livello principale devono contenere gli stessi campi degli eventi standard definiti dalle risorse.

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

Le sezioni seguenti forniscono collegamenti alle esercitazioni per creare argomenti personalizzati usando portale di Azure, CLI, PowerShell e i modelli di Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Esercitazioni portale di Azure
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con il portale di Azure](custom-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi personalizzati. |
| [Guida introduttiva: instradare eventi personalizzati ad Archiviazione code di Azure](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |
| [Come inserire post sull'argomento personalizzato](post-to-custom-topic.md) | Descrive come pubblicare un evento in un argomento personalizzato. |


## <a name="azure-cli-tutorials"></a>Esercitazioni CLI di Azure
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure](custom-event-quickstart.md) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi personalizzati. |
| [Interfaccia della riga di comando di Azure: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-cli-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [Interfaccia della riga di comando di Azure sottoscrive eventi per un argomento personalizzato](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|

## <a name="azure-powershell-tutorials"></a>Esercitazioni Azure PowerShell
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con Azure PowerShell](custom-event-quickstart-powershell.md) | Illustra come usare Azure PowerShell per inviare gli eventi personalizzati. |
| [PowerShell: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-powershell-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [PowerShell: sottoscrivere eventi per un argomento personalizzato](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|

## <a name="arm-template-tutorials"></a>Esercitazioni sul modello ARM
|Titolo  |Descrizione  |
|---------|---------|
| [Modello di Resource Manager: argomento personalizzato ed endpoint di webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Un modello di Resource Manager che crea un argomento personalizzato e una sottoscrizione per l'argomento personalizzato. Invia eventi a un webhook. |
| [Modello di Resource Manager: argomento personalizzato ed endpoint di Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Un modello di Resource Manager che crea una sottoscrizione per l'argomento personalizzato. Invia eventi in Hub eventi di Azure. |

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Argomenti di sistema](system-topics.md)
- [Domini](event-domains.md)