---
title: SDK Griglia di eventi di Azure
description: Vengono descritti gli SDK per la Griglia di eventi di Azure. Questi SDK forniscono gestione, pubblicazione e consumo.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 5e93448ef703be88583cd59ae5eee9d1993c4054
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322530"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK Griglia di eventi per la gestione e la pubblicazione

La Griglia di eventi fornisce gli SDK che consentono di gestire a livello di codice le risorse e pubblicare eventi.

## <a name="management-sdks"></a>SDK di gestione

Gli SDK di gestione consentono di creare, aggiornare ed eliminare gli argomenti della griglia di eventi e le sottoscrizioni. Attualmente sono disponibili gli SDK seguenti:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDK del piano dati

Gli SDK del piano dati consentono di pubblicare eventi sugli argomenti occupandosi dell'autenticazione, formando l'evento e pubblicando in modo asincrono sull'endpoint specificato. Consentono inoltre di utilizzare eventi proprietari. Attualmente sono disponibili gli SDK seguenti:

| Linguaggio di programmazione | SDK | 
| -------------------- | ---------- | ---------- | 
| .NET | SDK stabile: [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>SDK di anteprima: [Azure. Messaging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | SDK stabile: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>SDK di anteprima: [Azure-Messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (vedere le versioni più recenti stabili e non definitive nella pagina della **cronologia di rilascio** ) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) passare alla scheda **versioni** per visualizzare i pacchetti versione stabile e beta più recenti. | 
| Go | [Azure SDK per Go](https://github.com/Azure/azure-sdk-for-go) | | 
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) | | 


## <a name="next-steps"></a>Passaggi successivi

* Per le applicazioni di esempio, vedere gli [esempi di codice di Griglia di eventi](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md)
* Per i comandi Griglia di eventi nell'interfaccia della riga di comando di Azure di rete, vedere [Interfaccia della riga di comando di Azure](/cli/azure/eventgrid).
* Per i comandi Griglia di eventi in PowerShell, vedere [PowerShell](/powershell/module/az.eventgrid).
