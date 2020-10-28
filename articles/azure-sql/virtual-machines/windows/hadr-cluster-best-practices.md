---
title: Procedure consigliate per la configurazione del cluster
description: Informazioni sulle configurazioni del cluster supportate quando si configura la disponibilità elevata e il ripristino di emergenza (HADR) per SQL Server in macchine virtuali di Azure, ad esempio i quorum supportati o le opzioni di routing della connessione.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: b385d6dfb5beba481ad92403d69f5d0988f3bce3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786429"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Procedure consigliate per la configurazione del cluster (SQL Server nelle macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Un cluster viene usato per la disponibilità elevata e il ripristino di emergenza (HADR) con SQL Server in macchine virtuali (VM) di Azure. 

Questo articolo fornisce le procedure consigliate per la configurazione del cluster per le [istanze del cluster di failover (failover)](failover-cluster-instance-overview.md) e i [gruppi di disponibilità](availability-group-overview.md) quando vengono usate con SQL Server nelle VM di Azure. 


## <a name="networking"></a>Rete

Usare una singola scheda di interfaccia di rete per server (nodo cluster) e una singola subnet. La rete di Azure dispone di ridondanza fisica, che rende le schede di rete e le subnet aggiuntive non necessarie in un cluster guest di macchine virtuali di Azure. Il report di convalida del cluster informa che i nodi sono raggiungibili solo in una singola rete. È possibile ignorare questo avviso nei cluster di failover guest della macchina virtuale di Azure.

## <a name="quorum"></a>Quorum

Sebbene un cluster a due nodi funzioni senza una [risorsa quorum](/windows-server/storage/storage-spaces/understand-quorum), i clienti devono usare esclusivamente una risorsa quorum per ottenere supporto di produzione. La convalida del cluster non passerà alcun cluster senza una risorsa quorum. 

Tecnicamente, un cluster a tre nodi può sopravvivere a una perdita a nodo singolo (fino a due nodi) senza una risorsa quorum. Tuttavia, quando il cluster si trova in due nodi, c'è il rischio che le risorse del cluster passino alla modalità offline in caso di perdita del nodo o di errore di comunicazione per evitare uno scenario Split Brain.

La configurazione di una risorsa quorum consente al cluster di continuare online con un solo nodo online.

La tabella seguente elenca le opzioni del quorum disponibili nell'ordine consigliato per l'uso con una macchina virtuale di Azure, con il disco di controllo che è la scelta preferita: 


