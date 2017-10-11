---
title: 'Sicurezza di un cluster di Service Fabric: ruoli client | Microsoft Docs'
description: In questo articolo vengono descritti i due ruoli client e le autorizzazioni fornite per i ruoli.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: 
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 85935e60bba4b27972282700e2e9c9a22b403bdb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Controllo di accesso basato sui ruoli per i client di Service Fabric
Azure Service Fabric supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Service Fabric: amministratore e utente. Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster.  

**amministratori** hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli **utenti** , per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

I due ruoli di client, amministratore o client, vengono specificati al momento della creazione del cluster fornendo certificati separati per ognuno di essi. Per informazioni dettagliate su come configurare un cluster di Service Fabric protetto, vedere l'articolo relativo alla [sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md) .

## <a name="default-access-control-settings"></a>Impostazioni predefiniti di controllo di accesso
Il tipo di controllo di accesso dell’amministratore ha accesso completo a tutte le API di FabricClient. Può eseguire qualsiasi operazione di lettura e scrittura con il cluster di Service Fabric, incluse:

### <a name="application-and-service-operations"></a>Operazioni dell’applicazione e di servizio
* **CreateService**: creazione del servizio                             
* **CreateServiceFromTemplate**: creazione del servizio da un modello                             
* **UpdateService**: aggiornamenti del servizio                             
* **DeleteService**: eliminazione del servizio                             
* **ProvisionApplicationType**: provisioning del tipo applicazione                             
* **CreateApplication**: creazione dell'applicazione                               
* **DeleteApplication**: eliminazione dell'applicazione                             
* **UpgradeApplication**: avvio o interruzione degli aggiornamenti dell'applicazione                             
* **UnprovisionApplicationType**: annullamento del provisioning del tipo applicazione                             
* **MoveNextUpgradeDomain**: ripresa degli aggiornamenti dell'applicazione con un dominio di aggiornamento esplicito                             
* **ReportUpgradeHealth**: ripresa degli aggiornamenti dell'applicazione con lo stato di avanzamento corrente                             
* **ReportHealth**: report d'integrità                             
* **PredeployPackageToNode**: API di pre-distribuzione                            
* **CodePackageControl**: riavvio dei pacchetti di codice                             
* **RecoverPartition**: ripristino di una partizione                             
* **RecoverPartitions**: ripristino di partizioni                             
* **RecoverServicePartitions**: ripristino di partizioni del servizio                             
* **RecoverSystemPartitions**: ripristino di partizioni del servizio di sistema                             

### <a name="cluster-operations"></a>Operazioni del cluster
* **ProvisionFabric**: provisioning del manifesto del cluster e/o del file con estensione msi                             
* **UpgradeFabric**: avvio degli aggiornamenti del cluster                             
* **UnprovisionFabric**: annullamento del provisioning del manifesto del cluster e/o del file con estensione msi                         
* **MoveNextFabricUpgradeDomain**: ripresa degli aggiornamenti del cluster con un dominio di aggiornamento esplicito                             
* **ReportFabricUpgradeHealth**: ripresa degli aggiornamenti del cluster con lo stato di avanzamento corrente                             
* **StartInfrastructureTask**: avvio delle attività di infrastruttura                             
* **FinishInfrastructureTask**: completamento delle attività di infrastruttura                             
* **InvokeInfrastructureCommand**: comandi di gestione delle attività di infrastruttura                              
* **ActivateNode**: attivazione di un nodo                             
* **DeactivateNode**: disattivazione di un nodo                             
* **DeactivateNodesBatch**: disattivazione di più nodi                             
* **RemoveNodeDeactivations**: ripristino della disattivazione in più nodi                             
* **GetNodeDeactivationStatus**: controllo dello stato di disattivazione                             
* **NodeStateRemoved**: report dello stato del nodo rimosso                             
* **ReportFault**: report dell'errore                             
* **FileContent**: trasferimento del file del client dell'archivio immagini (esterno al cluster)                             
* **FileDownload**: inizio del download del file del client dell'archivio immagini (esterno al cluster)                             
* **InternalList**: operazione di elenco dei file del client dell'archivio immagini (interno)                             
* **Delete**: operazione di eliminazione del client dell'archivio immagini                              
* **Upload**: operazione di caricamento del client dell'archivio immagini                             
* **NodeControl**: avvio, arresto e riavvio di nodi                             
* **MoveReplicaControl**: spostamento delle repliche da un nodo a un altro                             

### <a name="miscellaneous-operations"></a>Operazioni varie
* **Ping**: ping del client                             
* **Query**: tutte le query consentite
* **NameExists**: verifiche dell'esistenza dell'URI di denominazione                             

Il tipo di controllo di accesso utente è limitato per impostazione predefinita alle operazioni seguenti. 

* **EnumerateSubnames**: enumerazione dell'URI di denominazione                             
* **EnumerateProperties**: enumerazione delle proprietà di denominazione                             
* **PropertyReadBatch**: operazioni di lettura delle proprietà di denominazione                             
* **GetServiceDescription**: notifiche del servizio di long polling e lettura delle descrizioni dei servizi                             
* **ResolveService**: risoluzione del servizio basata sui reclami                             
* **ResolveNameOwner**: risoluzione del proprietario dei nomi URI                             
* **ResolvePartition**: risoluzione dei servizi di sistema                             
* **ServiceNotifications**: notifiche di servizio basate su eventi                             
* **GetUpgradeStatus**: stato dell'aggiornamento dell'applicazione di polling                             
* **GetFabricUpgradeStatus**: stato dell'aggiornamento del cluster del polling                             
* **InvokeInfrastructureQuery**: attività dell'infrastruttura di esecuzione di query                             
* **List**: operazione di elenco dei file del client dell'archivio immagini                             
* **ResetPartitionLoad**: reimpostazione del carico per un'unità di failover                             
* **ToggleVerboseServicePlacementHealthReporting**: creazione di report sull'integrità del posizionamento dettagliato del servizio                             

Il controllo di accesso amministratore ha accesso anche alle operazioni precedenti.

## <a name="changing-default-settings-for-client-roles"></a>Modifica delle impostazioni predefinite per i ruoli del client
Nel file manifesto del cluster è possibile assegnare al client funzionalità di amministratore, se necessario. È possibile modificare le impostazioni predefinite accedendo all'opzione **Impostazioni infrastruttura** durante la [creazione del cluster](service-fabric-cluster-creation-via-portal.md) e fornendo le impostazioni precedenti nei campi relativi a **nome**, **amministratore**, **utente** e **valore**.

## <a name="next-steps"></a>Passaggi successivi
[Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)

[Creazione del cluster di Service Fabric](service-fabric-cluster-creation-via-portal.md)

