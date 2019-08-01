---
title: Risorse di Azure senza limite di 800 conteggio
description: Elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: a796896450a5b786e3b78aeddd81e6d66b02eb94
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700457"
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

* botServices: contattare il supporto tecnico per estendere il limite.

## <a name="microsoftcompute"></a>Microsoft.Compute

* dischi
* immagini
* snapshot
* virtualMachines

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

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* flussi di lavoro

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
* publicIPAddresses: contattare il supporto tecnico per estendere il limite.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections: contattare il supporto tecnico per estendere il limite.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* siti

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo di quote e limiti, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
