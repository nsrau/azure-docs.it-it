---
title: Istanze del cluster di failover
description: Informazioni sulle istanze del cluster di failover (failover) con SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 6d77855f095c59b47156af735f4581076ce5a09c
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89611627"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Istanze del cluster di failover con SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo presenta le differenze di funzionalità quando si lavora con le istanze del cluster di failover per SQL Server in macchine virtuali (VM) di Azure. 

## <a name="overview"></a>Panoramica

SQL Server in macchine virtuali di Azure usa la funzionalità WSFC (Windows Server Failover Clustering) per fornire la disponibilità elevata locale tramite la ridondanza a livello di istanza del server: un'istanza del cluster di failover. Un'istanza FCI è una singola istanza di SQL Server installata nei nodi WSFC (o semplicemente nel cluster) e, possibilmente, in più subnet. In rete, un'istanza del cluster di failover sembra essere un'istanza di SQL Server in esecuzione in un singolo computer. Tuttavia, l'istanza FCI fornisce il failover da un nodo WSFC a un altro se il nodo corrente non è più disponibile.

Il resto dell'articolo è incentrato sulle differenze per le istanze del cluster di failover quando vengono usate con SQL Server nelle VM di Azure. Per ulteriori informazioni sulla tecnologia di clustering di failover, vedere: 

