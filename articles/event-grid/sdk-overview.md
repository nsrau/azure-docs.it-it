---
title: SDK Griglia di eventi di Azure
description: Vengono descritti gli SDK per la Griglia di eventi di Azure. Questi SDK forniscono gestione, pubblicazione e consumo.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK Griglia di eventi per la gestione e la pubblicazione

La Griglia di eventi fornisce gli SDK che consentono di gestire a livello di codice le risorse e pubblicare eventi.

## <a name="management-sdks"></a>SDK di gestione

Gli SDK di gestione consentono di creare, aggiornare ed eliminare gli argomenti della griglia di eventi e le sottoscrizioni. Attualmente sono disponibili gli SDK seguenti:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>SDK di pubblicazione

Gli SDK di pubblicazione consentono di pubblicare eventi sugli argomenti occupandosi dell'autenticazione, formando l'evento e pubblicando in modo asincrono sull'endpoint specificato. Attualmente sono disponibili gli SDK seguenti:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md)
* Per i comandi Griglia di eventi nell'interfaccia della riga di comando di Azure di rete, vedere [Interfaccia della riga di comando di Azure](/cli/azure/eventgrid).
* Per i comandi Griglia di eventi in PowerShell, vedere [PowerShell](/powershell/module/azurerm.eventgrid).