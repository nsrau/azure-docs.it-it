---
title: Spostare risorse di archiviazione di mainframe in archiviazione di Azure
description: Le risorse di archiviazione di Azure con scalabilità elevata possono aiutare le organizzazioni basate su mainframe eseguire la migrazione e modernizzare le applicazioni z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896348"
---
# <a name="move-mainframe-storage-to-azure"></a>Spostare risorse di archiviazione di mainframe in Azure

Per eseguire i carichi di lavoro di mainframe in Microsoft Azure, è necessario conoscere le differenze tra le funzionalità del mainframe in Azure. Le risorse di archiviazione a scalabilità elevata possono aiutare le organizzazioni iniziano a modernizzare senza abbandonare le applicazioni su che si basano.

Azure offre funzionalità simili a mainframe e capacità di archiviazione è paragonabile a sistemi basati su z14 IBM (il modello più recente al momento della stesura di questo articolo). Questo articolo descrive come ottenere risultati simili in Azure.

## <a name="mainframe-storage-at-a-glance"></a>Archiviazione di mainframe in modo immediato

Mainframe IBM caratterizza archiviazione in due modi. Il primo è un dispositivo di archiviazione di accesso diretta (DAS). Il secondo è archiviazione sequenziale. Per gestire l'archiviazione, il mainframe fornisce dati struttura Storage Management sottosistema (DFSMS). Gestisce l'accesso ai dati per i dispositivi di archiviazione diversi.

[DAS](https://en.wikipedia.org/wiki/Direct-access_storage_device) fa riferimento a un dispositivo distinto per l'archiviazione (non in-memory) secondario che consente a un indirizzo univoco da utilizzare per l'accesso diretto dei dati. In origine, il termine DAS applicato a rotazione dei dischi, tamburi magnetici o celle di dati. Tuttavia, dopo il termine inoltre possibile applicare ai dispositivi di archiviazione SSD (Solid State Drive), reti di area di archiviazione (SAN), collegare rete (NAS) di archiviazione e unità ottiche. Ai fini di questo documento, DAS fa riferimento a rotazione dei dischi, SAN e unità SSD.

A differenza di archiviazione DAS, archiviazione sequenziali su un mainframe fa riferimento ai dispositivi, ad esempio le unità nastro in cui i dati sono accessibile da un punto di partenza, quindi letti o scritti in una riga.

I dispositivi di archiviazione in genere sono connesse tramite una connessione fiber (FICON) o sono accessibili direttamente sull'uso di bus dei / o al mainframe [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), una tecnologia di IBM per le comunicazioni ad alta velocità tra le partizioni in un server con un hypervisor.

La maggior parte dei sistemi mainframe separano l'archiviazione in due tipi:

- *Spazio di archiviazione online* (noto anche come archiviazione ad accesso frequente) è necessaria per le operazioni quotidiane. Archiviazione DAS viene in genere usata per questo scopo. Tuttavia, archiviazione sequenziali, ad esempio i backup su nastro giornalieri (logici o fisici), è anche utilizzabile per questo scopo.

- *Spazio di archiviazione* (noto anche come archiviazione offline sicura) non è garantito a essere montato in un determinato momento. In alternativa, è montato e accessibile in base alle esigenze. Spazio di archiviazione viene spesso implementato utilizzando backup su nastro sequenziale (logico o fisico) per l'archiviazione.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe e latenza dei / o e IOPS

Mainframe vengono spesso usati per le applicazioni che richiedono i/o di prestazioni elevate e bassa latenza dei / o. È possibile farlo usando le connessioni FICON ai dispositivi dei / o e HiperSockets. Quando HiperSockets vengono utilizzati per connettere le applicazioni e dispositivi direttamente al canale dei / o di un mainframe, è possibile ottenere la latenza nei microsecondi.

## <a name="azure-storage-at-a-glance"></a>Archiviazione di Azure in modo immediato

Azure infrastructure-as-a-service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) opzioni di archiviazione offrono capacità di mainframe confrontabili.

Microsoft offre relativi a diversi petabyte di spazio di archiviazione per le applicazioni ospitate in Azure e sono disponibili diverse opzioni di archiviazione. Queste vanno da archiviazione SSD per prestazioni elevate nell'archivio blob a basso costo per archiviazione di massa e archivi. Inoltre, Azure offre un'opzione di ridondanza dei dati per l'archiviazione, ovvero qualcosa che accetta più complessa da configurare in un ambiente mainframe.