||[Disco di controllo](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Cloud di controllo](/windows-server/failover-clustering/deploy-cloud-witness)  |[Condivisione file di controllo](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Sistema operativo supportato**| Tutti |Windows Server 2016+| Tutti|


### <a name="disk-witness"></a>Disco di controllo

Un disco di controllo è un disco di dimensioni ridotte del cluster nel gruppo di archiviazione disponibile nel cluster. Questo disco è a disponibilità elevata ed è in grado di eseguire il failover tra i nodi. Contiene una copia del database del cluster, con dimensioni predefinite generalmente inferiori a 1 GB. Il disco di controllo è l'opzione quorum preferita per tutti i cluster che usano i dischi condivisi di Azure (o qualsiasi soluzione di dischi condivisi, ad esempio una rete SAN SCSI, iSCSI o Fibre Channel).  Un volume condiviso cluster non può essere utilizzato come disco di controllo.

Configurare un disco condiviso di Azure come disco di controllo. 

Per iniziare, vedere [configurare un disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo.


**Sistemi operativi supportati** : Tutti   


### <a name="cloud-witness"></a>Cloud di controllo

Un server di controllo del mirroring è un tipo di quorum di controllo del cluster di failover che usa Microsoft Azure per fornire un voto sul quorum del cluster. Le dimensioni predefinite sono pari a circa 1 MB e contengono solo il timestamp. Un server di controllo del cloud è ideale per le distribuzioni in più siti, più zone e più aree.

Per iniziare, vedere [configurare un cloud](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)di controllo.


**Sistemi operativi supportati** : Windows Server 2016 e versioni successive   


### <a name="file-share-witness"></a>Condivisione file di controllo

Una condivisione file di controllo è una condivisione file SMB che in genere è configurata in un file server che esegue Windows Server. Mantiene le informazioni di clustering in un file witness. log, ma non archivia una copia del database del cluster. In Azure è possibile configurare una [condivisione file di Azure](../../../storage/files/storage-how-to-create-file-share.md) da usare come condivisione file di controllo oppure è possibile usare una condivisione file in una macchina virtuale separata.

Se si intende usare una condivisione file di Azure, è possibile montarla con lo stesso processo usato per [montare la condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Per iniziare, vedere [configurare una condivisione file](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo.


**Sistemi operativi supportati** : Windows Server 2012 e versioni successive   

## <a name="connectivity"></a>Connettività

In un ambiente di rete locale tradizionale, un'istanza del cluster di failover di SQL Server sembra essere una singola istanza di SQL Server in esecuzione in un singolo computer. Poiché viene eseguito il failover dell'istanza del cluster di failover dal nodo al nodo, il nome della rete virtuale (VNN) per l'istanza fornisce un punto di connessione unificato e consente alle applicazioni di connettersi all'istanza di SQL Server senza sapere quale nodo è attualmente attivo. Quando si verifica un failover, il nome della rete virtuale viene registrato nel nuovo nodo attivo dopo l'avvio. Questo processo è trasparente per il client o l'applicazione che si connette a SQL Server e questo riduce al minimo i tempi di inattività del client o dell'applicazione durante un errore. Analogamente, il listener del gruppo di disponibilità utilizza un VNN per instradare il traffico alla replica appropriata. 

Usare un VNN con Azure Load Balancer o un nome di rete distribuito (DNN) per instradare il traffico verso VNN dell'istanza del cluster di failover con SQL Server in macchine virtuali di Azure o per sostituire il listener VNN esistente in un gruppo di disponibilità. 


Nella tabella seguente viene confrontata la supportabilità della connessione HADR: 

| |**Nome rete virtuale (VNN)**  |**Nome rete distribuita (DNN)**  |
|---------|---------|---------|
|**Versione minima del sistema operativo**| Tutti | Windows Server 2016 |
|**Versione minima di SQL Server** |Tutti |SQL Server 2019 CU2 (per FCI)<br/> SQL Server 2019 CU8 (per AG)|
|**Soluzione HADR supportata** | Istanza del cluster di failover <br/> gruppo di disponibilità | Istanza del cluster di failover <br/> gruppo di disponibilità|


### <a name="virtual-network-name-vnn"></a>VNN (Virtual Network Name)

Poiché il punto di accesso IP virtuale funziona in modo diverso in Azure, è necessario configurare [Azure Load Balancer](../../../load-balancer/index.yml) per instradare il traffico all'indirizzo IP dei nodi FCI o del listener del gruppo di disponibilità. In macchine virtuali di Azure, un servizio di bilanciamento del carico include l'indirizzo IP per il VNN su cui si basano le risorse del SQL Server cluster. Il servizio di bilanciamento del carico distribuisce i flussi in ingresso che arrivano al front-end e quindi instrada il traffico alle istanze definite dal pool back-end. Il flusso del traffico viene configurato usando le regole di bilanciamento del carico e i probe di integrità. Con SQL Server FCI, le istanze del pool back-end sono le macchine virtuali di Azure in esecuzione SQL Server. 

Si verifica un lieve ritardo di failover quando si usa il servizio di bilanciamento del carico, perché il probe di integrità esegue controlli attivi ogni 10 secondi per impostazione predefinita. 

Per iniziare, informazioni su come configurare Azure Load Balancer per l' [istanza del cluster di failover](failover-cluster-instance-vnn-azure-load-balancer-configure.md) o un [gruppo di disponibilità](availability-group-vnn-azure-load-balancer-configure.md)

**Sistemi operativi supportati** : Tutti   
**Versione di SQL supportata** : Tutti   
**Soluzione HADR supportata** : istanza del cluster di failover e gruppo di disponibilità   


### <a name="distributed-network-name-dnn"></a>DNN (Distributed Network Name)

Il nome di rete distribuita è una nuova funzionalità di Azure per SQL Server 2019. DNN fornisce un modo alternativo per SQL Server client di connettersi all'istanza del cluster di failover di SQL Server o al gruppo di disponibilità senza usare un servizio di bilanciamento del carico. 

Quando viene creata una risorsa DNN, il cluster associa il nome DNS agli indirizzi IP di tutti i nodi del cluster. Il client SQL tenterà di connettersi a ogni indirizzo IP in questo elenco per individuare la risorsa a cui connettersi.  È possibile accelerare questo processo specificando `MultiSubnetFailover=True` nella stringa di connessione. Questa impostazione indica al provider di provare tutti gli indirizzi IP in parallelo, in modo che il client possa connettersi immediatamente all'istanza FCI o al listener. 

Quando possibile, è consigliabile usare un nome di rete distribuita su un servizio di bilanciamento del carico perché: 
- La soluzione end-to-end è più affidabile perché non è più necessario gestire la risorsa di bilanciamento del carico. 
- L'eliminazione dei probe del servizio di bilanciamento del carico riduce al minimo la durata del failover. 
- Il DNN semplifica il provisioning e la gestione del listener del gruppo di disponibilità o dell'istanza del cluster di failover con SQL Server nelle VM di Azure. 

La maggior parte delle funzionalità SQL Server funziona in modo trasparente con il cluster di failover e i gruppi di disponibilità quando si usa DNN, ma esistono alcune funzionalità che possono richiedere particolare attenzione. Per altre informazioni, vedere interoperabilità [FCI e DNN](failover-cluster-instance-dnn-interoperability.md) e [interoperabilità AG e DNN](availability-group-dnn-interoperability.md) . 

Per iniziare, informazioni su come configurare una risorsa nome di rete distribuita per [un'istanza del cluster di failover](failover-cluster-instance-distributed-network-name-dnn-configure.md) o un [gruppo di disponibilità](availability-group-distributed-network-name-dnn-listener-configure.md)

**Sistemi operativi supportati** : Windows Server 2016 e versioni successive   
**Versione SQL supportata** : SQL Server 2019 Cu2 (FCI) e SQL Server 2019 CU8 (AG)   
**Soluzione HADR supportata** : istanza del cluster di failover e gruppo di disponibilità   


## <a name="limitations"></a>Limitazioni

Quando si lavora con la FCI o i gruppi di disponibilità e SQL Server in macchine virtuali di Azure, tenere presenti le limitazioni seguenti. 

### <a name="msdtc"></a>MSDTC 

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019, con archiviazione in volumi condivisi del cluster e [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md), oppure nelle VM di SQL Server che usano dischi condivisi di Azure. 

Nelle macchine virtuali di Azure, MSDTC non è supportato in Windows Server 2016 o versione precedente con volumi condivisi del cluster per i motivi seguenti:

- La risorsa MSDTC in cluster non può essere configurata per usare la risorsa di archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna risorsa di archiviazione condivisa disponibile per l'uso, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver determinato le procedure consigliate appropriate per la soluzione, iniziare preparando la [macchina virtuale SQL Server per l'istanza FCI](failover-cluster-instance-prepare-vm.md) o creando il gruppo di disponibilità usando il [portale di Azure](availability-group-azure-portal-configure.md), l'interfaccia della riga di comando di [Azure/PowerShell](./availability-group-az-commandline-configure.md)o i [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md).