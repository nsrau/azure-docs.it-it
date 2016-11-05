---
title: Configurare il cluster autonomo | Microsoft Docs
description: Questo articolo descrive come configurare un cluster di Service Fabric autonomo o privato.
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: dkshir

---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>Impostazioni di configurazione per un cluster autonomo in Windows
Questo articolo descrive come configurare un cluster di Service Fabric autonomo usando il file ***ClusterConfig.JSON***. Questo file viene scaricato sul computer in uso, nell'ambito del [download del pacchetto autonomo di Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). Il file ClusterConfig.JSON consente di specificare informazioni quali i nodi di Service Fabric e i relativi indirizzi IP, i vari tipi di nodi nel cluster, le configurazioni di sicurezza e la topologia di rete in termini di domini di errore/aggiornamento per il cluster di Service Fabric. 

Di seguito verranno esaminate le diverse sezioni di questo file.

## <a name="general-cluster-configurations"></a>Configurazioni generali del cluster
Questa sezione tratta in modo ampio le configurazioni specifiche dei cluster, come illustrato nel frammento di codice JSON seguente.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

È possibile attribuire qualsiasi nome descrittivo al cluster di Service Fabric assegnandolo alla variabile **name**. È possibile modificare **clusterManifestVersion** in base alla configurazione. Tale variabile deve essere aggiornata prima dell'aggiornamento della configurazione di Service Fabric. È possibile mantenere il valore predefinito per **apiVersion**.

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

Un cluster di Service Fabric richiede almeno 3 nodi. È possibile aggiungere più nodi a questa sezione in base alla configurazione. La tabella seguente illustra le impostazioni di configurazione per ogni nodo.

| **Configurazione nodo** | **Descrizione** |
| --- | --- |
| nodeName |È possibile assegnare qualsiasi nome descrittivo al nodo. |
| iPAddress |Trovare l'indirizzo IP del nodo aprendo una finestra di comando e digitando `ipconfig`. Prendere nota dell'indirizzo IPV4 e assegnarlo alla variabile **iPAddress** . |
| nodeTypeRef |È possibile assegnare un tipo diverso a ogni nodo. I [tipi di nodo](#nodetypes) sono definiti nella sezione seguente. |
| faultDomain |I domini di errore consentono agli amministratori del cluster di definire i nodi fisici su cui è probabile che si verifichino contemporaneamente degli errori a causa di dipendenze fisiche condivise. |
| upgradeDomain |I domini di aggiornamento descrivono set di nodi che vengono arrestati per gli aggiornamenti di Service Fabric quasi contemporaneamente. È possibile scegliere i nodi da assegnare a determinati domini di aggiornamento perché non sono limitati da eventuali requisiti fisici. |

## <a name="cluster-**properties**"></a>Variabile **properties**
La sezione **properties** in ClusterConfig.JSON viene usata per configurare il cluster come indicato di seguito.

### <a name="**diagnosticsstore**"></a>**diagnosticsStore**
È possibile configurare parametri per consentire la diagnostica e la risoluzione degli errori di nodi e cluster tramite la sezione **diagnosticsStore** , come illustrato nel frammento riportato di seguito. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** descrive la diagnostica del cluster e può essere impostata in base alla configurazione in uso. Queste variabili consentono di raccogliere log di traccia ETW, dump di arresto anomalo e contatori delle prestazioni. Per altre informazioni sui log di traccia ETW, leggere gli articoli [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Traccia ETW](https://msdn.microsoft.com/library/ms751538.aspx). Tutti i log che includono [dump di arresto anomalo del sistema](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) possono essere indirizzati alla cartella **connectionString** nel computer. È anche possibile usare **AzureStorage** per l'archiviazione della diagnostica. Per un frammento di esempio, vedere di seguito.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="**security**"></a>**security**
La sezione **security** è necessaria per la protezione di un cluster di Service Fabric autonomo. Il frammento seguente mostra una parte di questa sezione.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** descrive il cluster protetto e può essere impostata in base alla configurazione in uso. **ClusterCredentialType** e **ServerCredentialType** determinano il tipo di protezione che verrà implementata dal cluster e dai nodi. È possibile impostarle su *X509* per una protezione basata su certificato o su *Windows* per una protezione basata su Azure Active Directory. Il resto della sezione **security** sarà basato sul tipo di protezione. Leggere l'articolo [Proteggere il cluster Windows autonomo con i certificati](service-fabric-windows-cluster-x509-security.md) o [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md) per informazioni su come compilare il resto della sezione **security**.

### <a name="**reliabilitylevel**"></a>**reliabilityLevel**
**reliabilityLevel** definisce il numero di copie dei servizi di sistema eseguibili sui nodi primari del cluster. Ciò aumenta l'affidabilità di questi servizi e quindi del cluster. È possibile impostare questa variabile su *Bronze*, *Silver*, *Gold* o *Platinum* per 3, 5, 7 o 9 copie di questi servizi rispettivamente. Ecco un esempio.

    "reliabilityLevel": "Bronze",

Si noti che poiché un nodo primario esegue una sola copia dei servizi di sistema, è necessario un minimo di 3 nodi primari per livelli di affidabilità *Bronze*, 5 per *Silver*, 7 per *Gold* e 9 per *Platinum*.

<a id="nodetypes"></a>

### <a name="**nodetypes**"></a>**nodeTypes**
La sezione **nodeTypes** descrive il tipo dei nodi del cluster. Per ogni cluster deve essere specificato almeno un tipo di nodo, come illustrato nel frammento riportato di seguito. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

**name** è il nome descrittivo per questo tipo di nodo particolare. Per creare un nodo di questo tipo, è necessario assegnare il nome descrittivo per questo tipo di nodo alla variabile **nodeTypeRef** per tale nodo, come indicato nella sezione precedente [Nodi del cluster](#clusternodes) . Per ogni tipo di nodo, è possibile definire vari endpoint per la connessione al cluster. È possibile scegliere qualsiasi numero di porta per gli endpoint di connessione, purché non entrino in conflitto con altri endpoint in questo cluster. Se si vuole creare una porta del gateway applicazione HTTP, è possibile specificare "reverseProxyEndpointPort": [numero porta] in aggiunta alle altre porte riportate sopra. Per un cluster con più tipi di nodo, sarà presente un tipo di nodo primario, per il quale **isPrimary** sarà impostato su *true*. Il resto dei nodi avrà **isPrimary** impostato su *false*. Leggere l'articolo [Considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md) per maggiori informazioni sui valori **nodeTypes** e **reliabilityLevel** in base alla capacità del cluster e per conoscere la differenza tra i tipi di nodo primari e non primari.

### <a name="**fabricsettings**"></a>**fabricSettings**
Questa sezione consente di impostare le directory radice per i log e i dati di Service Fabric. È possibile personalizzarle solo durante la creazione del cluster iniziale. Per un frammento di esempio di questa sezione, vedere di seguito.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

Si consiglia di usare un'unità non del sistema operativo come FabricDataRoot e FabricLogRoot, poiché risulta più affidabile in caso di arresto anomalo del sistema operativo. Si noti che se si personalizza solo la radice dei dati, la radice del log verrà inserita un livello sotto la radice dei dati.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la configurazione di un file ClusterConfig.JSON in base alla configurazione del cluster autonomo, è possibile distribuire il cluster seguendo l'articolo [Creare un cluster di Azure Service Fabric locale o nel cloud](service-fabric-cluster-creation-for-windows-server.md) e quindi proseguire per [visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--HONumber=Oct16_HO2-->


