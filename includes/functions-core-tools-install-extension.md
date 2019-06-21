---
title: File di inclusione
description: File di inclusione
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179913"
---
Bundle di estensione per apportare tutte le associazioni pubblicate dal team di funzioni di Azure disponibile tramite un'impostazione nel *host. JSON* file. Per lo sviluppo locale, assicurarsi di aver la versione più recente di [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Per usare pacchetti di estensione, aggiornare il *host. JSON* file da includere la voce seguente per `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- Il `id` proprietà fa riferimento a spazio dei nomi per il bundle di estensione per funzioni di Microsoft Azure.
- Il `version` fa riferimento alla versione del bundle.

Riunire incremento delle versioni in pacchetti le modifiche di bundle. Modifiche di versione principale si verificano quando i pacchetti del bundle spostare solo una versione principale. Il `version` utilizzata dalla proprietà di [notazione di intervallo per la specifica di intervalli di versione](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Il runtime di funzioni sceglie sempre la versione massima consentita definita per l'intervallo e intervallo di versioni.

Quando si fa riferimento i bundle di estensione nel progetto, quindi tutte le associazioni predefinite sono disponibili per le funzioni. Le associazioni disponibili nel [bundle estensione](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) sono:

|Pacchetto  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|