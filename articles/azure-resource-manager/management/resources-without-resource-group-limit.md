---
title: Risorse senza limite di 800 conteggi
description: Elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 42e8ddeebcd1dda4fc67e4e7be137c5e01decdf4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715618"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Risorse non limitate a 800 di istanze per gruppo di risorse

Per impostazione predefinita, è possibile distribuire fino a 800 istanze di un tipo di risorsa in ogni gruppo di risorse. Tuttavia, alcuni tipi di risorse sono esenti dal limite dell'istanza 800. Questo articolo elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse. Tutti gli altri tipi di risorse sono limitati a 800 istanze.

Per alcuni tipi di risorse, è necessario contattare il supporto tecnico per rimuovere il limite dell'istanza 800. Questi tipi di risorse sono indicati in questo articolo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products
* verificationKeys

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices: per impostazione predefinita, limitato a 800 istanze. Il limite può essere aumentato contattando il supporto tecnico.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registri/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registri/buildTasks/Steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* server

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* server

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* server
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* spazi dei nomi

## <a name="microsoftexperimentation"></a>Microsoft. sperimentazione

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* flussi di lavoro

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumi
* netAppAccounts/capacityPools/Volumes/mountTargets
* netAppAccounts/capacityPools/volumi/snapshot

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/tutti
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses: per impostazione predefinita, limitato a 800 istanze. Il limite può essere aumentato contattando il supporto tecnico.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections: per impostazione predefinita, limitato a 800 istanze. Il limite può essere aumentato contattando il supporto tecnico.

## <a name="microsoftrelay"></a>Microsoft.Relay

* spazi dei nomi

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* spazi dei nomi

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* applicazioni
* containerGroups
* gateways
* networks
* chiavi private
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* siti

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo di quote e limiti, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).
