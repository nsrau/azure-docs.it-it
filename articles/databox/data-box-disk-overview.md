---
title: Panoramica di Microsoft Azure Data Box Disk | Microsoft Docs
description: Viene descritta la soluzione cloud Azure Data Box Disk che consente di trasferire grandi quantità di dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125031"
---
# <a name="what-is-azure-data-box-disk"></a>Che cos'è Azure Data Box Disk?

La soluzione Microsoft Azure Data Box Disk consente di inviare terabyte di dati locali ad Azure in modo rapido, economico e affidabile. Il trasferimento dei dati sicuro è accelerato tramite la spedizione da 1 a 5 dischi SSD (Solid State Drive). Questi dischi da 8 TB crittografati vengono inviati al data center tramite un gestore telefonico di area.

È possibile configurare, connettere e sbloccare rapidamente i dischi tramite il servizio Data Box nel portale di Azure. Copiare i dati su dischi e spedire i dischi ad Azure. Nel data center di Azure i dati vengono caricati automaticamente dalle unità al cloud usando un collegamento di rete privata per il caricamento veloce.

## <a name="use-cases"></a>Casi d'uso

Usare Data Box Disk per il trasferimento di terabyte di dati in scenari con connettività di rete assente o limitata. Lo spostamento dei dati può avvenire una tantum, periodicamente o può essere un trasferimento di dati in blocco iniziale seguito da trasferimenti periodici.

- **Migrazione una tantum**: quando si spostano grandi quantità di dati locali in Azure. Ad esempio, lo spostamento di dati da nastri offline al livello di archiviazione ad accesso sporadico di Azure.
- **Trasferimento incrementale**: quando si esegue un trasferimento in blocco iniziale usando Data Box Disk (valore di inizializzazione) seguito da trasferimenti incrementali sulla rete. Ad esempio si usano Commvault e Data Box Disk per spostare copie di backup in Azure. Questa migrazione è seguita dalla copia incrementale dei dati tramite rete nell'archiviazione di Azure.
- **Caricamenti periodici**: quando vengono generate periodicamente grandi quantità di dati che devono essere spostate in Azure. Ad esempio nella prospezione di fonti energetiche, in cui viene generato contenuto video negli impianti di trivellazione e nelle installazioni di turbine a vento.

### <a name="ingestion-of-data-from-data-box"></a>Inserimento di dati da Data Box

I provider di Azure e i provider non Azure possono inserire dati da Azure Data Box. I servizi di Azure che forniscono l'inserimento dati da Azure Data Box includono:

- **SharePoint Online** - Usare Azure Data Box e lo Strumento di migrazione di SharePoint per eseguire la migrazione dei contenuti della condivisione file a SharePoint Online. Grazie a Data Box, è possibile rimuovere la dipendenza dal collegamento WAN per il trasferimento dei dati. Per altre informazioni, vedere [Usare Azure Data Box Heavy per eseguire la migrazione del contenuto di una condivisione file in SharePoint Online](data-box-heavy-migrate-spo.md).

- **Sincronizzazione file di Azure** - Consente di eseguire la replica di file da Data Box a una condivisione file di Azure, permettendo di centralizzare i servizi file in Azure, mantenendo al tempo stesso l'accesso locale ai dati. Per altre informazioni, vedere [Distribuire Sincronizzazione file di Azure](../storage/files/storage-sync-files-deployment-guide.md).

