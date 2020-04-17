---
title: Configurazione dell'archiviazione per le VM di SQL Server | Documentazione Microsoft
description: Questo argomento descrive come viene configurata l'archiviazione da Azure per le VM di SQL Server durante il provisioning (modello di distribuzione di Resource Manager). Viene inoltre spiegato come è possibile configurare l'archiviazione per le VM di SQL Server esistenti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482147"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configurazione dell'archiviazione per le VM di SQL Server

Quando si configura un'immagine di macchina virtuale di SQL Server in Azure, il portale consente di automatizzare la configurazione dell'archiviazione, ovvero collegare l'archiviazione alla VM, renderla disponibile per SQL Server e ottimizzarla in base alle specifiche esigenze a livello di prestazioni.

Questo argomento illustra come viene configurata l'archiviazione da Azure per le VM di SQL Server, sia durante il provisioning che per le VM esistenti. Questa configurazione è basata sulle [procedure consigliate per le prestazioni](virtual-machines-windows-sql-performance.md) per le VM virtuali di Azure che eseguono SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prerequisiti

Per usare le impostazioni di configurazione automatica dell'archiviazione, la macchina virtuale deve avere le caratteristiche seguenti:

* Provisioning eseguito con un' [immagine della raccolta di SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Uso del [modello di distribuzione Azure Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Uso di [unità SSD Premium](../disks-types.md).

## <a name="new-vms"></a>Nuove VM

Le sezioni seguenti descrivono come configurare l'archiviazione per le nuove macchine virtuali di SQL Server.

### <a name="azure-portal"></a>Portale di Azure

Quando si esegue il provisioning di una macchina virtuale di Azure usando un'immagine della raccolta di SQL Server, selezionare **Modifica configurazione** nella scheda Impostazioni di **SQL Server** per aprire la pagina Configurazione archiviazione ottimizzata per le prestazioni. È possibile lasciare i valori predefiniti o modificare il tipo di configurazione del disco più adatto alle proprie esigenze in base al carico di lavoro. 

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selezionare il tipo di carico di lavoro da distribuire per in **Ottimizzazione archiviazione**. Con l'opzione Ottimizzazione **generale,** per impostazione predefinita si dirà un disco dati con 5000 operazioni di I/O al secondo massime e si utilizzerà la stessa unità per i dati, il log delle transazioni e l'archiviazione TempDB. Se si seleziona **Elaborazione transazionale** (OLTP) o **Data warehousing,** verrà creato un disco separato per i dati, un disco separato per il log delle transazioni e verrà utilizzato SSD locale per TempDB. Non esistono differenze di archiviazione tra **L'elaborazione transazionale** e **Data warehousing**, ma modifica la configurazione dello [stripe e](#workload-optimization-settings)i flag di traccia. La scelta dell'archiviazione Premium imposta la memorizzazione nella cache su *ReadOnly* per l'unità dati e *Su None* per l'unità di log in base alle procedure consigliate per le [prestazioni della macchina virtuale](virtual-machines-windows-sql-performance.md)di SQL Server . 

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

La configurazione del disco è completamente personalizzabile in modo da poter configurare la topologia di archiviazione, il tipo di disco e le operazioni di I/O al secondo necessarie per il carico di lavoro della macchina virtuale di SQL Server.The disk configuration is completely customizable so that you can configure the storage topology, disk type and IOPs you need for your SQL Server VM workload. È inoltre possibile utilizzare UltraSSD (anteprima) come opzione per il tipo di **disco** se la macchina virtuale di SQL Server si trova in una delle aree supportate (Stati Uniti orientali 2, Asia sudorientale e Nord Europa) e sono stati abilitati [dischi ultra per l'abbonamento](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Inoltre, è possibile impostare la memorizzazione nella cache per i dischi. Le macchine virtuali di Azure dispongono di una tecnologia di memorizzazione nella cache a più livelli denominata [Cache BLOB](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) quando viene usata con [i dischi Premium.](/azure/virtual-machines/windows/disks-types#premium-ssd) La cache BLOB usa una combinazione della RAM della macchina virtuale e dell'unità SSD locale per la memorizzazione nella cache. 

La memorizzazione nella cache del disco per SSD Premium può essere *ReadOnly*, *ReadWrite* o *None*. 

- La memorizzazione nella cache ReadOnly è estremamente vantaggiosa per i file di dati di SQL Server archiviati in Archiviazione *Premium.ReadOnly* caching is highly beneficial for SQL Server data files that are stored on Premium Storage. La memorizzazione nella cache *ReadOnly* comporta una bassa latenza di lettura, un'elevata velocità di I/O al secondo di lettura e velocità effettiva in quanto le letture vengono eseguite dalla cache, che si trova all'interno della memoria della macchina virtuale e dell'Unità SSD locale. Queste letture sono molto più veloci rispetto alle letture dal disco dati, ovvero dall'archiviazione BLOB di Azure.These reads are much faster than reads from data disk, which is from the Azure blob storage. L'archiviazione Premium non conta le letture servite dalla cache verso le operazioni di I/O al secondo e la velocità effettiva del disco. Pertanto, l'applicabile è in grado di ottenere operazioni di I/O al secondo e velocità effettiva totali più elevate. 
- *Nessuna* configurazione della cache deve essere utilizzata per i dischi che ospitano il file di log di SQL Server come file di log viene scritto in sequenza e non trae vantaggio dalla memorizzazione nella cache *ReadOnly.* 
- La memorizzazione nella cache *ReadWrite* non deve essere utilizzata per ospitare i file di SQL Server poiché SQL Server non supporta la coerenza dei dati con la cache *ReadWrite.* Scrive la capacità di spreco della cache BLOB *ReadOnly* e le latenze aumentano leggermente se le scritture passano attraverso i livelli di cache BLOB *ReadOnly.* 


   > [!TIP]
   > Assicurarsi che la configurazione di archiviazione corrisponda alle limitazioni imposte dalle dimensioni della macchina virtuale selezionata. La scelta di parametri di archiviazione che superano `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`il limite di prestazioni della dimensione della macchina virtuale genererà un errore: . Ridurre le operazioni di I/O al secondo modificando il tipo di disco oppure aumentare la limitazione del limite delle prestazioni aumentando le dimensioni della macchina virtuale. 


A seconda delle scelte effettuate, Azure esegue le seguenti attività di configurazione dell'archiviazione dopo la creazione della VM:

* Crea e collega le unità SSD Premium alla macchina virtuale.
* Configura i dischi dati in modo che siano accessibili per SQL Server.
* Configura i dischi dati in un pool di archiviazione in base ai requisiti specificati per dimensioni e prestazioni (operazioni di I/O al secondo e velocità effettiva).
* Associa il pool di archiviazione a una nuova unità nella macchina virtuale.
* Ottimizza la nuova unità in base al tipo di carico di lavoro specificato (data warehousing, elaborazione transazionale o generale).

Per ulteriori dettagli su come vengono configurate le impostazioni dell'archiviazione da Azure, vedere la [sezione Configurazione dell'archiviazione](#storage-configuration). Per una procedura dettagliata completa su come creare una macchina virtuale di SQL Server nel portale di Azure, vedere [l'esercitazione](virtual-machines-windows-portal-sql-server-provision.md)sul provisioning.

### <a name="resource-manage-templates"></a>Modelli di Resource Manager

Se si usano i modelli di Resource Manager seguenti, vengono collegati per impostazione predefinita due dischi dati Premium, senza configurare un pool di archiviazione. È comunque possibile personalizzare questi modelli per modificare il numero di dischi di dati Premium collegati alla macchina virtuale.

* [Creare una VM con il backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Creare una VM con l'applicazione automatica delle patch](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Creare una VM con l'integrazione di AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modello di avvio rapido

È possibile usare il modello di avvio rapido seguente per distribuire una macchina virtuale di SQL Server usando l'ottimizzazione dell'archiviazione. 

* [Creare una macchina virtuale con ottimizzazione dell'archiviazioneCreate VM with storage optimization](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Creare una macchina virtuale con UltraSSDCreate VM using UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le VM di SQL Server esistenti, è possibile modificare alcune impostazioni di archiviazione nel portale di Azure. Aprire la [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)e selezionare **Panoramica**. La pagina Panoramica di SQL Server mostra l'utilizzo corrente dello spazio di archiviazione della macchina virtuale. In questo grafico vengono visualizzate tutte le unità esistenti nella VM. Per ogni unità, lo spazio di archiviazione viene visualizzato in quattro sezioni:

* SQL data
* Log SQL
* Altro (archiviazione non SQL)
* Disponibile

Per modificare le impostazioni di archiviazione, selezionare **Configura** in **Impostazioni**. 

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

È possibile modificare le impostazioni del disco per le unità configurate durante il processo di creazione della macchina virtuale di SQL Server.You can modify the disk settings for the drives that were configured during the SQL Server VM creation process. Selezionando **Estendi unità** si apre la pagina di modifica dell'unità, che consente di modificare il tipo di disco e di aggiungere altri dischi. 

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configurazione dell'archiviazione

Questa sezione fornisce un riferimento per le modifiche alla configurazione di archiviazione che Azure esegue automaticamente durante il provisioning o la configurazione della macchina virtuale SQL nel portale di Azure.This section provides a reference for the storage configuration changes that Azure automatically performs during SQL VM provisioning or configuration in the Azure portal.

* Azure configura un pool di archiviazione dall'archiviazione selezionata dalla macchina virtuale. Nella sezione successiva di questo argomento vengono fornite informazioni dettagliate sulla configurazione del pool di archiviazione.
* Per la configurazione automatica dell'archiviazione vengono sempre usati dischi dati P30 [SSD Premium](../disks-types.md). Esiste quindi una corrispondenza 1:1 tra il numero selezionato di terabyte e il numero di dischi dati collegati alla VM.

Per informazioni sui prezzi, vedere la pagina [Prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage) nella scheda **Archiviazione su disco** .

### <a name="creation-of-the-storage-pool"></a>Creazione del pool di archiviazione

Azure usa le impostazioni seguenti per creare il pool di archiviazione nelle VM di SQL.

| Impostazione | valore |
| --- | --- |
| Dimensioni di striping |256 KB (data warehousing); 64 KB (transazionale) |
| Dimensione disco |1 TB ciascuno |
| Cache |Lettura |
| Dimensioni allocazione |Dimensioni delle unità di allocazione NTFS = 64 KB |
| Ripristino | Recupero con registrazione minima (nessuna resilienza) |
| Numero di colonne |Numero di dischi dati fino a 8<sup>1</sup> |


<sup>1</sup> Dopo aver creato il pool di archiviazione non è possibile modificare il numero di colonne nel pool.


## <a name="workload-optimization-settings"></a>Impostazioni di ottimizzazione del carico di lavoro

La tabella seguente descrive le tre opzioni disponibili per il tipo di carico di lavoro e le ottimizzazioni corrispondenti:

| Tipo di carico di lavoro | Descrizione | Ottimizzazioni |
| --- | --- | --- |
| **Generale** |Impostazione predefinita che supporta la maggior parte dei carichi di lavoro |nessuno |
| **Elaborazione transazionale** |Ottimizza l'archiviazione per carichi di lavoro OLTP di database tradizionali |Flag di traccia 1117<br/>Flag di traccia 1118 |
| **Data warehousing** |Ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report |Flag di traccia 610<br/>Flag di traccia 1117 |

> [!NOTE]
> È possibile specificare il tipo di carico di lavoro solo quando si esegue il provisioning di una macchina virtuale di SQL Serer, selezionandolo nel passaggio di configurazione dell'archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