Archiviazione di Azure è disponibile come [dischi di Azure](/azure/virtual-machines/windows/managed-disks-overview), [file di Azure](/azure/storage/files/storage-files-introduction), e [i BLOB di Azure](/azure/storage/blobs/storage-blobs-overview) come sono riepilogate nella tabella seguente. Altre informazioni sulle [quando usare ognuna](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>DESCRIZIONE</th><th>Usa questa opzione quando vuoi:</th></tr>
</thead>
<tbody>
<tr><td>File di Azure
</td>
<td>
Fornisce un'interfaccia SMB, librerie client e un <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> interfaccia che consente l'accesso ovunque ai file archiviati.
</td>
<td><ul>
<li>Lift- and -shift un'applicazione nel cloud quando l'applicazione usa le API del sistema di file nativo per condividere dati tra altre applicazioni in esecuzione in Azure.</li>
<li>Store lo sviluppo e strumenti che devono essere accessibili dal numero di macchine virtuali di debug.</li>
</ul>
</td>
</tr>
<tr><td>BLOB di Azure
</td>
<td>Fornisce librerie client e un <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> interfaccia che consente dati non strutturati archiviati e di accedervi su larga scala in BLOB in blocchi. Supporta anche <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> per le soluzioni aziendali di analisi di Big Data.
</td>
<td><ul>
<li>Supporta gli scenari di streaming e ad accesso casuale in un'applicazione.</li>
<li>Avere accesso ai dati dell'applicazione da qualsiasi posizione.</li>
<li>Crea un lake dati aziendali in Azure ed eseguire analitica dei big Data.</li>
</ul></td>
</tr>
<tr><td>Dischi di Azure
</td>
<td>Fornisce librerie client e un <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> interfaccia che consente di archiviare e accedere da un disco rigido virtuale collegato in modo permanente i dati.
</td>
<td><ul>
<li>Lift- and -shift le applicazioni che usano le API del sistema di file nativo per leggere e scrivere dati in dischi persistenti.</li>
<li>Store dei dati che non deve necessariamente essere accessibili dall'esterno la macchina virtuale a cui è collegato il disco.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure attivo (online) e archiviazione offline sicura (archivio)

Il tipo di archiviazione per un dato sistema dipende dai requisiti del sistema, incluse le dimensioni di archiviazione, la velocità effettiva e IOPS. Per l'archiviazione DAS-type su un mainframe, applicazioni in Azure in genere utilizzare archiviazione di unità di dischi di Azure. Per spazio di archiviazione di mainframe, archiviazione blob viene usata in Azure.

Le unità SSD offrono le migliori prestazioni di archiviazione in Azure. Le opzioni seguenti sono disponibili (al momento della stesura di questo documento):

| Type         | Dimensione           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB a 64 TB  | 1.200 a 160,000 IOPS |
| SSD Premium  | 32 GB a 32 TB | IOPS di 12 a 15.000     |
| SSD Standard | 32 GB a 32 TB | IOPS di 12 a 2.000      |

Archiviazione BLOB offre il massimo volume di archiviazione in Azure. Oltre alle dimensioni di archiviazione, Azure offre un'archiviazione gestita e non gestita. Con archiviazione gestita, Azure si occupa di gestire gli account di archiviazione sottostante. Con l'archiviazione non gestita, l'utente si assume la responsabilità per la configurazione di account di archiviazione di Azure di dimensione appropriata soddisfare i requisiti di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione dei mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting in macchine virtuali di Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Spostare il calcolo di mainframe in Azure](mainframe-compute-Azure.md)
- [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [SSD Managed Disks standard per i carichi di lavoro di macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Risorse di IBM

- [Sysplex parallele in IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e la funzionalità di controllo libero: Oltre le nozioni di base](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creazione di utenti necessari per un'installazione della funzionalità di pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - istanza comando Create](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale Clustered soluzione di Database](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud di Microsoft Azure per enti pubblici per le applicazioni di mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse per la migrazione

- [Alliance modernizzazione di piattaforma: IBM Db2 in Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Data Center virtuale Lift- and -Shift Guida](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
