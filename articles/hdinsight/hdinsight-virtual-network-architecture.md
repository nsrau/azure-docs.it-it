---
title: Architettura della rete virtuale di Azure HDInsightAzure HDInsight virtual network architecture
description: Informazioni sulle risorse disponibili quando si crea un cluster HDInsight in una rete virtuale di Azure.Learn the resources available when you create an HDInsight cluster in an Azure Virtual Network.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390215"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architettura della rete virtuale di Azure HDInsightAzure HDInsight virtual network architecture

Questo articolo illustra le risorse presenti quando si distribuisce un cluster HDInsight in una rete virtuale di Azure personalizzata. Queste informazioni consentono di connettere le risorse locali al cluster HDInsight in Azure.This information will help you to connect on-premises resources to your HDInsight cluster in Azure. Per altre informazioni sulle reti virtuali di Azure, vedere [Che cos'è](../virtual-network/virtual-networks-overview.md)la rete virtuale di Azure? .

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipi di risorse nei cluster di Azure HDInsightResource types in Azure HDInsight clusters

I cluster di Azure HDInsight hanno tipi diversi di macchine virtuali o nodi. Ogni tipo di nodo svolge un ruolo nel funzionamento del sistema. Nella tabella seguente vengono riepilogati questi tipi di nodo e i relativi ruoli nel cluster.

| Type | Descrizione |
| --- | --- |
| Nodo head |  Per tutti i tipi di cluster ad eccezione di Apache Storm, i nodi head ospitano i processi che gestiscono l'esecuzione dell'applicazione distribuita. Il nodo head è anche il nodo in cui è possibile sSH ed eseguire applicazioni che vengono quindi coordinate per l'esecuzione tra le risorse del cluster. Il numero di nodi head è fissato a due per tutti i tipi di cluster. |
| Nodo zooKeeper | Il gestore dello zoo coordina le attività tra i nodi che eseguono l'elaborazione dei dati. Esegue anche l'elezione leader del nodo head e tiene traccia di quale nodo head esegue un servizio master specifico. Il numero di nodi di zooKeeper è fissato a tre. |
| Nodo del ruolo di lavoro | Rappresenta i nodi che supportano la funzionalità di elaborazione dati. I nodi di lavoro possono essere aggiunti o rimossi dal cluster per scalare le capacità di elaborazione e gestire i costi. |
| Nodo perimetrale del server R | Il nodo perimetrale R Server rappresenta il nodo in cui è possibile eseguire SSH ed eseguire applicazioni coordinate per l'esecuzione tra le risorse del cluster. Un nodo perimetrale non partecipa all'analisi dei dati all'interno del cluster. Questo nodo ospita anche R Studio Server, consentendo di eseguire l'applicazione R utilizzando un browser. |
| Nodo Area | Per il tipo di cluster HBase, il nodo dell'area (noto anche come nodo di dati) esegue il server di area. I server di area gestiscono e gestiscono una parte dei dati gestiti da HBase. I nodi region possono essere aggiunti o rimossi dal cluster per scalare le capacità di elaborazione e gestire i costi.|
| Nodo Nimbus | Per il tipo di cluster Storm, il nodo Nimbus fornisce una funzionalità simile al nodo Head. Il nodo Nimbus assegna le attività ad altri nodi di un cluster tramite il gestore dello zoo, che coordina l'esecuzione delle topologie Storm. |
| Nodo Supervisore | Per il tipo di cluster Storm, il nodo supervisore esegue le istruzioni fornite dal nodo Nimbus per eseguire l'elaborazione. |

## <a name="resource-naming-conventions"></a>Convenzioni di denominazione delle risorse

Utilizzare i nomi di dominio completi (FQDN) per l'indirizzamento dei nodi nel cluster. È possibile ottenere i nomi FQDN per vari tipi di nodo nel cluster utilizzando [l'API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Questi FQDN saranno nel `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`formato .

Il `<node-type-prefix>` sarà *hn* per headnodes, *wn* per i nodi di lavoro e *zn* per i nodi zookeeper.

Se è necessario solo il nome host, utilizzare solo la prima parte del nome di dominio completo:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Risorse di rete virtuale di baseBasic virtual network resources

The following diagram shows the placement of HDInsight nodes and network resources in Azure.

![Diagramma delle entità HDInsight create in Azure custom VNET](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Le risorse predefinite in una rete virtuale di Azure includono i tipi di nodo del cluster menzionati nella tabella precedente. E dispositivi di rete che supportano la comunicazione tra la rete virtuale e le reti esterne.

La tabella seguente riepiloga i nove nodi del cluster creati quando HDInsight viene distribuito in una rete virtuale di Azure personalizzata.

| Tipo di risorsa | Numero presente | Dettagli |
| --- | --- | --- |
|Nodo head | two |    |
|Nodo Zookeeper | three | |
|Nodo del ruolo di lavoro | two | Questo numero può variare in base alla configurazione e al ridimensionamento del cluster. Per Apache Kafka è necessario un minimo di tre nodi di lavoro.  |
|Nodo gateway | two | I nodi gateway sono macchine virtuali di Azure create in Azure, ma non visibili nella sottoscrizione. Contattare il supporto tecnico se è necessario riavviare questi nodi. |

The following network resources present are automatically created inside the virtual network used with HDInsight:

| Risorsa di rete | Numero presente | Dettagli |
| --- | --- | --- |
|Bilanciamento del carico | three | |
|Interfacce di rete | Nove | Questo valore è basato su un cluster normale, in cui ogni nodo ha la propria interfaccia di rete. Le nove interfacce sono per: due nodi head, tre nodi zookeeper, due nodi di lavoro e due nodi gateway menzionati nella tabella precedente. |
|Indirizzi IP pubblici | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Endpoint per la connessione a HDInsightEndpoints for connecting to HDInsight

È possibile accedere al cluster HDInsight in tre modi:You can access your HDInsight cluster in three ways:

- Un endpoint HTTPS all'esterno `CLUSTERNAME.azurehdinsight.net`della rete virtuale in .
- Un endpoint SSH per la connessione `CLUSTERNAME-ssh.azurehdinsight.net`diretta al nodo head in corrispondenza di .
- Un endpoint HTTPS all'interno della rete `CLUSTERNAME-int.azurehdinsight.net`virtuale . Si noti il "`-int`" in questo URL. Questo endpoint verrà risolto in un indirizzo IP privato in tale rete virtuale e non è accessibile da Internet pubblico.

A questi tre endpoint viene assegnato un servizio di bilanciamento del carico.

Gli indirizzi IP pubblici vengono inoltre forniti ai due endpoint che consentono la connessione dall'esterno della rete virtuale.

1. Un indirizzo IP pubblico viene assegnato al servizio di bilanciamento del carico per il `CLUSTERNAME.azurehdinsight.net`nome di dominio completo (FQDN) da utilizzare per la connessione al cluster da Internet.
1. Il secondo indirizzo IP pubblico viene utilizzato `CLUSTERNAME-ssh.azurehdinsight.net`per il nome di dominio solo SSH .

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere il traffico in ingresso ai cluster HDInsight in una rete virtuale con endpoint privatoSecure incoming traffic to HDInsight clusters in a virtual network with private endpoint](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
