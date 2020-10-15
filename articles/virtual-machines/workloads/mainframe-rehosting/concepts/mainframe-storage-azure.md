---
title: Spostare l'archiviazione mainframe nell'archiviazione di Azure
description: Le risorse di archiviazione di Azure estremamente scalabili possono aiutare le organizzazioni basate su mainframe a migrare e modernizzare le applicazioni IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 9a3a7cad8399146834eed0282e0655a551bc083a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977273"
---
# <a name="move-mainframe-storage-to-azure"></a>Spostare l'archiviazione mainframe in Azure

Per eseguire carichi di lavoro mainframe in Microsoft Azure, è necessario capire come vengono confrontate le funzionalità del mainframe con Azure. Le risorse di archiviazione altamente scalabili possono aiutare le organizzazioni ad iniziare a modernizzare senza abbandonare le applicazioni su cui si basano.

Azure fornisce funzionalità di archiviazione e funzionalità di tipo mainframe paragonabili ai sistemi basati su IBM Z14 (il modello più recente al momento della stesura di questo articolo). Questo articolo descrive come ottenere risultati confrontabili in Azure.

## <a name="mainframe-storage-at-a-glance"></a>Archivio mainframe a colpo d'occhio

Il mainframe IBM caratterizza l'archiviazione in due modi. Il primo è un dispositivo di archiviazione con accesso diretto (DASD). Il secondo è l'Archivio sequenziale. Per la gestione dell'archiviazione, il mainframe fornisce il sottosistema di gestione archiviazione di data facilities (DFSMS). Gestisce l'accesso ai dati ai vari dispositivi di archiviazione.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) fa riferimento a un dispositivo separato per l'archiviazione secondaria (non in memoria) che consente l'uso di un indirizzo univoco per l'accesso diretto ai dati. Originariamente, il termine DASD applicato ai dischi rotanti, ai tamburi magnetici o alle celle di dati. Tuttavia, il termine può essere applicato anche a dispositivi SSD (Solid-State Storage), reti San (Storage Area Network), NAS (Network Attached Storage) e unità ottiche. Ai fini di questo documento, DASD fa riferimento a spinning disks, San e SSD.

A differenza dell'archiviazione DASD, l'archiviazione sequenziale in un mainframe si riferisce a dispositivi come le unità nastro in cui è possibile accedere ai dati da un punto di partenza, quindi vengono letti o scritti in una riga.

I dispositivi di archiviazione vengono in genere collegati tramite una connessione Fiber (FICOn) o sono accessibili direttamente sul bus di IO del mainframe usando [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), una tecnologia IBM per le comunicazioni ad alta velocità tra le partizioni in un server con un hypervisor.

La maggior parte dei sistemi mainframe separa lo spazio di archiviazione in due tipi:

- L' *archiviazione online, nota* anche come archiviazione a caldo, è necessaria per le operazioni quotidiane. Per questo scopo, viene in genere usato l'archiviazione DASD. Per questo scopo, è tuttavia possibile utilizzare l'archiviazione sequenziale, ad esempio backup su nastro giornalieri (logici o fisici).

- Non è garantito che l' *archiviazione dell'archivio* (anche nota come archiviazione a freddo) venga montata in un determinato momento. Viene invece montato e accessibile in base alle esigenze. L'archiviazione degli archivi viene spesso implementata mediante backup sequenziali su nastro (logici o fisici) per l'archiviazione.

## <a name="mainframe-versus-io-latency-and-iops"></a>Latenza e IOPS del mainframe rispetto a IO

I mainframe vengono spesso usati per le applicazioni che richiedono un i/o a prestazioni elevate e bassa latenza IO. Questa operazione può essere eseguita usando le connessioni FICOn ai dispositivi IO e HiperSockets. Quando si usano HiperSockets per connettere le applicazioni e i dispositivi direttamente al canale IO di un mainframe, è possibile raggiungere la latenza in microsecondi.

## <a name="azure-storage-at-a-glance"></a>Archiviazione di Azure a colpo d'occhio

Le opzioni di infrastruttura distribuita come servizio ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) di Azure per l'archiviazione forniscono capacità mainframe analoghe.

Microsoft offre petabyte di archiviazione per le applicazioni ospitate in Azure e sono disponibili diverse opzioni di archiviazione. Si tratta di un intervallo di archiviazione SSD per prestazioni elevate ad archiviazione BLOB a basso costo per archiviazione di massa e archivi. Azure fornisce inoltre un'opzione di ridondanza dei dati per l'archiviazione, operazione che richiede più impegno per la configurazione in un ambiente mainframe.

