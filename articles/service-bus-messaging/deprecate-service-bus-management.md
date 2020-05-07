---
title: Servizi di messaggistica di Azure-Service Manager Gestione risorse
description: Questo articolo fornisce il mapping dell'API REST di Azure Service Manager deprecata & cmdlet di PowerShell per Gestione risorse API REST & cmdlet di PowerShell.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: 71e64f4be44d835ad4eed0bb74177e55aef3a35a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792259"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Deprecazione del supporto di Azure Service Manager per il bus di servizio, l'inoltro e hub eventi di Azure

Gestione risorse, lo stack dell'infrastruttura cloud di nuova generazione, sostituisce completamente il modello di gestione dei servizi di Azure classico (modello di distribuzione classica). Di conseguenza, le API REST del modello di distribuzione classica e il supporto per Service Bus, Relay e hub eventi verranno ritirati il 1 ° novembre 2021. Questa deprecazione è stata annunciata per la prima volta in un [annuncio Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), ma recentemente abbiamo deciso di estendere il periodo di deprecazione altri due anni dal momento dell'annuncio originale. Per facilitarne l'identificazione, le `management.core.windows.net` API hanno un URI. Vedere la tabella seguente per un elenco delle API deprecate e la relativa versione API Azure Resource Manager che è ora necessario usare.

Per continuare a usare Service Bus, Relay e hub eventi, passare a Gestione risorse entro il 31 ottobre 2021. Invitiamo tutti i clienti che usano ancora le API obsolete per sfruttare al meglio i vantaggi offerti da Gestione risorse, tra cui il raggruppamento di risorse, i tag, un processo di distribuzione e gestione semplificato e un controllo di accesso con granularità fine tramite il controllo degli accessi in base al ruolo (RBAC).

Per ulteriori informazioni su Azure Resource Manager Service Manager di Azure, vedere il [blog TechNet](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Per altre informazioni su Service Manager e Gestione risorse API per il bus di servizio di Azure, l'inoltro e hub eventi, vedere la documentazione dell'API REST:

- [Bus di servizio di Azure](/rest/api/servicebus/)
- [Hub eventi di Azure](/rest/api/eventhub/)
- [Servizio di inoltro di Azure](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>API REST di Service Manager-Gestione risorse API REST

| API Service Manager (deprecato) | API del bus di servizio Gestione risorse | API dell'hub eventi Gestione risorse | API di inoltro Gestione risorse |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Spazi dei nomi-GetNamespaceAsync** <br/>[Spazio dei nomi Get del bus di servizio](/rest/api/servicebus/get-namespace)<br/>[Spazio dei nomi Get di hub eventi](/rest/api/eventhub/get-event-hub)<br/>[Spazio dei nomi Get di inoltro](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Bus di servizio/Hub eventi/GetConnectionDetals di inoltro<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | listkeys | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Argomenti-GetTopicsAsync**<br/>Bus di servizio<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Code-GetQueueAsync** <br/>Bus di servizio<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Inoltri-GetRelaysAsync**<br/>[Ottenere gli inoltri](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Bus di servizio/Hub eventi/GetNamespaceAuthRule di inoltro<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |getauthorizationrule | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Spazi dei nomi-DeleteNamespaceAsync**<br/>[Spazio dei nomi Delete del bus di servizio](/rest/api/servicebus/delete-namespace)<br/>[Spazio dei nomi eliminazione di hub eventi](/rest/api/eventhub/delete-event-hub)<br/>[Inoltro Elimina spazio dei nomi](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | eliminare | [delete](/rest/api/relay/namespaces/delete) | 
| **Piano dello SKU-GetPlanAsync**<br/>Bus di servizio/Hub eventi/spazio dei nomi Get di inoltro<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **Piano dello SKU-UpdatePlanAsync**<br/>Bus di servizio/Hub eventi/spazio dei nomi Get di inoltro<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [CreateOrUpdate](/rest/api/servicebus/namespaces/createorupdate) | CreateOrUpdate | [CreateOrUpdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Bus di servizio/Hub eventi/spazio dei nomi Get di inoltro<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [CreateOrUpdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdateauthorizationrule | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Bus di servizio/Hub eventi/inoltro<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[CreateOrUpdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdateauthorizationrule | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Spazio dei nomi Get del bus di servizio](/rest/api/servicebus/get-namespace)<br/>[Spazio dei nomi Get di hub eventi](/rest/api/eventhub/get-event-hub)<br/>[Spazio dei nomi Get di inoltro](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Spazio dei nomi Get del bus di servizio/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | listbysku | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Bus di servizio/EventHub/inoltro<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [CreateOrUpdate](/rest/api/servicebus/namespaces/createorupdate) | CreateOrUpdate | [CreateOrUpdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Elencare gli hub eventi](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Ottenere Hub eventi](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Bus di servizio/Hub eventi/inoltro<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | deleteauthorizationrule | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Bus di servizio/EventHub/inoltro<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | listauthorizationrules | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[Disponibilità dello spazio dei nomi del bus di servizio](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | checknameavailability | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Spazi dei nomi-CreateOrUpdateNamespaceAsync**<br/>Bus di servizio/Hub eventi/inoltro<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [CreateOrUpdate](/rest/api/servicebus/namespaces/createorupdate) | CreateOrUpdate | [CreateOrUpdate](/rest/api/relay/namespaces/createorupdate) | 
| **Argomenti-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell-Gestione risorse PowerShell
| Comando Service Manager PowerShell (deprecato) | Nuovi comandi di Gestione risorse | Comando Gestione risorse più recente |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Remove-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione seguente: 

- Documentazione dell'API REST più recente
    - [Bus di servizio di Azure](/rest/api/servicebus/)
    - [Hub eventi di Azure](/rest/api/eventhub/)
    - [Servizio di inoltro di Azure](/rest/api/relay/)
- Documentazione di PowerShell più recente
    - [Bus di servizio di Azure](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Hub eventi di Azure](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Griglia di eventi di Azure](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
