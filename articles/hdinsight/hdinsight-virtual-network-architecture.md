---
title: Architettura della rete virtuale di Azure HDInsight
description: Informazioni sulle risorse disponibili quando si crea un cluster HDInsight in una rete virtuale di Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 340974201d62f97669db442f4a95439a6ac90a5e
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960603"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architettura della rete virtuale di Azure HDInsight

Questo articolo illustra le risorse presenti quando si distribuisce un cluster HDInsight in una rete virtuale di Azure personalizzata. Queste informazioni consentono di connettere le risorse locali al cluster HDInsight in Azure. Per altre informazioni sulle reti virtuali di Azure, vedere informazioni su [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipi di risorse nei cluster HDInsight di Azure

I cluster Azure HDInsight hanno tipi diversi di macchine virtuali o nodi. Ogni tipo di nodo svolge un ruolo nel funzionamento del sistema. La tabella seguente riepiloga questi tipi di nodo e i relativi ruoli nel cluster.

| Type | Descrizione |
| --- | --- |
| Nodo head |  Per tutti i tipi di cluster ad eccezione di Apache Storm, i nodi head ospitano i processi che gestiscono l'esecuzione dell'applicazione distribuita. Il nodo Head è anche il nodo in cui è possibile accedere tramite SSH ed eseguire le applicazioni che vengono quindi coordinate per l'esecuzione tra le risorse del cluster. Il numero di nodi head è fisso a due per tutti i tipi di cluster. |
| Nodo ZooKeeper | Zookeeper coordina le attività tra i nodi che eseguono l'elaborazione dei dati. Il nodo head viene anche designato come leader e tiene traccia del nodo head che esegue un servizio master specifico. Il numero di nodi ZooKeeper è fisso a tre. |
| Nodo del ruolo di lavoro | Rappresenta i nodi che supportano la funzionalità di elaborazione dei dati. I nodi di lavoro possono essere aggiunti o rimossi dal cluster per ridimensionare la capacità di elaborazione e gestire i costi. |
| Nodo R Server Edge | Il nodo R Server Edge rappresenta il nodo a cui è possibile accedere tramite SSH ed eseguire le applicazioni che vengono quindi coordinate per l'esecuzione tra le risorse del cluster. Un nodo perimetrale non partecipa all'analisi dei dati all'interno del cluster. Questo nodo ospita anche R Studio Server, che consente di eseguire l'applicazione R usando un browser. |
| Nodo Region | Per il tipo di cluster HBase, il nodo Region (noto anche come nodo dati) esegue il server di area. I server di area servono e gestiscono una parte dei dati gestiti da HBase. I nodi Region possono essere aggiunti o rimossi dal cluster per ridimensionare la capacità di elaborazione e gestire i costi.|
| Nodo Nimbus | Per il tipo di cluster Storm, il nodo Nimbus offre funzionalità simili a quelle del nodo head. Il nodo Nimbus Assegna attività ad altri nodi in un cluster tramite Zookeeper, che coordina l'esecuzione delle topologie Storm. |
| Nodo supervisore | Per il tipo di cluster Storm, il nodo Supervisor esegue le istruzioni fornite dal nodo Nimbus per eseguire l'elaborazione desiderata. |

## <a name="basic-virtual-network-resources"></a>Risorse di rete virtuale di base

Il diagramma seguente illustra il posizionamento dei nodi HDInsight e delle risorse di rete in Azure.

![Diagramma delle entità HDInsight create in VNET personalizzati di Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Le risorse predefinite presenti quando HDInsight viene distribuito in una rete virtuale di Azure includono i tipi di nodo del cluster indicati nella tabella precedente, nonché i dispositivi di rete che supportano la comunicazione tra la rete virtuale e le reti esterne.

La tabella seguente riepiloga i nove nodi del cluster creati quando HDInsight viene distribuito in una rete virtuale di Azure personalizzata.

| Tipo di risorsa | Numero presente | Dettagli |
| --- | --- | --- |
|Nodo head | two |    |
|Nodo Zookeeper | tre | |
|Nodo del ruolo di lavoro | two | Questo numero può variare in base alla configurazione del cluster e al ridimensionamento. Per Apache Kafka è necessario un minimo di tre nodi di lavoro.  |
|Nodo gateway | two | I nodi del gateway sono macchine virtuali di Azure create in Azure, ma non sono visibili nella sottoscrizione. Se è necessario riavviare questi nodi, contattare il supporto tecnico. |

Le risorse di rete seguenti sono presenti automaticamente nella rete virtuale usata con HDInsight:

| Risorsa di rete | Numero presente | Dettagli |
| --- | --- | --- |
|Servizio di bilanciamento del carico | tre | |
|Interfacce di rete | nove | Questo valore è basato su un normale cluster, in cui ogni nodo dispone di una propria interfaccia di rete. Le nove interfacce sono per i due nodi head, tre nodi Zookeeper, due nodi del ruolo di lavoro e due nodi gateway indicati nella tabella precedente. |
|Indirizzi IP pubblici | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Endpoint per la connessione a HDInsight

È possibile accedere al cluster HDInsight in tre modi:

- Un endpoint HTTPS esterno alla rete virtuale in `CLUSTERNAME.azurehdinsight.net`.
- Un endpoint SSH per la connessione diretta a nodo Head all' `CLUSTERNAME-ssh.azurehdinsight.net`indirizzo.
- Un endpoint HTTPS all'interno della rete `CLUSTERNAME-int.azurehdinsight.net`virtuale. Si noti "-int" in questo URL. Questo endpoint verrà risolto in un indirizzo IP privato nella rete virtuale e non sarà accessibile dalla rete Internet pubblica.

A questi tre endpoint viene assegnato un servizio di bilanciamento del carico.

Gli indirizzi IP pubblici vengono anche forniti ai due endpoint che consentono la connessione dall'esterno della rete virtuale.

1. Un indirizzo IP pubblico viene assegnato al servizio di bilanciamento del carico per il nome di dominio completo (FQDN) da usare per la connessione al cluster da `CLUSTERNAME.azurehdinsight.net`Internet.
1. Il secondo indirizzo IP pubblico viene usato per il nome `CLUSTERNAME-ssh.azurehdinsight.net`di dominio solo SSH.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere il traffico in ingresso nei cluster HDInsight in una rete virtuale con endpoint privato](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
