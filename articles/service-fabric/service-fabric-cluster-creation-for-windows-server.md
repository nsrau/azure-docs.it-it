---
title: Creare un cluster autonomo di Azure Service Fabric | Documentazione Microsoft
description: Creare un cluster di Azure Service Fabric su qualsiasi macchina (fisica o virtuale) che esegue Windows Server, sia locale che nel cloud.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/24/2017
ms.author: chackdan;maburlik
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 3389684dca62a1b3c8297600c7c09ffef642c854
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Creare un cluster autonomo in esecuzione su Windows Server
Azure Service Fabric consente di creare cluster Service Fabric su qualsiasi macchina virtuale o computer che esegue Windows Server. In questo modo è possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente che contenga un set di computer Windows Server interconnessi, in locale o con qualsiasi provider di cloud. Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric, denominato pacchetto autonomo per Windows Server.

Questo articolo illustra la procedura di creazione di un cluster autonomo di Service Fabric.

> [!NOTE]
> Questo pacchetto autonomo di Windows Server è disponibile in commercio e può essere usato per distribuzioni di produzione. Il pacchetto può contenere nuove funzionalità di Service Fabric in "Anteprima". Scorrere verso il basso fino alla sezione "[Funzionalità di anteprima incluse in questo pacchetto](#previewfeatures_anchor)". per visualizzare l'elenco delle funzionalità in anteprima. È possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) ora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>Ottenere supporto per il pacchetto autonomo Service Fabric
* Porre domande alla community sul pacchetto autonomo Service Fabric per Windows Server nel [forum di Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Aprire un ticket per ottenere il [supporto professionale per Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Altre informazioni sul supporto professionale Microsoft sono disponibili [qui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* È possibile anche ottenere supporto per questo pacchetto come parte del [Supporto tecnico Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Per altre informazioni vedere [Opzioni di supporto di Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).
* Per raccogliere i log a scopo di supporto, eseguire l'[agente di raccolta log autonomo di Service Fabric](https://go.microsoft.com/fwlink/?linkid=842487).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>Download del pacchetto autonomo Service Fabric
Per creare il cluster, usare il pacchetto autonomo di Service Fabric per Windows Server (2012 R2 e versioni successive) disponibile qui: <br>
[Collegamento per il download - Pacchetto autonomo di Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Informazioni dettagliate sul contenuto del pacchetto sono disponibili [qui](service-fabric-cluster-standalone-package-contents.md).

Il pacchetto di runtime di Service Fabric viene scaricato automaticamente al momento della creazione del cluster. Se si esegue la distribuzione da un computer non connesso a Internet, scaricare il pacchetto di runtime fuori banda da qui: <br>
[Collegamento per il download - Runtime di Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Trovare esempi di configurazione di cluster autonomi in: <br>
[Esempi di configurazione di cluster autonomi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Creare il cluster
Service Fabric può essere distribuito in un cluster di sviluppo macchine usando il file *ClusterConfig.Unsecure.DevCluster.json* file incluso negli [esempi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Decomprimere il pacchetto autonomo nella macchina, copiare il file di configurazione di esempio nella macchina locale, quindi eseguire lo script *CreateServiceFabricCluster.ps1* tramite una sessione di PowerShell come amministratore, dalla cartella del pacchetto autonomo:
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Passaggio 1A: creare un cluster di sviluppo locale non protetto
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Vedere la sezione di configurazione dell'ambiente in [Pianificare e preparare la distribuzione del cluster](service-fabric-cluster-standalone-deployment-preparation.md) per i dettagli sulla risoluzione.

Al termine dell'esecuzione degli scenari di sviluppo è possibile rimuovere il cluster Service Fabric dalla macchina facendo riferimento alla procedura descritta nella sezione seguente "[Rimuovere un cluster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Passaggio 1B: creare un cluster con più macchine
Dopo avere eseguito i passaggi di pianificazione e preparazione illustrati nel collegamento seguente, si è pronti per creare il cluster di produzione usando il file di configurazione del cluster. <br>
[Pianificare e preparare la distribuzione del cluster](service-fabric-cluster-standalone-deployment-preparation.md)

1. Convalidare il file di configurazione scritto eseguendo lo script *TestConfiguration.ps1* nella cartella del pacchetto autonomo:  

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
```

Verrà visualizzato un output simile al seguente: Se nel campo in basso "Passed" viene restituito un valore "True", sono stati superati i controlli di integrità ed è possibile distribuire il cluster in base alla configurazione di input.

```
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

2. Creare il cluster: eseguire lo script *CreateServiceFabricCluster.ps1* per la distribuzione del cluster Service Fabric in ogni computer nella configurazione. 
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
```

> [!NOTE]
> Le tracce di distribuzione vengono scritte nella VM o nel computer in cui è stato eseguito lo script PowerShell CreateServiceFabricCluster.ps1. Sono disponibili nella sottocartella DeploymentTraces all'interno della directory da cui è stato eseguito lo script. Per verificare se Service Fabric è stato distribuito correttamente in un computer, individuare i file installati nella directory FabricDataRoot, come descritto in dettaglio nella sezione FabricSetting del file di configurazione del cluster. Per impostazione predefinita la directory è c:\ProgramData\SF. I processi FabricHost.exe e Fabric.exe possono essere visualizzati in esecuzione in Gestione attività.
> 
> 

### <a name="step-2-connect-to-the-cluster"></a>Passaggio 2: connettersi al cluster
Per connettersi a un cluster protetto, vedere la sezione relativa alla [connessione di Service Fabric a un cluster protetto](service-fabric-connect-to-secure-cluster.md).

Per connettersi a un cluster non protetto, eseguire il seguente comando di PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Passaggio 3: Visualizzare Service Fabric Explorer
È ora possibile connettersi al cluster con Service Fabric Explorer direttamente da uno dei computer con http://localhost:19080/Explorer/index.html o in modalità remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Aggiungere e rimuovere ruoli
È possibile aggiungere o rimuovere nodi nel cluster di Service Fabric autonomo al variare delle esigenze aziendali. Per i passaggi dettagliati, leggere le informazioni su [aggiunta e rimozione di nodi in un cluster Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Rimuovere un cluster
Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON. Se necessario, è anche possibile specificare un percorso per il log del processo di eliminazione.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dati di telemetria raccolti e come rifiutarli esplicitamente
Per impostazione predefinita, il prodotto raccoglie i dati di telemetria sull'utilizzo di Service Fabric per migliorarlo. Best Practice Analyzer, che viene eseguito durante la configurazione, controlla la connettività a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se non è raggiungibile, la configurazione non riesce, a meno che non si rifiutino esplicitamente i dati di telemetria.

1. La pipeline di telemetria cerca di caricare i dati seguenti in [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una volta al giorno. Si tratta di un tentativo di caricamento che non influisce sulla funzionalità del cluster. I dati di telemetria vengono inviati solo dal nodo che esegue la gestione failover primaria. Nessun altro nodo invia dati di telemetria.
2. La telemetria è costituita dagli elementi seguenti:

* Numero di servizi
* Numero di elementi ServiceTypes
* Numero di elementi Applications
* Numero di elementi ApplicationUpgrades
* Numero di elementi FailoverUnits
* Numero di elementi InBuildFailoverUnits
* Numero di elementi UnhealthyFailoverUnits
* Numero di elementi Replicas
* Numero di elementi InBuildReplicas
* Numero di elementi StandByReplicas
* Numero di elementi OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Numero di elementi Nodes
* IsContextComplete: True/False
* ClusterId: GUID generato casualmente per ogni cluster
* ServiceFabricVersion
* Indirizzo IP della macchina virtuale o del computer da cui vengono caricati i dati di telemetria

Per disabilitare la telemetria, aggiungere quanto segue all'elemento *properties* nel file di configurazione del cluster *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funzionalità di anteprima incluse in questo pacchetto
Nessuna.


> [!NOTE]
> A partire dalla nuova [versione GA del cluster autonomo per Windows Server (versione 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/) è possibile aggiornare il cluster per le versioni future, manualmente o automaticamente. Per informazioni dettagliate, vedere [Aggiornare il cluster autonomo di Service Fabric in Windows Server](service-fabric-cluster-upgrade-windows-server.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire e rimuovere applicazioni con PowerShell](service-fabric-deploy-remove-applications.md)
* [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
* [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Aggiornare il cluster autonomo di Service Fabric in Windows Server](service-fabric-cluster-upgrade-windows-server.md)
* [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

