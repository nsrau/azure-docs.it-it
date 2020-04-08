---
title: Risorse senza limite di conteggio 800
description: Elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8644bec1a68acebff18cf83d17acb014784dc964
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804776"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Risorse non limitate a 800 istanze per gruppo di risorseResources not limited to 800 instances per resource group

Per impostazione predefinita, è possibile distribuire fino a 800 istanze di un tipo di risorsa in ogni gruppo di risorse. Tuttavia, alcuni tipi di risorse sono esenti dal limite di 800 istanze. Questo articolo elenca i tipi di risorse di Azure che possono avere più di 800 istanze in un gruppo di risorse. Tutti gli altri tipi di risorse sono limitati a 800 istanze.

Per alcuni tipi di risorse, è necessario contattare il supporto per rimuovere il limite di 800 istanze. Tali tipi di risorse sono indicati in questo articolo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Per impostazione predefinita, limitato a 800 istanze. Tale limite può essere aumentato contattando il supporto tecnico.

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
* registri/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* gruppi di server
* servers
* serverv2 (serverv2)
* singleServer

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* pianificazioni - Per impostazione predefinita, limitate a 800 istanze. Tale limite può essere aumentato contattando il supporto tecnico.

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
* softwareUpdateProfile (profilato)
* softwareAggiornamenti

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* flussi di lavoro

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccount
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumi
* netAppAccounts/capacityPools/volumi/mountTargets
* netAppAccounts/capacityPools/volumi/istantanei

## <a name="microsoftnetwork"></a>Microsoft.Network

* ApplicationGatewayWebApplicationFirewallPolicies (ApplicazioneGatewayWebApplicationFirewallPolicies)
* applicationSecurityGroups
* bastionGli ospiti
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
* privateDns-
* privateDns-ones/A
* privateDns-ones/AAAA
* privateDns-ones/CNAME
* privateDns-ones/MX
* privateDns-ones/PTR
* privateDns-ones/SOA
* privateDns-ones/SRV
* privateDNS-ones/TXT
* privateDns-ones/all
* privateDns-ones/virtualNetworkLinks
* endpoint privati
* privateLinkServices
* publicIPAddresses - Per impostazione predefinita, limitato a 800 istanze.publicIPAddresses - By default, limited to 800 instances. Tale limite può essere aumentato contattando il supporto tecnico.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - Per impostazione predefinita, limitato a 800 istanze. Tale limite può essere aumentato contattando il supporto tecnico.

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

Per un elenco completo di quote e limiti, vedere [Limiti, quote e vincoli](azure-subscription-service-limits.md)di sottoscrizione e servizio di Azure.
