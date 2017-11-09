---
title: Configurare un cluster autonomo di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare un cluster di Azure Service Fabric autonomo o locale.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: dc17ba7f8cc1326790b0256de277ccb2eaa20949
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Impostazioni di configurazione per un cluster autonomo in Windows
Questo articolo descrive come configurare un cluster autonomo di Azure Service Fabric usando il file ClusterConfig.JSON. È possibile usare questo file per specificare informazioni quali i nodi di Service Fabric, i relativi indirizzi IP e i diversi tipi di nodi nel cluster. È anche possibile specificare le configurazioni di sicurezza, nonché la topologia di rete in termini di domini di errore e aggiornamento per il cluster autonomo.

Quando si [scarica un pacchetto autonomo di Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alcuni campioni del file ClusterConfig.JSON vengono scaricati sul computer in uso. I campioni i cui nomi contengono DevCluster aiuteranno a creare un cluster con tutti e tre i nodi nello stesso computer, ad esempio nodi logici. Almeno uno di questi nodi deve essere contrassegnato come primario. Questo cluster è utile per un ambiente di sviluppo o test. Non è supportato come cluster di produzione. I campioni i cui nomi contengono MultiMachine aiuteranno a creare un cluster di qualità di produzione, in cui ogni nodo si trova in un computer separato. Il numero di nodi primari per i cluster si basa sul [livello di affidabilità](#reliability). Nella versione 05-2017 dell'API 5.7 la proprietà del livello di affidabilità è stata rimossa. Il codice calcola invece il livello di affidabilità ottimale per il cluster. Non usare questa proprietà nella versione del codice 5.7 e successive.


* ClusterConfig.Unsecure.DevCluster.JSON e ClusterConfig.Unsecure.MultiMachine.JSON mostrano rispettivamente come creare cluster senza protezione per test e produzione.

* ClusterConfig.Windows.DevCluster.JSON e ClusterConfig.Windows.MultiMachine.JSON mostrano come creare cluster di test o produzione protetti tramite [sicurezza di Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.JSON e ClusterConfig.X509.MultiMachine.JSON mostrano come creare cluster di test o produzione protetti tramite [sicurezza basata su certificato X509](service-fabric-windows-cluster-x509-security.md).

Verranno esaminate ora le diverse sezioni di un file ClusterConfig.JSON.

## <a name="general-cluster-configurations"></a>Configurazioni generali del cluster
Le Configurazioni generali del cluster comprendono le ampie configurazioni specifiche del cluster, come illustrato nel frammento JSON seguente:

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",

È possibile attribuire qualsiasi nome descrittivo al cluster di Service Fabric assegnandolo alla variabile name. Il clusterConfigurationVersion è il numero di versione del cluster. Aumentarlo ogni volta che si esegue l'aggiornamento del cluster di Service Fabric. Lasciare la proprietà apiVersion impostata sul valore predefinito.

    <a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nodi del cluster
È possibile configurare i nodi nel cluster di Service Fabric tramite la sezione nodes, come illustrato nel frammento seguente:

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster di Service Fabric deve contenere almeno 3 nodi. È possibile aggiungere più nodi a questa sezione in base alla configurazione. La tabella seguente illustra le impostazioni di configurazione per ogni nodo:

| **Configurazione nodo** | **Descrizione** |
| --- | --- |
| nodeName |È possibile assegnare qualsiasi nome descrittivo al nodo. |
| iPAddress |Trovare l'indirizzo IP del nodo aprendo una finestra di comando e digitando `ipconfig`. Prendere nota dell'indirizzo IPV4 e assegnarlo alla variabile iPAddress. |
| nodeTypeRef |È possibile assegnare un tipo diverso a ogni nodo. I [tipi di nodo](#node-types) sono definiti nella sezione seguente. |
| faultDomain |I domini di errore consentono agli amministratori del cluster di definire i nodi fisici su cui è probabile che si verifichino contemporaneamente degli errori a causa di dipendenze fisiche condivise. |
| upgradeDomain |I domini di aggiornamento descrivono set di nodi che vengono arrestati per gli aggiornamenti di Service Fabric quasi contemporaneamente. È possibile scegliere i nodi da assegnare a determinati domini di aggiornamento perché non sono limitati da eventuali requisiti fisici. |

## <a name="cluster-properties"></a>Proprietà del cluster
La sezione properties in ClusterConfig.JSON viene usata per configurare il cluster come indicato di seguito:

    <a id="reliability"></a>

### <a name="reliability"></a>Affidabilità
Il concetto di reliabilityLevel definisce il numero di repliche o istanze dei servizi di sistema di Service Fabric eseguibili nei nodi primari del cluster. Determina l'affidabilità di questi servizi e quindi del cluster. Il valore viene calcolato dal sistema in fase di creazione e di aggiornamento del cluster.

### <a name="diagnostics"></a>Diagnostica
La sezione diagnosticsStore consente di configurare i parametri per consentire la diagnostica e la risoluzione degli errori di nodi o cluster, come illustrato nel frammento riportato di seguito: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

La variabile metadata descrive la diagnostica del cluster e può essere impostata in base alla configurazione in uso. Queste variabili consentono di raccogliere log di traccia ETW, dump di arresto anomalo e contatori delle prestazioni. Per altre informazioni sui log di traccia ETW, leggere gli articoli [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Traccia ETW](https://msdn.microsoft.com/library/ms751538.aspx). Tutti i log che includono [dump di arresto anomalo](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) possono essere indirizzati alla cartella connectionString nel computer. È inoltre possibile usare AzureStorage per l'archiviazione di diagnostica. Vedere il frammento di esempio seguente:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sicurezza
La sezione security è necessaria per la protezione di un cluster di Service Fabric autonomo. Il frammento seguente mostra una parte di questa sezione:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

La variabile metadata descrive il cluster protetto e può essere impostata in base alla configurazione in uso. ClusterCredentialType e ServerCredentialType determinano il tipo di protezione implementata dal cluster e dai nodi. È possibile impostarle su *X509* per una protezione basata su certificato o su *Windows* per una protezione basata su Azure Active Directory. Il resto della sezione security sarà basato sul tipo di protezione. Leggere l'articolo sulla [sicurezza basata su certificati in un cluster autonomo](service-fabric-windows-cluster-x509-security.md) o sulla [sicurezza di Windows in un cluster autonomo](service-fabric-windows-cluster-windows-security.md) per informazioni su come compilare il resto della sezione security.

    <a id="nodetypes"></a>

### <a name="node-types"></a>Tipi di nodi
La sezione nodeTypes descrive il tipo di nodi del cluster. Per ogni cluster deve essere specificato almeno un tipo di nodo, come illustrato nel frammento riportato di seguito: 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

name è il nome descrittivo per questo tipo di nodo particolare. Per creare un nodo di questo tipo, assegnare il nome descrittivo alla variabile nodeTypeRef per tale nodo, come [indicato in precedenza](#nodes-on-the-cluster). Per ogni tipo di nodo, definire gli endpoint di connessione usati. È possibile scegliere qualsiasi numero di porta per gli endpoint di connessione, purché non entrino in conflitto con altri endpoint in questo cluster. In un cluster multinodo saranno presenti uno o più nodi primari (ad esempio isPrimary impostato su *true*), in base a [reliabilityLevel](#reliability). Per informazioni su nodeTypes e reliabilityLevel e per sapere quali sono i tipi di nodo primari e non primari, vedere [Considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md). 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Endpoint usati per configurare i tipi di nodi
* clientConnectionEndpointPort è la porta usata dal client per connettersi al cluster quando si usano le API del client. 
* clusterConnectionEndpointPort è la porta in cui i nodi comunicano tra loro.
* leaseDriverEndpointPort è la porta usata dal driver di lease del cluster per scoprire se i nodi sono ancora attivi. 
* serviceConnectionEndpointPort è la porta usata da applicazioni e servizi distribuiti in un nodo, per comunicare con il client di Service Fabric in quel determinato nodo.
* httpGatewayEndpointPort è la porta usata da Service Fabric Explorer per connettersi al cluster.
* Le porte ephemeralPorts eseguono l'override delle [porte dinamiche usate dal sistema operativo](https://support.microsoft.com/kb/929851). Service Fabric usa una parte di queste porte dell'applicazione, mentre le rimanenti sono a disposizione del sistema operativo. Inoltre esegue il mapping di questo intervallo nell'intervallo presente nel sistema operativo, quindi per tutti gli scopi è possibile usare gli intervalli forniti nei file JSON campione. È necessario assicurarsi che la differenza tra la porta iniziale e la porta finale sia almeno 255. Questo intervallo è condiviso con il sistema operativo e possono quindi verificarsi dei conflitti se la differenza è troppo bassa. Per visualizzare l'intervallo di porte dinamiche configurato, eseguire il comando `netsh int ipv4 show dynamicport tcp`.
* applicationPorts sono le porte che verranno usate dalle applicazioni di Service Fabric. L'intervallo di porte dell'applicazione deve essere abbastanza grande da soddisfare il requisito di endpoint delle applicazioni. Questo intervallo deve essere esclusivo dall'intervallo di porte dinamico del computer, ad esempio l'intervallo ephemeralPorts impostato nella configurazione. Service Fabric usa queste porte tutte le volte che servono nuove porte e si occupa di aprire i relativi firewall. 
* reverseProxyEndpointPort è un endpoint proxy inverso facoltativo. Per altre informazioni, vedere [Proxy inverso di Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Impostazioni log
La sezione fabricSettings consente di impostare le directory radice per i log e i dati di Service Fabric. È possibile personalizzarle solo durante la creazione del cluster iniziale. Vedere il frammento di esempio seguente di questa sezione:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

È consigliabile usare un'unità non del sistema operativo come FabricDataRoot e FabricLogRoot. Fornisce maggiore affidabilità nell'evitare situazioni in cui il sistema operativo non risponde. Si noti che se si personalizza solo la radice dei dati, la radice del log viene inserita un livello sotto la radice dei dati.

### <a name="stateful-reliable-services-settings"></a>Impostazioni dei Reliable Services con stato
La sezione KtlLogger consente di effettuare le impostazioni di configurazione globali per i Reliable Services. Per altre informazioni su queste impostazioni leggere [Configurazione di servizi Reliable Services con stato](service-fabric-reliable-services-configuration.md). L'esempio seguente illustra come modificare il log delle transazioni condiviso creato per eseguire il backup di eventuali raccolte affidabili per i servizi con stato:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Funzionalità aggiuntive
Per configurare le funzioni aggiuntive, l'impostazione di apiVersion deve essere 04-2017 o versione successiva ed è necessario configurare addonFeatures:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Supporto dei contenitori
Per abilitare il supporto dei contenitori sia per i contenitori di Windows Server che per i contenitori di Hyper-V per i cluster autonomi, deve essere attivata la funzionalità aggiuntiva DnsService.


## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un file ClusterConfig.JSON completo, configurato in base all'impostazione cluster autonomo, è possibile distribuire il cluster. Eseguire la procedura in [Creare un cluster autonomo di Service Fabric](service-fabric-cluster-creation-for-windows-server.md). Procedere quindi con [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e seguire i passaggi.

