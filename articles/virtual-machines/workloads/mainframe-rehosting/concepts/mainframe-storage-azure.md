---
title: Spostare l'archiviazione mainframe in Archiviazione di AzureMove mainframe storage to Azure Storage
description: Le risorse di archiviazione di Azure massicciamente scalabili possono aiutare le organizzazioni basate su mainframe a migrare e modernizzare le applicazioni IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288915"
---
# <a name="move-mainframe-storage-to-azure"></a>Spostare l'archiviazione mainframe in AzureMove mainframe storage to Azure

To run mainframe workloads on Microsoft Azure, you need to know how your mainframe's capabilities compare to Azure. Le risorse di archiviazione massicciamente scalabili possono aiutare le organizzazioni a iniziare a modernizzare senza abbandonare le applicazioni su cui si basano.

Azure offre funzionalità simili a mainframe e capacità di archiviazione paragonabile ai sistemi basati su IBM z14 (il modello più recente a partire da questa scrittura). Questo articolo illustra come ottenere risultati comparabili in Azure.This article tells you how to get comparable results on Azure.

## <a name="mainframe-storage-at-a-glance"></a>Archiviazione mainframe a colpo d'occhio

Il mainframe IBM caratterizza l'archiviazione in due modi. Il primo è un dispositivo di archiviazione ad accesso diretto (DASD). Il secondo è l'archiviazione sequenziale. Per la gestione dell'archiviazione, il mainframe fornisce il sottosistema Di Gestione archiviazione data facility (DFSMS). Gestisce l'accesso ai dati ai vari dispositivi di archiviazione.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) si riferisce a un dispositivo separato per l'archiviazione secondaria (non in memoria) che consente di utilizzare un indirizzo univoco per l'accesso diretto ai dati. Originariamente, il termine DASD applicato a dischi rotanti, tamburi magnetici o celle di dati. Tuttavia, ora il termine può essere applicato anche ai dispositivi di archiviazione a stato solido (SSD), alle reti SAN (Storage Area Network), ai NAS (Network Attached Storage) e alle unità ottiche. Ai fini del presente documento, DASD si riferisce a dischi rotanti, SAN e Unità SSD.

A differenza dell'archiviazione DASD, l'archiviazione sequenziale su un mainframe si riferisce a dispositivi come le unità nastro in cui si accede ai dati da un punto di partenza, quindi leggere o scrivere in una riga.

I dispositivi di archiviazione sono in genere collegati utilizzando una connessione in fibra (FICON) o sono accessibili direttamente sul bus I/O del mainframe utilizzando [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), una tecnologia IBM per le comunicazioni ad alta velocità tra partizioni su un server con un hypervisor.

La maggior parte dei sistemi mainframe separa l'archiviazione in due tipi:

- *Lo storage online* (noto anche come hot storage) è necessario per le operazioni quotidiane. L'archiviazione DASD viene in genere utilizzata a questo scopo. Tuttavia, l'archiviazione sequenziale, ad esempio i backup su nastro giornalieri (logici o fisici), può essere utilizzata anche a questo scopo.

- *Non* è garantito che l'archiviazione di archivio (nota anche come cold storage) venga montata in un determinato momento. Al contrario, viene montato e vi si accede in base alle esigenze. L'archiviazione di archiviazione viene spesso implementata tramite backup su nastro sequenziali (logici o fisici) per l'archiviazione.

## <a name="mainframe-versus-io-latency-and-iops"></a>Confronto tra frame e latenza di I/O e operazioni di I/OMainframe vs IO latency and IO

I mainframe vengono spesso utilizzati per le applicazioni che richiedono operazioni di I/O ad alte prestazioni e bassa latenza di I/O.Mainframes are often used for applications that require high iO and low IO latency. Possono farlo utilizzando le connessioni FICON ai dispositivi di I/O e HiperSockets. Quando HiperSockets viene utilizzato per connettere applicazioni e dispositivi direttamente al canale di I/O di un mainframe, è possibile raggiungere la latenza nei microsecondi.

## <a name="azure-storage-at-a-glance"></a>Archiviazione di Azure a colpo d'occhioAzure storage at a glance

Le opzioni di Azure infrastructure-as-a-service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) per l'archiviazione offrono una capacità mainframe paragonabile.

Microsoft offre petabyte di spazio di archiviazione per le applicazioni ospitate in Azure e sono disponibili diverse opzioni di archiviazione. Questi spaziano dall'archiviazione SSD per prestazioni elevate all'archiviazione BLOB a basso costo per l'archiviazione di massa e gli archivi. Inoltre, Azure offre un'opzione di ridondanza dei dati per l'archiviazione, operazione che richiede più impegno per la configurazione in un ambiente mainframe.