- **Archivi HDFS** - Eseguire la migrazione dei dati da un archivio HDFS (Hadoop Distributed File System) locale del cluster Hadoop ad Archiviazione di Azure usando Data Box. Per altre informazioni, vedere [Eseguire la migrazione da un archivio HDFS locale ad archiviazione di Azure con Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Backup di Azure** - Consente di spostare backup di grandi dimensioni di dati aziendali critici tramite meccanismi offline in un insieme di credenziali di Servizi di ripristino di Azure. Per altre informazioni, vedere la [panoramica di Backup di Azure](../backup/backup-overview.md).

È possibile usare i dati di Data Box con molti provider di servizi non Azure. Ad esempio:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** - Consente di eseguire la migrazione di volumi elevati di dati a Microsoft Azure usando Azure Data Box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** - Consente di eseguire il backup e la replica di quantità elevate di dati dal computer Hyper-V in Data Box.

Per un elenco degli altri provider di servizi non Azure che offrono l'integrazione con Data Box, vedere [Partner di Azure Data Box](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Il flusso di lavoro

Il flusso di lavoro tipico è costituito dai passaggi seguenti:

1. **Ordine**: creare un ordine nel portale di Azure, fornire informazioni sulla spedizione e l'account di archiviazione di Azure a cui i dati sono destinati. Se i dischi sono disponibili, Azure esegue la crittografia, poi prepara e spedisce i dischi con un ID di verifica della spedizione.

2. **Ricezione**: dopo che i dischi sono stati recapitati, decomprimere e connettere i dischi al computer che contiene i dati da copiare. Sbloccare i dischi.

3. **Copiare i dati**: trascinare e rilasciare per copiare i dati nei dischi.

4. **Restituire**: preparare e rispedire i dischi al data center di Azure.

5. **Caricare**: i dati vengono automaticamente copiati dai dischi di Azure. I dischi vengono eliminati in modo sicuro secondo le linee guida del National Institute of Standards and Technology (NIST).

Durante questo processo si riceve una notifica tramite posta elettronica a ogni cambiamento di stato. Per informazioni dettagliate sul flusso, passare a [Deploy Data Box Disks in Azure portal](data-box-disk-quickstart-portal.md) (Distribuire dischi Data Box nel portale di Azure).

## <a name="benefits"></a>Vantaggi

Data Box Disk è progettato per spostare grandi quantità di dati in Azure senza alcun impatto sulla rete. La soluzione offre i vantaggi seguenti:

- **Velocità**: Data Box Disk usa una connessione USB 3.0 per spostare un massimo di 35 TB di dati in Azure in meno di una settimana.

- **Facile da usare**: Data Box è una soluzione facile da usare.

  - I dischi usano la connettività USB praticamente senza richiedere configurazione.
  - I dischi hanno un fattore di forma piccolo che li rende più semplici da gestire.
  - I dischi non hanno requisiti di alimentazione esterna.
  - I dischi possono essere utilizzati con un server, un desktop o un laptop di data center.
  - La soluzione offre tracciabilità end-to-end tramite il portale di Azure.

- **Sicurezza**: Data Box Disk è dotato di sicurezza integrata per i dischi, i dati e il servizio.
  - I dischi sono resistenti alle manomissioni e supportano funzionalità di aggiornamento sicure.
  - I dati sui dischi sono protetti costantemente con crittografia AES a 128 bit.
  - I dischi possono essere sbloccati solo con una chiave fornita nel portale di Azure.
  - Il servizio è protetto dalle funzioni di sicurezza di Azure.
  - Dopo che i dati sono stati caricati in Azure, i dischi vengono cancellati, in conformità con gli standard NIST 800-88r1.  

Per altre informazioni, vedere [Azure Data Box Disk security and data protection](data-box-disk-security.md) (Sicurezza e protezione dei dati di Azure Data Box Disk).

## <a name="features-and-specifications"></a>Funzionalità e specifiche

| Specifiche                                          | Descrizione              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | < 0,9 kg (2 libbre) per box. Fino a 5 dischi nel box                |
| Dimensioni                                              | Disco - Unità SSD da 2,5" |
| Cavi                                                  | 1 cavo USB 3.1 per disco|
| Capacità di archiviazione per ordine                              | 40 TB (circa 35 TB utilizzabili)|
| Capacità di archiviazione su disco                                   | 8 TB (circa 7 TB utilizzabili)|
| Interfaccia dati                                          | USB   |
| Sicurezza                                                | Pre-crittografia con BitLocker e aggiornamento sicuro <br> Dischi protetti con passkey <br> Dati crittografati costantemente  |
| Velocità di trasferimento dei dati                                      | fino a 430 MBps a seconda della dimensione del file      |
|Gestione                                               | Portale di Azure |

## <a name="region-availability"></a>Aree di disponibilità

Per informazioni sulla disponibilità a livello di area, vedere [Prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). È possibile distribuire Data Box Disk anche nel cloud di Azure per enti pubblici. Per altre informazioni, vedere [What is Azure Government?](../azure-government/documentation-government-welcome.md) (Informazioni su Azure per enti pubblici).

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Passaggi successivi

- Rivedere i [requisiti per Data Box Disk](data-box-disk-system-requirements.md).
- Comprendere i [limiti di Data Box Disk](data-box-disk-limits.md).
- Distribuire rapidamente [Azure Data Box Disk](data-box-disk-quickstart-portal.md) nel portale di Azure.