- [Tecnologie cluster di Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server istanze del cluster di failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Le istanze del cluster di failover con SQL Server in macchine virtuali di Azure supportano l'uso di un disco di controllo, un cloud di controllo o una condivisione file di controllo per il quorum del cluster.

Per altre informazioni, vedere [procedure consigliate per il quorum con SQL Server macchine virtuali in Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Archiviazione

Negli ambienti cluster locali tradizionali, un cluster di failover Windows utilizza una rete di archiviazione (SAN) accessibile da entrambi i nodi come risorsa di archiviazione condivisa. I file SQL Server sono ospitati nello spazio di archiviazione condiviso e solo il nodo attivo può accedere ai file in una sola volta. 

SQL Server in macchine virtuali di Azure offre diverse opzioni come una soluzione di archiviazione condivisa per una distribuzione di SQL Server istanze del cluster di failover: 

||[Dischi condivisi di Azure](../../../virtual-machines/windows/disks-shared.md)|[Condivisioni file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Versione minima del sistema operativo**| Tutti |Windows Server 2012|Windows Server 2016|
|**Versione minima di SQL Server**|Tutti|SQL Server 2012|SQL Server 2016|
|**Disponibilità VM supportata** |Set di disponibilità con gruppi di posizionamento vicini |Set di disponibilità e zone di disponibilità|Set di disponibilità |
|**Supporta FileStream**|Sì|No|Sì |
|**Cache BLOB di Azure**|No|No|Sì|

Il resto di questa sezione elenca i vantaggi e le limitazioni di ogni opzione di archiviazione disponibile per SQL Server nelle VM di Azure. 

### <a name="azure-shared-disks"></a>Dischi condivisi di Azure

I [dischi condivisi di Azure](../../../virtual-machines/windows/disks-shared.md) sono una funzionalità di [Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Windows Server Failover Clustering supporta l'uso di dischi condivisi di Azure con un'istanza del cluster di failover. 

**Sistema operativo supportato**: tutti   
**Versione SQL supportata**: tutti     

**Vantaggi**: 
- Utile per le applicazioni che desiderano eseguire la migrazione ad Azure mantenendo l'architettura di disponibilità elevata e ripristino di emergenza (HADR) così come sono. 
- Consente di eseguire la migrazione di applicazioni in cluster in Azure così come sono a causa del supporto per le prenotazioni permanenti SCSI (SCSI PR). 
- Supporta la SSD Premium condivisa di Azure e l'archiviazione su disco Ultra di Azure.
- Può usare un singolo disco condiviso o eseguire lo striping di più dischi condivisi per creare un pool di archiviazione condiviso. 
- Supporta FILESTREAM.


**Limiti**: 
- Le macchine virtuali devono essere inserite nello stesso set di disponibilità e nel gruppo di posizionamento di prossimità.
- Le zone di disponibilità non sono supportate.
- SSD Premium caching del disco non è supportata.
 
Per iniziare, vedere [SQL Server istanza del cluster di failover con i dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Spazi di archiviazione diretta

[Spazi di archiviazione diretta](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) è una funzionalità di Windows Server supportata con il clustering di failover in macchine virtuali di Azure. Fornisce una SAN virtuale basata su software.

**Sistema operativo supportato**: Windows Server 2016 e versioni successive   
**Versione SQL supportata**: SQL Server 2016 e versioni successive   


**Vantaggi** 
- Una larghezza di banda di rete sufficiente consente una soluzione di archiviazione condivisa affidabile e a elevate prestazioni. 
- Supporta la cache BLOB di Azure, quindi le letture possono essere gestite localmente dalla cache. Gli aggiornamenti vengono replicati contemporaneamente in entrambi i nodi. 
- Supporta FileStream. 

**Limitazioni**
- Disponibile solo per Windows Server 2016 e versioni successive. 
- Le zone di disponibilità non sono supportate.
- Richiede la stessa capacità del disco collegata a entrambe le macchine virtuali. 
- È necessaria una larghezza di banda di rete elevata per ottenere prestazioni elevate a causa della replica continua del disco. 
- Richiede una dimensione di macchina virtuale più grande e un doppio pagamento per l'archiviazione, perché l'archiviazione è collegata a ogni macchina virtuale. 

Per iniziare, vedere [SQL Server istanza del cluster di failover con spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Condivisione file Premium

Le [condivisioni file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) sono una funzionalità di [file di Azure](../../../storage/files/index.yml). Le condivisioni file Premium sono supportate da unità SSD e hanno una latenza costantemente bassa. Sono completamente supportati per l'uso con istanze del cluster di failover per SQL Server 2012 o versioni successive in Windows Server 2012 o versioni successive. Le condivisioni file Premium offrono maggiore flessibilità, perché è possibile ridimensionare e ridimensionare una condivisione file senza tempi di inattività.

**Sistema operativo supportato**: Windows Server 2012 e versioni successive   
**Versione SQL supportata**: SQL Server 2012 e versioni successive   

**Vantaggi** 
- Solo una soluzione di archiviazione condivisa per le macchine virtuali distribuite in più zone di disponibilità. 
- File system completamente gestite con latenze a una sola cifra e prestazioni di I/O a picchi. 

**Limitazioni**
- Disponibile solo per Windows Server 2012 e versioni successive. 
- FileStream non è supportato. 


Per iniziare, vedere [SQL Server istanza del cluster di failover con la condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

Sono disponibili soluzioni di clustering partner con archiviazione supportata. 

**Sistema operativo supportato**: tutti   
**Versione SQL supportata**: tutti   

Un esempio usa il nome dell'oggetto di archiviazione. Per altre informazioni, vedere il post di Blog sul [clustering di failover e](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)il tipo di dati.

### <a name="iscsi-and-expressroute"></a>iSCSI e ExpressRoute

È anche possibile esporre una risorsa di archiviazione a blocchi condivisa di destinazione iSCSI tramite Azure ExpressRoute. 

**Sistema operativo supportato**: tutti   
**Versione SQL supportata**: tutti   

Ad esempio, NetApp Private Storage (NPS) espone una destinazione iSCSI tramite ExpressRoute con Equinix a macchine virtuali di Azure.

Per le soluzioni di archiviazione e replica dei dati condivise dei partner Microsoft, contattare il fornitore per individuare eventuali problemi relativi all'accesso ai dati in caso di failover.

## <a name="connectivity"></a>Connettività

Le istanze del cluster di failover con SQL Server in macchine virtuali di Azure utilizzano un [nome di rete distribuita (DNN)](hadr-distributed-network-name-dnn-configure.md) o un [nome di rete virtuale (VNN) con Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md) per instradare il traffico all'istanza SQL Server, indipendentemente dal nodo attualmente proprietario delle risorse del cluster. Quando si usano determinate funzionalità e DNN con una SQL Server FCI, è necessario tenere presenti alcune considerazioni aggiuntive. Per altre informazioni, vedere [interoperabilità di DNN con SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) . 

Per altre informazioni sulle opzioni di connettività del cluster, vedere [Route HADR connections to SQL Server on Azure VM](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitazioni

Per le istanze del cluster di failover con SQL Server in macchine virtuali di Azure, tenere presenti le limitazioni seguenti. 

### <a name="lightweight-resource-provider"></a>Provider di risorse leggero   
A questo punto, SQL Server istanze del cluster di failover in macchine virtuali di Azure sono supportate solo con la [modalità di gestione semplice](sql-vm-resource-provider-register.md#management-modes) dell' [estensione dell'agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md). Per passare dalla modalità di estensione completa a quella Lightweight, eliminare la risorsa della **macchina virtuale SQL** per le macchine virtuali corrispondenti e quindi registrarla con il provider di risorse VM SQL in modalità lightweight. Quando si elimina la risorsa **macchina virtuale SQL** utilizzando la portale di Azure, deselezionare la casella di controllo accanto alla macchina virtuale corretta. 

L'estensione completa supporta funzionalità quali backup automatizzato, applicazione di patch e gestione avanzata del portale. Queste funzionalità non funzioneranno per le macchine virtuali SQL Server dopo la reinstallazione dell'agente in modalità di gestione leggera.

### <a name="msdtc"></a>MSDTC 

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione in volumi condivisi cluster (CSV) e [azure Load Balancer standard](../../../load-balancer/load-balancer-standard-overview.md) o in macchine virtuali SQL Server che usano dischi condivisi di Azure. 

In macchine virtuali di Azure, MSDTC non è supportato per Windows Server 2016 o versioni precedenti con volumi condivisi cluster, perché:

- La risorsa MSDTC in cluster non può essere configurata per usare la risorsa di archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna risorsa di archiviazione condivisa disponibile per l'uso, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.


## <a name="next-steps"></a>Passaggi successivi

Esaminare le procedure consigliate per le [configurazioni del cluster](hadr-cluster-best-practices.md)e quindi [preparare la macchina virtuale SQL Server per l'istanza FCI](failover-cluster-instance-prepare-vm.md). 

Per altre informazioni, vedere: 

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server istanze del cluster di failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

