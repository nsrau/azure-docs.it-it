---
title: Configurare il cluster autonomo | Documentazione Microsoft
description: Questo articolo descrive come configurare un cluster di Service Fabric autonomo o privato.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4fb6ef56d694aff967840ab26b75b66a2e799cc1
ms.openlocfilehash: 977de9160be63a91b5926daa45528e5ee205e448


---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>Impostazioni di configurazione per un cluster autonomo in Windows
Questo articolo descrive come configurare un cluster di Service Fabric autonomo usando il file ***ClusterConfig.JSON***. Questo file può essere usato per specificare informazioni quali i nodi di Service Fabric e i relativi indirizzi IP, i vari tipi di nodi nel cluster, le configurazioni di sicurezza e la topologia di rete in termini di domini di errore/aggiornamento per il cluster autonomo.

Quando si [scarica un pacchetto autonomo di Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alcuni campioni del file ClusterConfig.JSON vengono scaricati sul computer in uso. I campioni i cui nomi contengono *DevCluster* aiuteranno a creare un cluster con tutti e tre i nodi nello stesso computer, ad esempio nodi logici. Almeno uno di questi nodi deve essere contrassegnato come primario. Questo cluster è utile per un ambiente di sviluppo o test e non è supportato come cluster di produzione. I campioni i cui nomi contengono *MultiMachine* aiuteranno a creare un cluster di qualità di produzione, in cui ogni nodo si trova in un computer separato. Il numero di nodi primari per i cluster si baserà sul [livello di affidabilità](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* e *ClusterConfig.Unsecure.MultiMachine.JSON* mostrano rispettivamente come creare cluster senza protezione per test e produzione. 
2. *ClusterConfig.Windows.DevCluster.JSON* e *ClusterConfig.Windows.MultiMachine.JSON* mostrano come creare cluster di test o produzione protetti tramite [protezione di Windows](service-fabric-windows-cluster-windows-security.md).
3. *ClusterConfig.X509.DevCluster.JSON* e *ClusterConfig.X509.MultiMachine.JSON* mostrano come creare cluster di test o produzione protetti tramite [protezione basata su certificato X509](service-fabric-windows-cluster-x509-security.md). 

Verranno esaminate ora le diverse sezioni di un file ***ClusterConfig.JSON***, come indicato di seguito.

## <a name="general-cluster-configurations"></a>Configurazioni generali del cluster
Questa sezione tratta in modo ampio le configurazioni specifiche dei cluster, come illustrato nel frammento di codice JSON seguente.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

È possibile attribuire qualsiasi nome descrittivo al cluster di Service Fabric assegnandolo alla variabile **name**. **clusterConfigurationVersion** rappresenta il numero di versione del cluster; è possibile aggiornarlo ogni volta che si aggiorna il cluster di Service Fabric. Tuttavia, è necessario mantenere il valore predefinito per **apiVersion**.

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nodi del cluster
È possibile configurare i nodi nel cluster di Service Fabric tramite la sezione **nodes** , come illustrato nel frammento seguente.

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

Un cluster Service Fabric deve contenere almeno 3 nodi. È possibile aggiungere più nodi a questa sezione in base alla configurazione. La tabella seguente illustra le impostazioni di configurazione per ogni nodo.

| **Configurazione nodo** | **Descrizione** |
| --- | --- |
| nodeName |È possibile assegnare qualsiasi nome descrittivo al nodo. |
| iPAddress |Trovare l'indirizzo IP del nodo aprendo una finestra di comando e digitando `ipconfig`. Prendere nota dell'indirizzo IPV4 e assegnarlo alla variabile **iPAddress** . |
| nodeTypeRef |È possibile assegnare un tipo diverso a ogni nodo. I [tipi di nodo](#nodetypes) sono definiti nella sezione seguente. |
| faultDomain |I domini di errore consentono agli amministratori del cluster di definire i nodi fisici su cui è probabile che si verifichino contemporaneamente degli errori a causa di dipendenze fisiche condivise. |
| upgradeDomain |I domini di aggiornamento descrivono set di nodi che vengono arrestati per gli aggiornamenti di Service Fabric quasi contemporaneamente. È possibile scegliere i nodi da assegnare a determinati domini di aggiornamento perché non sono limitati da eventuali requisiti fisici. |

## <a name="cluster-properties"></a>Proprietà del cluster
La sezione **properties** in ClusterConfig.JSON viene usata per configurare il cluster come indicato di seguito.

<a id="reliability"></a>

### <a name="reliability"></a>Affidabilità
Nella sezione **reliabilityLevel** viene definito il numero di copie dei servizi di sistema eseguibili sui nodi primari del cluster. Ciò aumenta l'affidabilità di questi servizi e quindi del cluster. È possibile impostare questa variabile su *Bronze*, *Silver*, *Gold* o *Platinum* per 3, 5, 7 o 9 copie di questi servizi rispettivamente. Ecco un esempio.

    "reliabilityLevel": "Bronze",

Si noti che poiché un nodo primario esegue una sola copia dei servizi di sistema, è necessario un minimo di 3 nodi primari per livelli di affidabilità *Bronze*, 5 per *Silver*, 7 per *Gold* e 9 per *Platinum*.

### <a name="diagnostics"></a>Diagnostica
La sezione **diagnosticsStore** consente di configurare i parametri per consentire la diagnostica e la risoluzione degli errori di nodi o cluster, come illustrato nel frammento riportato di seguito. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** descrive la diagnostica del cluster e può essere impostata in base alla configurazione in uso. Queste variabili consentono di raccogliere log di traccia ETW, dump di arresto anomalo e contatori delle prestazioni. Per altre informazioni sui log di traccia ETW, leggere gli articoli [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Traccia ETW](https://msdn.microsoft.com/library/ms751538.aspx). Tutti i log che includono [dump di arresto anomalo del sistema](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) possono essere indirizzati alla cartella **connectionString** nel computer. È anche possibile usare *AzureStorage* per l'archiviazione della diagnostica. Per un frammento di esempio, vedere di seguito.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sicurezza
La sezione **security** è necessaria per la protezione di un cluster di Service Fabric autonomo. Il frammento seguente mostra una parte di questa sezione.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** descrive il cluster protetto e può essere impostata in base alla configurazione in uso. **ClusterCredentialType** e **ServerCredentialType** determinano il tipo di protezione che verrà implementata dal cluster e dai nodi. È possibile impostarle su *X509* per una protezione basata su certificato o su *Windows* per una protezione basata su Azure Active Directory. Il resto della sezione **security** sarà basato sul tipo di protezione. Leggere l'articolo [Proteggere il cluster Windows autonomo con i certificati](service-fabric-windows-cluster-x509-security.md) o [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md) per informazioni su come compilare il resto della sezione **security**.

<a id="nodetypes"></a>

### <a name="node-types"></a>Tipi di nodi
La sezione **nodeTypes** descrive il tipo dei nodi del cluster. Per ogni cluster deve essere specificato almeno un tipo di nodo, come illustrato nel frammento riportato di seguito. 

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

**name** è il nome descrittivo per questo tipo di nodo particolare. Per creare un nodo di questo tipo, assegnare il nome descrittivo alla variabile **nodeTypeRef** per tale nodo, come indicato [sopra](#clusternodes). Per ogni tipo di nodo, definire gli endpoint di connessione che verranno usati. È possibile scegliere qualsiasi numero di porta per gli endpoint di connessione, purché non entrino in conflitto con altri endpoint in questo cluster. In un cluster multinodo saranno presenti uno o più nodi primari (ad esempio **isPrimary** impostato su *true*), in base a [**reliabilityLevel**](#reliability). Leggere l'articolo [Considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md) per informazioni sui valori **nodeTypes** e **reliabilityLevel** e per conoscere la differenza tra i tipi di nodo primari e non primari. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Endpoint usati per configurare i tipi di nodi
* *clientConnectionEndpointPort* è la porta usata dal client per connettersi al cluster quando si usano le API del client. 
* *clusterConnectionEndpointPort* è la porta in cui i nodi di comunicano tra loro.
* *leaseDriverEndpointPort* è la porta usata dal driver di lease del cluster per scoprire se i nodi sono ancora attivi. 
* *serviceConnectionEndpointPort* è la porta usata da applicazioni e servizi distribuiti in un nodo, per comunicare con il client di Service Fabric in quel determinato nodo.
* *httpGatewayEndpointPort* è la porta usata da Service Fabric Explorer per connettersi al cluster.
* Le porte *ephemeralPorts* eseguono l'override delle [porte dinamiche usate dal sistema operativo](https://support.microsoft.com/kb/929851). Service Fabric userà una parte di queste porte dell'applicazione, mentre le rimanenti saranno a disposizione del sistema operativo. Inoltre eseguirà il mapping di questo intervallo nell'intervallo presente nel sistema operativo, quindi per tutti gli scopi è possibile usare gli intervalli forniti nei file JSON campione. È necessario assicurarsi che la differenza tra la porta iniziale e la porta finale sia almeno 255. Questo intervallo è condiviso con il sistema operativo e possono quindi verificarsi dei conflitti se la differenza è troppo bassa. Per visualizzare l'intervallo di porte dinamiche configurato, eseguire il comando `netsh int ipv4 show dynamicport tcp`.
* *applicationPorts* sono le porte che verranno usate dalle applicazioni di Service Fabric. Deve trattarsi di un subset di *ephemeralPorts*, sufficiente per soddisfare il requisito di endpoint delle applicazioni. Service Fabric le userà tutte le volte che servono nuove porte, inoltre si occuperà di aprire i relativi firewall. 
* *reverseProxyEndpointPort* è un endpoint proxy inverso facoltativo. Per altri dettagli vedere [Proxy inverso di Service Fabric](service-fabric-reverseproxy.md). 

### <a name="other-settings"></a>Altre impostazioni
La sezione **fabricSettings** consente di impostare le directory radice per i log e i dati di Service Fabric. È possibile personalizzarle solo durante la creazione del cluster iniziale. Per un frammento di esempio di questa sezione, vedere di seguito.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Si consiglia di usare un'unità non del sistema operativo come FabricDataRoot e FabricLogRoot, poiché risulta più affidabile in caso di arresto anomalo del sistema operativo. Si noti che se si personalizza solo la radice dei dati, la radice del log verrà inserita un livello sotto la radice dei dati.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la configurazione di un file ClusterConfig.JSON in base alla configurazione del cluster autonomo, è possibile distribuire il cluster seguendo l'articolo [Creare un cluster di Service Fabric autonomo](service-fabric-cluster-creation-for-windows-server.md) e quindi proseguire per la [Visualizzazione del cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).




<!--HONumber=Dec16_HO2-->


