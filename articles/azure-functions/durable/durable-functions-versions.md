---
title: Panoramica delle versioni di Funzioni durevoli - Funzioni di AzureDurable Functions versions overview - Azure Functions
description: Ulteriori informazioni sulle versioni di Funzioni durevoli.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152891"
---
# <a name="durable-functions-versions-overview"></a>Panoramica delle versioni di Funzioni durevoli

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non si ha già familiarità con funzioni durevoli, vedere la [documentazione introduttiva](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nuove funzionalità in 2.x

In questa sezione vengono descritte le funzionalità delle funzioni durevoli aggiunte nella versione 2.x.

### <a name="durable-entities"></a>Entità durevoli

In Funzioni durevoli 2.x è stato introdotto un nuovo concetto di [funzioni di entità.](durable-functions-entities.md)

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato, note come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. A differenza delle funzioni del ritor, le funzioni di entità non hanno vincoli di codice specifici. Le funzioni di entità, inoltre, gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.

Per altre informazioni, vedere l'articolo [Entità durevoli.](durable-functions-entities.md)

### <a name="durable-http"></a>HTTP durevole

In Funzioni durevoli 2.x è stata introdotta una nuova funzionalità [HTTP durevole](durable-functions-http-features.md#consuming-http-apis) che consente di:

* Chiamare le API HTTP direttamente dalle funzioni di orchestrazione (con alcune limitazioni documentate).
* Implementare il polling automatico dello stato HTTP 202 sul lato client.
* Supporto predefinito per [le identità gestite](../../active-directory/managed-identities-azure-resources/overview.md)di Azure.

Per altre informazioni, vedere l'articolo [Funzionalità HTTP.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Eseguire la migrazione da 1.x a 2.xMigrate from 1.x to 2.x

In questa sezione viene descritto come eseguire la migrazione della versione 1.x delle funzioni durevoli alla versione 2.x per sfruttare le nuove funzionalità.

### <a name="upgrade-the-extension"></a>Aggiornare l'estensione

Installare la versione 2.x dell'estensione delle [associazioni di funzioni durevoli](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) nel progetto. Per altre informazioni, vedere [Registrare le estensioni](../functions-bindings-register.md) di associazione di Funzioni di Azure.See Register Azure Functions binding extensions for more information.

### <a name="update-your-code"></a>Aggiornare il codice

Funzioni durevoli 2.x introduce diverse modifiche di rilievo. Le applicazioni Durable Functions 1.x non sono compatibili con Durable Functions 2.x senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche che è necessario apportare quando si aggiornano le funzioni della versione 1.x a 2.x.

#### <a name="hostjson-schema"></a>Schema Host.json

Funzioni durevoli 2.x utilizza un nuovo schema host.json. Le principali modifiche da 1.x includono:

* `"storageProvider"`(e `"azureStorage"` la sottosezione) per la configurazione specifica dell'archiviazione.
* `"tracing"`per la configurazione di traccia e registrazione.
* `"notifications"`(e `"eventGrid"` la sottosezione) per la configurazione delle notifiche della griglia degli eventi.

Per informazioni dettagliate, vedere la documentazione di [riferimento di host.json di Durable Functions.json.](durable-functions-bindings.md#durable-functions-2-0-host-json)

#### <a name="default-taskhub-name-changes"></a>Modifiche al nome taskhub predefinito

Nella versione 1.x, se un nome hub attività non è stato specificato in host.json, è stato impostato per impostazione predefinita su "DurableFunctionsHub". Nella versione 2.x, il nome dell'hub attività predefinito è ora derivato dal nome dell'app per le funzioni. Per questo motivo, se non è stato specificato un nome dell'hub attività durante l'aggiornamento a 2.x, il codice funzionerà con il nuovo hub attività e tutte le orchestrazioni in corso non diranno più un'applicazione che le elabora. Per risolvere questo problema, è possibile impostare in modo esplicito il nome dell'hub attività per il valore predefinito v1.x di "DurableFunctionsHub", oppure è possibile seguire le nostre indicazioni di [distribuzione zero-downtime](durable-functions-zero-downtime-deployment.md) per informazioni dettagliate su come gestire le modifiche di interruzione per le orchestrazioni in corso.

#### <a name="public-interface-changes-net-only"></a>Modifiche all'interfaccia pubblica (solo .NET)Public interface changes (.NET only)

Nella versione 1.x, i vari oggetti di _contesto_ supportati da funzioni durevoli dispongono di classi di base astratte destinate all'utilizzo negli unit test. Come parte di Durable Functions 2.x, queste classi di base astratte vengono sostituite con interfacce.

La tabella seguente rappresenta le principali modifiche:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` o `IDurableClient` |
| `DurableOrchestrationContext` o `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` o `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Nel caso in cui una classe base astratta conteneva metodi virtuali, questi metodi virtuali sono stati sostituiti da metodi di estensione definiti in `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>le modifiche di function.json (JavaScript e script in C

In Funzioni durevoli 1.x, l'associazione client di orchestrazione utilizza un `type` oggetto di `orchestrationClient`. Viene invece `durableClient` utilizzato dalla versione 2.x.
