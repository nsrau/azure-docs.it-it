---
title: Eseguire la migrazione da AWS e altre piattaforme a Managed Disks in Azure | Documentazione Microsoft
description: Creare macchine virtuali in Azure usando dischi rigidi virtuali caricati da altri cloud, come AWS o altre piattaforme di virtualizzazione, e sfruttare i vantaggi di Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dce319c055f7988355cfadbc1d63df5e268e53
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Eseguire la migrazione da Amazon Web Services (AWS) e altre piattaforme a Managed Disks in Azure

È possibile caricare i file di disco rigido virtuale da soluzioni di virtualizzazione locali o AWS in Azure per creare macchine virtuali che sfruttino i vantaggi di Managed Disks. Azure Managed Disks elimina la necessità di gestire gli account di archiviazione per le macchine virtuali IaaS di Azure. È necessario specificare solo il tipo (Premium o Standard) e le dimensioni necessarie del disco, poiché sarà Azure a creare e gestire il disco automaticamente. 

È possibile caricare dischi rigidi virtuali generalizzati e specializzati. 
- **Disco rigido virtuale generalizzato**: tutte le informazioni dell'account personale sono state rimosse tramite Sysprep. 
- **Disco rigido virtuale specializzato**: gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. 

> [!IMPORTANT]
> Prima di caricare dischi rigidi virtuali in Azure, è necessario seguire la procedura in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Documentazione                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Si ha un'istanza EC2 di AWS esistente di cui si vuole eseguire la migrazione alle VM di usando dischi gestiti                              | [Spostare una VM Windows da Amazon Web Services (AWS) ad Azure](aws-to-azure.md)                           |
| Si ha una VM di un'altra piattaforma di virtualizzazione da usare come immagine per creare più macchine virtuali di Azure. | [Caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova macchina virtuale in Azure](upload-generalized-managed.md) |
| Si dispone di una macchina virtuale personalizzata in modo univoco che si desidera ricreare in Azure.                                                      | [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Panoramica di Managed Disks

Il servizio Azure Managed Disks semplifica la gestione delle macchine virtuali, eliminando la necessità di gestire gli account di archiviazione. Managed Disks trae inoltre vantaggio dalla maggiore affidabilità delle macchine virtuali in un set di disponibilità. Assicura che i dischi di macchine virtuali diverse in un set di disponibilità siano sufficientemente isolati tra loro per evitare un singolo punto di guasto. Colloca automaticamente i dischi di macchine virtuali differenti in un set di disponibilità in diverse unità di scala di archiviazione (indicatori) per limitare l'impatto degli errori di una singola unità causati da anomalie hardware e software. In base alle specifiche esigenze, è possibile scegliere tra due tipi di opzioni di archiviazione: 
 
- [Managed Disks Premium](premium-storage.md) prevede unità SSD basate su supporti di archiviazione che assicurano prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con elevato numero di operazioni di I/O. Per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi, è possibile migrare a Managed Disks Premium.  

- [Managed Disks Standard](standard-storage.md) usa supporti di memorizzazione basati su unità disco rigido (HDD) ed è ideale per le operazioni di sviluppo/test e per altri carichi di lavoro ad accesso sporadico, meno sensibili alla variabilità delle prestazioni.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Pianificare la migrazione a Managed Disks

Questa sezione consente di prendere le decisioni migliori sui tipi di macchina virtuale e disco.


### <a name="location"></a>Località

Selezionare una posizione in cui Azure Managed Disks è disponibile. Se si esegue la migrazione a Managed Disks Premium, assicurarsi anche che l'Archiviazione Premium sia disponibile nell'area di destinazione della migrazione. Per informazioni aggiornate sulle località disponibili, vedere [Prodotti in base all'area](https://azure.microsoft.com/regions/#services) .

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali

Se si sta eseguendo la migrazione a Managed Disks Premium, sarà necessario aggiornare le dimensioni della macchina virtuale alle dimensioni di Archiviazione Premium disponibili nell'area in cui si trova la VM. Esaminare le dimensioni delle macchine virtuali in grado di supportare Archiviazione Premium. Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](sizes.md).
Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

### <a name="disk-sizes"></a>Dimensione disco

**Managed Disks Premium**

È possibile usare tre tipi di dischi gestiti della versione Premium con la macchina virtuale, ciascuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco Premium per la macchina virtuale in base alle esigenze dell'applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

| Tipo di disco Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Dimensioni disco           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| IOPS per disco       | 500               | 2300              | 5000              |
| Velocità effettiva per disco | 100 MB al secondo | 150 MB al secondo | 200 MB al secondo |

**Managed Disks Standard**

Esistono cinque tipi di dischi gestiti della versione Standard che possono essere usati con la macchina virtuale. Si differenziano per capacità ma presentano gli stessi limiti IOP e di velocità effettiva. Scegliere il tipo di disco gestito della versione Standard in base alle esigenze in termini di capacità dell'applicazione.

| Tipo di disco Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Dimensioni disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| IOPS per disco       | 500              | 500              | 500              | 500              | 500              |
| Velocità effettiva per disco | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo |

### <a name="disk-caching-policy"></a>Criteri di memorizzazione nella cache su disco 

**Managed Disks Premium**

Per impostazione predefinita, il criterio di memorizzazione nella cache su disco è impostato su *Sola lettura* per tutti i dischi di dati Premium e su *Lettura/scrittura* per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.

### <a name="pricing"></a>Prezzi

Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti. Tuttavia, il prezzo della versione Standard dei dischi gestiti è diverso da quello della versione Standard dei dischi non gestiti.


## <a name="next-steps"></a>Fasi successive

- Prima di caricare dischi rigidi virtuali in Azure, è necessario seguire la procedura in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
