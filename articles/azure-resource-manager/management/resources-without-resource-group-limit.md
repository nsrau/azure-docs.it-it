---
title: Risorse senza limite di 800 conteggi
description: Elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse.
ms.topic: conceptual
author: davidsmatlak
ms.author: v-dasmat
ms.date: 05/04/2020
ms.openlocfilehash: 892b59b3d3e980abfcdb9cd692c2598ceb1284ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82780930"
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

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices: per impostazione predefinita, limitato a 800 istanze. Il limite può essere aumentato contattando il supporto tecnico.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
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

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2
* singleServers

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* pianificazioni: per impostazione predefinita, limitate a 800 istanze. Il limite può essere aumentato contattando il supporto tecnico.

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* spazi dei nomi

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

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

* scala Web
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

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo di quote e limiti, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).