Archiviazione di Azure è disponibile come [dischi](/azure/virtual-machines/windows/managed-disks-overview)di Azure , File di [Azure](/azure/storage/files/storage-files-introduction)e BLOB di [Azure,](/azure/storage/blobs/storage-blobs-overview) come riepiloga la tabella seguente. Ulteriori informazioni su [quando utilizzare ogni](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)file .

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Descrizione</th><th>Utilizzare quando si desidera:</th></tr>
</thead>
<tbody>
<tr><td>File di Azure
</td>
<td>
Fornisce un'interfaccia SMB, librerie client e un'interfaccia <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> che consente l'accesso da qualsiasi luogo ai file archiviati.
</td>
<td><ul>
<li>Sollevare e spostare un'applicazione nel cloud quando l'applicazione usa le API del file system nativo per condividere i dati tra l'applicazione e altre applicazioni in esecuzione in Azure.Lift and shift an application to the cloud when the application uses the native file system APIs to share data between it and other applications running in Azure.</li>
<li>Strumenti di sviluppo e debug dell'archiviazione a cui è necessario accedere da molte macchine virtuali.</li>
</ul>
</td>
</tr>
<tr><td>BLOB di Azure
</td>
<td>Fornisce librerie client e un'interfaccia <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> che consente l'archiviazione e l'accesso ai dati non strutturati su larga scala in BLOB in blocchi. Supporta anche <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> per le soluzioni aziendali di analisi di Big Data.
</td>
<td><ul>
<li>Supporta scenari di streaming e accesso casuale in un'applicazione.</li>
<li>Avere accesso ai dati dell'applicazione da qualsiasi luogo.</li>
<li>Crea un data lake aziendale in Azure ed esegui l'analisi dei Big Data.</li>
</ul></td>
</tr>
<tr><td>Dischi di Azure
</td>
<td>Fornisce librerie client e un'interfaccia <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> che consente di archiviare i dati in modo permanente e di accedervi da un disco rigido virtuale collegato.
</td>
<td><ul>
<li>Sollevare e spostare le applicazioni che utilizzano le API native del file system per leggere e scrivere dati su dischi persistenti.</li>
<li>Archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Archiviazione a caldo (online) e a freddo (archivio) di AzureAzure hot (online) and cold (archive) storage

Il tipo di archiviazione per un determinato sistema dipende dai requisiti del sistema, incluse le dimensioni dell'archiviazione, la velocità effettiva e le operazioni di I/O al secondo. Per l'archiviazione di tipo DASD in un mainframe, le applicazioni in Azure usano in genere l'archiviazione unità di dischi di Azure.For DASD-type storage on a mainframe, applications on Azure typically use Azure Disks drive storage instead. Per l'archiviazione di archiviazione mainframe, l'archiviazione BLOB viene usata in Azure.For mainframe archive storage, blob storage is used on Azure.

Gli SSD offrono le massime prestazioni di archiviazione in Azure.SSDs provide the highest storage performance on Azure. Sono disponibili le seguenti opzioni (al set di scrittura di questo documento):

| Type         | Dimensione           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | Da 4 GB a 64 TB  | Da 1.200 a 160.000 IOPS |
| SSD Premium  | Da 32 GB a 32 TB | Da 12 a 15.000 IOPS     |
| SSD Standard | Da 32 GB a 32 TB | Da 12 a 2.000 IOPS      |

Blob storage provides the largest volume of storage on Azure. Oltre alle dimensioni di archiviazione, Azure offre archiviazione gestita e non gestita. Con l'archiviazione gestita, Azure si occupa della gestione degli account di archiviazione sottostanti. Con l'archiviazione non gestita, l'utente si assume la responsabilità della configurazione degli account di archiviazione di Azure delle dimensioni appropriate per soddisfare i requisiti di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosting del frame principale nelle macchine virtuali di AzureMainframe rehosting on Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Spostare il calcolo del mainframe in AzureMove mainframe compute to Azure](mainframe-compute-Azure.md)
- [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Dischi gestiti SSD standard per carichi di lavoro di macchine virtuali di AzureStandard SSD Managed Disks for Azure VM workloads](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Risorse IBM

- [Sysplex parallelo su IBM](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e lo strumento di accoppiamento: oltre le basi](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2) (Creazione degli utenti necessari per l'installazione della funzionalità Db2 pureScale)
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html) (Db2icrt - Comando Crea istanza)
- [Db2 pureScale Soluzione per il database cluster](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud di Microsoft Azure per enti pubblici per le applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse per la migrazione

- [Data center virtuale di Azure: guida al lift and shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS)
