
<properties
   pageTitle="Protezione del Cluster di infrastruttura di servizi: Ruoli client | Microsoft Azure"
   description="In questo articolo vengono descritti i due ruoli client e le autorizzazioni fornite per i ruoli." 
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="subramar"/>



# Controllo di accesso basato sui ruoli (per i client di Infrastruttura di servizi)

Infrastruttura di servizi supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Infrastruttura di servizi: amministratore e utente. Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster. Gli amministratori hanno accesso completo alle funzionalità di gestione (incluse funzionalità di lettura/scrittura) e gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alla gestione (ad esempio query) e la possibilità di risolvere applicazioni e servizi.

I due ruoli di client (amministratore o client) vengono specificati al momento della creazione del cluster fornendo certificati separati per ognuno di essi. Vedere [Protezione del cluster di Infrastruttura di servizi](service-fabric-cluster-security.md) per informazioni dettagliate su come configurare un cluster di Infrastruttura di servizi protetto.


## Impostazioni predefiniti di controllo di accesso


Il tipo di controllo di accesso dell’amministratore ha accesso completo a tutte le API di FabricClient. Esso può eseguire qualsiasi operazione di lettura e scrittura con il cluster di Infrastruttura di servizi, incluse:

### Operazioni dell’applicazione e di servizio 
* **CreateService** : creazione del servizio 							
* **CreateServiceFromTemplate** : creazione del servizio da un modello 							
* **UpdateService** : aggiornamenti del servizio 							
* **DeleteService** : eliminazione del servizio 							
* **ProvisionApplicationType** : tipo applicazione del provisioning 							
* **CreateApplication** : creazione dell’applicazione 							
* **DeleteApplication** : eliminazione dell'applicazione 							
* **UpgradeApplication** : avvio o interruzione degli aggiornamenti dell'applicazione 							
* **UnprovisionApplicationType** : tipo applicazione di annullamento del provisioning 							
* **MoveNextUpgradeDomain** : ripresa degli aggiornamenti dell'applicazione con un dominio di aggiornamento esplicito 							
* **ReportUpgradeHealth** : ripresa degli aggiornamenti dell'applicazione con lo stato di avanzamento corrente 							
* **ReportHealth** : report d’integrità 							
* **PredeployPackageToNode** : api di pre-distribuzione 							
* **CodePackageControl** : riavvio dei pacchetti di codice 							
* **RecoverPartition** : ripristino di una partizione 							
* **RecoverPartitions** : ripristino di partizioni 							
* **RecoverServicePartitions** : ripristino di partizioni del servizio 							
* **RecoverSystemPartitions** : ripristino delle partizioni del servizio di sistema 							


### Operazioni del cluster
* **ProvisionFabric** : provisioning del manifesto del cluster e/o MSI 							
* **UpgradeFabric** : avvio degli aggiornamenti del cluster 							
* **UnprovisionFabric** : annullamento del provisioning del manifesto del cluster e/o MSI 							
* **MoveNextFabricUpgradeDomain** : ripresa degli aggiornamenti del cluster con un dominio di aggiornamento esplicito 							
* **ReportFabricUpgradeHealth** : ripresa degli aggiornamenti del cluster con lo stato di avanzamento corrente 							
* **StartInfrastructureTask** : avvio delle attività di infrastruttura 							
* **FinishInfrastructureTask** : completamento delle attività di infrastruttura 							
* **InvokeInfrastructureCommand** : comandi di gestione delle attività dell'infrastruttura 							
* **ActivateNode** : attivazione di un nodo 							
* **DeactivateNode** : disattivazione di un nodo 							
* **DeactivateNodesBatch** : disattivazione di più nodi 							
* **RemoveNodeDeactivations** : ripristino della disattivazione su più nodi 							
* **GetNodeDeactivationStatus** : controllo dello stato di disattivazione 							
* **NodeStateRemoved** : report dello stato del nodo rimosso 							
* **ReportFault** : errore di reporting 							
* **FileContent** : trasferimento del file del client dell’archivio immagini (esterno al cluster) 							
* **FileDownload** : inizio del download del file del client dell’archivio immagini (esterno al cluster) 							
* **InternalList** : operazione di elenco dei file del client dell’archivio immagini (interno) 							
* **Delete** : operazione di eliminazione del client dell’archivio immagini 							
* **Upload** : operazione di caricamento del client dell’archivio immagini 							
* **NodeControl** : avvio; arresto; e riavvio di nodi 							
* **MoveReplicaControl** : spostamento delle repliche da un nodo a un altro 							

### Operazioni varie
* **Ping** : ping del client 							
* **Query** : tutte le query consentite
* **NameExists** : verifica l’esistenza dell’URI di denominazione 							



Il tipo di controllo di accesso utente è per impostazione predefinita limitata alle operazioni seguenti (il controllo degli accessi dell’amministratore consente inoltre l'accesso a queste operazioni:

* **EnumerateSubnames** : enumerazione dell’URI di denominazione 							
* **EnumerateProperties** : enumerazione delle proprietà di denominazione 							
* **PropertyReadBatch** : operazioni di lettura delle proprietà di denominazione 							
* ****GetServiceDescription: notifiche del servizio di long polling e lettura delle descrizioni dei servizi
* **ResolveService** : risoluzione del servizio basata sui reclami 							
* **ResolveNameOwner** : risoluzione del proprietario dei nomi URI 							
* **ResolvePartition** : risoluzione dei servizi di sistema 							
* **ServiceNotifications** : notifiche di servizio basate su eventi 							
* **GetUpgradeStatus** : stato dell’aggiornamento dell’applicazione di polling 							
* **GetFabricUpgradeStatus** : stato dell’aggiornamento del cluster del polling 							
* **InvokeInfrastructureQuery** : attività dell’infrastruttura di esecuzione di query 							
* **List** : operazione di elenco dei file del client dell’archivio immagini 							
* **ResetPartitionLoad** : reimpostazione del carico per un failoverUnit 							
* ****ToggleVerboseServicePlacementHealthReporting: Creazione di report sull'integrità della selezione del servizio dettagliati

## Modifica delle impostazioni predefinite per i ruoli del client

Nel file manifesto del cluster, è possibile fornire funzionalità di amministrazione al client se necessario. È possibile modificare le impostazioni predefinite tramite l’opzione **Impostazioni infrastruttura* durante la [creazione di un cluster](service-fabric-cluster-creation-via-portal.md) e specificare le impostazioni denominate come descritto in precedenza nel campo **Nome** e **admin, user** nel campo del valore.

## Passaggi successivi

[Protezione del Cluster di Infrastruttura di servizi](service-fabric-cluster-security.md)

[Creazione del Cluster di Infrastruttura di servizi](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=Nov15_HO4-->