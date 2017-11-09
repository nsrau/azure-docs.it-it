---
title: Configurare un cluster autonomo di Azure Service Fabric | Microsoft Docs
description: "Creare un cluster di sviluppo autonomo con tre nodi in esecuzione nello stesso computer. Al termine della configurazione sarà possibile creare un cluster costituito da più macchine virtuali."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 5438d8d366ef989d5ae29581477513f8c884c4b3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Creare il primo cluster autonomo di Service Fabric
È possibile creare un cluster autonomo di Service Fabric su qualsiasi macchina virtuale o computer che esegua Windows Server 2012 R2 o Windows Server 2016, locale o nel cloud. Questa guida introduttiva consente di creare un cluster di sviluppo autonomo in pochi minuti.  Al termine si ottiene un cluster di tre nodi in esecuzione in un singolo computer nel quale è possibile distribuire app.

## <a name="before-you-begin"></a>Prima di iniziare
Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric autonomi.  [Scaricare il pacchetto di installazione](http://go.microsoft.com/fwlink/?LinkId=730690).  Decomprimere il pacchetto di installazione in una cartella nel computer o nella macchina virtuale in cui si configura il cluster di sviluppo.  Il contenuto del pacchetto di installazione è descritto nei dettagli [qui](service-fabric-cluster-standalone-package-contents.md).

L'amministratore del cluster che distribuisce e configura il cluster deve avere privilegi di amministratore nel computer. Non è possibile installare Service Fabric in un controller di dominio.

## <a name="validate-the-environment"></a>Convalidare l'ambiente
Lo script *TestConfiguration.ps1* nel pacchetto autonomo viene usato come Best Practices Analyzer per verificare se un cluster possa essere distribuito in un determinato ambiente. La [preparazione della distribuzione](service-fabric-cluster-standalone-deployment-preparation.md) elenca i prerequisiti e i requisiti dell'ambiente. Eseguire lo script per verificare se è possibile creare il cluster di sviluppo:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Creare il cluster
Con il pacchetto di installazione vengono installati diversi file di esempio per la configurazione del cluster. *ClusterConfig.Unsecure.DevCluster.json* è la configurazione del cluster più semplice: un cluster non sicuro, con tre nodi in esecuzione in un singolo computer.  Altri file di configurazione descrivono cluster con una o più macchine virtuali protetti con la sicurezza di Windows o certificati X.509.  Non è necessario modificare le impostazioni di configurazione predefinite per questa esercitazione, ma è consigliabile esaminare il file di configurazione per acquisire familiarità con le impostazioni.  La sezione **nodes** descrive i tre nodi nel cluster, indicando nome, indirizzo IP, [tipo di nodo, dominio di errore e dominio di aggiornamento](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  La sezione **properties** definisce le impostazioni per [sicurezza, livello di affidabilità, raccolta di dati di diagnostica e tipi di nodi ](service-fabric-cluster-manifest.md#cluster-properties) del cluster.

Questo cluster è senza protezione.  Chiunque si può connettere in modo anonimo ed eseguire operazioni di gestione. È quindi necessario proteggere sempre i cluster di produzione usando certificati X.509 o la sicurezza di Windows.  La sicurezza viene configurata solo in fase di creazione del cluster e non è possibile abilitare la sicurezza dopo la creazione del cluster.  Vedere [Proteggere un cluster](service-fabric-cluster-security.md) per altre informazioni sulla sicurezza dei cluster di Service Fabric.  

Per creare il cluster di sviluppo con tre nodi, eseguire lo script *CreateServiceFabricCluster.ps1* da una sessione amministratore di PowerShell:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Il pacchetto di runtime di Service Fabric viene scaricato e installato automaticamente al momento della creazione del cluster.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster
Il cluster di sviluppo di tre nodi è ora in esecuzione. Il modulo ServiceFabric di PowerShell viene installato con il runtime.  È possibile verificare che il cluster sia in esecuzione dallo stesso computer o da un computer remoto con il runtime di Service Fabric.  Il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) stabilisce una connessione al cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Per altri esempi di connessione a un cluster, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md). Dopo la connessione al cluster, usare il cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) per visualizzare un elenco dei nodi presenti nel cluster e informazioni di stato per ogni nodo. **HealthState** deve essere *OK* per ogni nodo.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualizzare il cluster con Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) rappresenta un ottimo strumento per la visualizzazione del cluster e la gestione delle applicazioni.  Service Fabric Explorer è un servizio in esecuzione nel cluster, cui si accede tramite un browser passando a [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

Il dashboard del cluster offre una panoramica del cluster, incluso un riepilogo dell'integrità delle applicazioni e dei nodi. La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile esaminare le applicazioni con il codice distribuito in quel nodo.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Rimuovere il cluster
Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON. Se necessario, è anche possibile specificare un percorso per il log del processo di eliminazione.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Per rimuovere il runtime di Service Fabric dal computer, eseguire lo script di PowerShell seguente dalla cartella del pacchetto.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato configurato un cluster di sviluppo autonomo, vedere gli articoli seguenti:
* [Configurare un cluster autonomo con più computer](service-fabric-cluster-creation-for-windows-server.md) e abilitare la sicurezza.
* [Distribuire le app tramite PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