Archiviazione di Azure è disponibile come Azure [disks](../../../managed-disks-overview.md), [file di Azure](../../../../storage/files/storage-files-introduction.md)e [BLOB di Azure](../../../../storage/blobs/storage-blobs-overview.md) , come illustrato nella tabella seguente. Altre informazioni su [quando usare ogni](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Descrizione</th><th>Usare quando si desidera:</th></tr>
</thead>
<tbody>
<tr><td>File di Azure
</td>
<td>
Fornisce un'interfaccia SMB, librerie client e un'interfaccia <a href="/rest/api/storageservices/file-service-rest-api">Rest</a> che consente l'accesso da qualsiasi posizione ai file archiviati.
</td>
<td><ul>
<li>È possibile spostare un'applicazione nel cloud quando l'applicazione usa le API file system native per condividere i dati tra l'applicazione e altre applicazioni in esecuzione in Azure.</li>
<li>Archiviare gli strumenti di sviluppo e debug a cui è necessario accedere da molte macchine virtuali.</li>
</ul>
</td>
</tr>
<tr><td>BLOB di Azure
</td>
<td>Fornisce librerie client e un'interfaccia <a href="/rest/api/storageservices/blob-service-rest-api">Rest</a> che consente di archiviare e accedere ai dati non strutturati su vasta scala nei BLOB in blocchi. Supporta anche <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> per le soluzioni aziendali di analisi di Big Data.
</td>
<td><ul>
<li>Supportare scenari di streaming e accesso casuale in un'applicazione.</li>
<li>Hanno accesso ai dati dell'applicazione ovunque ci si trovi.</li>
<li>Creazione di un data Lake aziendale in Azure ed esecuzione di analisi Big Data.</li>
</ul></td>
</tr>
<tr><td>Dischi di Azure
</td>
<td>Fornisce librerie client e un'interfaccia <a href="/rest/api/compute/disks">Rest</a> che consente di archiviare e accedere in modo permanente ai dati da un disco rigido virtuale collegato.
</td>
<td><ul>
<li>Applicazioni Lift-and-Shift che usano API file system native per leggere e scrivere dati in dischi permanenti.</li>
<li>Archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure Hot (online) e archiviazione a freddo (Archivio)

Il tipo di archiviazione per un determinato sistema dipende dai requisiti del sistema, tra cui le dimensioni di archiviazione, la velocità effettiva e IOPS. Per l'archiviazione di tipo DASD in un mainframe, le applicazioni in Azure usano in genere l'archiviazione unità dischi di Azure. Per l'archiviazione dell'archivio mainframe, l'archiviazione BLOB viene usata in Azure.

Le unità SSD forniscono le massime prestazioni di archiviazione in Azure. Al momento della stesura di questo documento sono disponibili le opzioni seguenti:

| Type         | Dimensione           | Operazioni di I/O al secondo                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | da 4 GB a 64 TB  | da 1.200 a 160.000 IOPS |
| SSD Premium  | da 32 GB a 32 TB | da 12 a 15.000 IOPS     |
| SSD Standard | da 32 GB a 32 TB | da 12 a 2.000 IOPS      |

Archiviazione BLOB fornisce il maggior volume di archiviazione in Azure. Oltre alle dimensioni di archiviazione, Azure offre archiviazione gestita e non gestita. Con l'archiviazione gestita, Azure gestisce gli account di archiviazione sottostanti. Con l'archiviazione non gestita, l'utente si assume la responsabilità di configurare gli account di archiviazione di Azure della dimensione appropriata per soddisfare i requisiti di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Riallocazione del mainframe in macchine virtuali di Azure](../overview.md)
- [Spostare il calcolo mainframe in Azure](mainframe-compute-Azure.md)
- [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](../../../../storage/common/storage-introduction.md)
- [SDD Standard Managed Disks per i carichi di lavoro delle macchine virtuali di Azure](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>Risorse IBM

- [Sysplex parallelo in IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [CICS IBM e la funzionalità di accoppiamento: oltre le nozioni di base](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2) (Creazione degli utenti necessari per l'installazione della funzionalità Db2 pureScale)
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html) (Db2icrt - Comando Crea istanza)
- [Soluzione di database del cluster DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure per enti pubblici cloud per applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse di migrazione

- [Data center virtuale di Azure: guida al lift and shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS)