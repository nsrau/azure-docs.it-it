---
title: Configurazione dell'archiviazione per le VM di SQL Server | Documentazione Microsoft
description: Questo argomento descrive in che modo Azure configura l'archiviazione per le macchine virtuali SQL Server durante il provisioning (Azure Resource Manager modello di distribuzione). Viene inoltre spiegato come è possibile configurare l'archiviazione per le VM di SQL Server esistenti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: ebeee228d8c936732465359dfa264d822cbecb1e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793076"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configurazione dell'archiviazione per le VM di SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Quando si configura un SQL Server immagine di macchina virtuale (VM) in Azure, il portale di Azure consente di automatizzare la configurazione dell'archiviazione. ovvero collegare l'archiviazione alla VM, renderla disponibile per SQL Server e ottimizzarla in base alle specifiche esigenze a livello di prestazioni.

Questo argomento illustra come viene configurata l'archiviazione da Azure per le VM di SQL Server, sia durante il provisioning che per le VM esistenti. Questa configurazione è basata sulle [procedure consigliate per le prestazioni](performance-guidelines-best-practices.md) per le VM virtuali di Azure che eseguono SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prerequisiti

Per usare le impostazioni di configurazione automatica dell'archiviazione, la macchina virtuale deve avere le caratteristiche seguenti:

* Provisioning eseguito con un' [immagine della raccolta di SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).
* Uso del [modello di distribuzione Azure Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Uso di [unità SSD Premium](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nuove VM

Le sezioni seguenti descrivono come configurare l'archiviazione per le nuove macchine virtuali di SQL Server.

### <a name="azure-portal"></a>Portale di Azure

Quando si esegue il provisioning di una VM di Azure usando un'immagine della raccolta di SQL Server, selezionare **Cambia la configurazione** nella scheda **Impostazioni di SQL Server** per aprire la pagina di configurazione dell'archiviazione ottimizzata per le prestazioni. È possibile lasciare i valori predefiniti o modificare il tipo di configurazione del disco più adatto alle proprie esigenze in base al carico di lavoro. 

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selezionare il tipo di carico di lavoro per cui si distribuisce SQL Server in **Ottimizzazione dell'archiviazione** . Con l'opzione di ottimizzazione **Generale** , per impostazione predefinita si otterrà un solo disco dati con max 5000 operazioni di I/O al secondo e si userà la stessa unità per i dati, il log delle transazioni e l'archiviazione TempDB. Se si seleziona **Elaborazione transazionale** o **Data warehousing** , viene creato un disco separato per i dati, un disco separato per il log delle transazioni e viene usata l'unità SSD locale per TempDB. Non esistono differenze in termini di archiviazione tra l' **elaborazione transazionale** e il **data warehousing** , ma cambiano la [configurazione di striping e i flag di traccia](#workload-optimization-settings). Se si sceglie l'archiviazione Premium, la memorizzazione nella cache viene impostata su *ReadOnly* per l'unità dati e su *None* per l'unità log in base alle [procedure consigliate per le prestazioni delle VM di SQL Server](performance-guidelines-best-practices.md). 

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/storage-configuration/sql-vm-storage-configuration.png)

La configurazione del disco è completamente personalizzabile, in modo che sia possibile configurare la topologia di archiviazione, il tipo di disco e le operazioni di I/O al secondo necessari per il carico di lavoro della VM di SQL Server. È anche possibile usare UltraSSD (anteprima) come opzione per il **tipo di disco** se la VM di SQL Server si trova in una delle aree supportate (Stati Uniti orientali 2, Asia sudorientale ed Europa settentrionale) e sono stati abilitati [dischi Ultra per la sottoscrizione](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Inoltre, è possibile impostare la memorizzazione nella cache per i dischi. Le macchine virtuali di Azure hanno una tecnologia di memorizzazione nella cache a più livelli denominata [cache di dati BLOB](../../../virtual-machines/premium-storage-performance.md#disk-caching) se usate con [dischi Premium](../../../virtual-machines/disks-types.md#premium-ssd). La cache di dati BLOB usa una combinazione della RAM della macchina virtuale e dell'unità SSD locale per la memorizzazione nella cache. 

La memorizzazione nella cache del disco per SSD Premium può essere *ReadOnly* , *ReadWrite* o *None* . 

- La memorizzazione nella cache *ReadOnly* è molto utile per i file di dati di SQL Server archiviati in Archiviazione Premium. Il tipo *ReadOnly* implica una bassa latenza di lettura e valori elevati per le operazioni di I/O al secondo e la velocità effettiva di lettura, poiché le letture vengono eseguite dalla cache, che si trova all'interno della memoria della VM e dell'unità SSD locale. Queste letture sono molto più veloci delle letture dal disco dati, ovvero dall'archiviazione BLOB di Azure. L'archiviazione Premium non include le operazioni di lettura servite dalla cache nel calcolo dei valori di operazioni di I/O al secondo e velocità effettiva del disco. Si è quindi in grado di ottenere valori totali di operazioni di I/O al secondo e velocità effettiva più elevati. 
- La configurazione della cache *None* deve essere usata per i dischi che ospitano il file di log di SQL Server, poiché tale file viene scritto in sequenza e non sfrutta la memorizzazione nella cache di tipo *ReadOnly* . 
- La memorizzazione nella cache *ReadWrite* non deve essere usata per ospitare i file di SQL Server perché SQL Server non supporta la coerenza dei dati con la cache *ReadWrite* . Le scritture sprecano la capacità della cache BLOB *ReadOnly* e le latenze aumentano leggermente se le scritture passano attraverso i livelli della cache BLOB *ReadOnly* . 


   > [!TIP]
   > Assicurarsi che la configurazione dell'archiviazione corrisponda alle limitazioni imposte dalle dimensioni della macchina virtuale selezionata. Se si scelgono parametri di archiviazione che superano il limite massimo delle dimensioni della macchina virtuale per le prestazioni, verrà generato un errore: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Ridurre le operazioni di I/O al secondo cambiando il tipo di disco o aumentare il limite massimo per le prestazioni aumentando le dimensioni della macchina virtuale. 


A seconda delle scelte effettuate, Azure esegue le seguenti attività di configurazione dell'archiviazione dopo la creazione della VM:

* Crea e collega le unità SSD Premium alla macchina virtuale.
* Configura i dischi dati in modo che siano accessibili per SQL Server.
* Configura i dischi dati in un pool di archiviazione in base ai requisiti specificati per dimensioni e prestazioni (operazioni di I/O al secondo e velocità effettiva).
* Associa il pool di archiviazione a una nuova unità nella macchina virtuale.
* Ottimizza la nuova unità in base al tipo di carico di lavoro specificato (data warehousing, elaborazione transazionale o generale).

Per ulteriori dettagli su come vengono configurate le impostazioni dell'archiviazione da Azure, vedere la [sezione Configurazione dell'archiviazione](#storage-configuration). Per istruzioni complete su come creare una VM di SQL Server nel portale di Azure, vedere l'[esercitazione sul provisioning](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Se si usano i modelli di Resource Manager seguenti, vengono collegati per impostazione predefinita due dischi dati Premium, senza configurare un pool di archiviazione. È comunque possibile personalizzare questi modelli per modificare il numero di dischi di dati Premium collegati alla macchina virtuale.

* [Creare una VM con il backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Creare una VM con l'applicazione automatica delle patch](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Creare una VM con l'integrazione di AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modello di avvio rapido

È possibile usare il modello di avvio rapido seguente per distribuire una VM di SQL Server usando l'ottimizzazione dell'archiviazione. 

* [Creare una VM con ottimizzazione dell'archiviazione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Creare una VM con UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le VM di SQL Server esistenti, è possibile modificare alcune impostazioni di archiviazione nel portale di Azure. Aprire la [risorsa della macchina virtuale SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selezionare **Panoramica** . La pagina Panoramica di SQL Server illustra l'utilizzo corrente dell'archiviazione della VM. In questo grafico vengono visualizzate tutte le unità esistenti nella VM. Per ogni unità, lo spazio di archiviazione viene visualizzato in quattro sezioni:

* SQL data
* Log SQL
* Altro (archiviazione non SQL)
* Disponibile

Per modificare le impostazioni di archiviazione, selezionare **Configura** in **Impostazioni** . 

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

È possibile modificare le impostazioni del disco per le unità configurate durante il processo di creazione della VM di SQL Server. Selezionando **Estendi unità** viene visualizzata la pagina di modifica dell'unità, che consente di modificare il tipo di disco, nonché di aggiungere altri dischi. 

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configurazione dell'archiviazione

Questa sezione fornisce un riferimento per le modifiche di configurazione dell'archiviazione eseguite automaticamente da Azure durante SQL Server provisioning o configurazione della macchina virtuale nel portale di Azure.

* Azure configura un pool di archiviazione dallo spazio di archiviazione selezionato dalla VM. La sezione successiva di questo argomento contiene informazioni dettagliate sulla configurazione del pool di archiviazione.
* Per la configurazione automatica dell'archiviazione vengono sempre usati dischi dati P30 [SSD Premium](../../../virtual-machines/disks-types.md). Esiste quindi una corrispondenza 1:1 tra il numero selezionato di terabyte e il numero di dischi dati collegati alla VM.

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
> È possibile specificare il tipo di carico di lavoro solo quando si effettua il provisioning di una macchina virtuale SQL Server selezionandolo nel passaggio configurazione archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).