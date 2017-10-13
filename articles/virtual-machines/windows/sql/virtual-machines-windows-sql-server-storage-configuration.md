---
title: Configurazione dell'archiviazione per le VM di SQL Server | Documentazione Microsoft
description: "Questo argomento descrive come viene configurata l'archiviazione da Azure per le VM di SQL Server durante il provisioning (modello di distribuzione di Resource Manager). Viene inoltre spiegato come è possibile configurare l'archiviazione per le VM di SQL Server esistenti."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: ninarn
ms.openlocfilehash: f10bac1189c94a581487d19fc0cc129acec6a636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configurazione dell'archiviazione per le VM di SQL Server
Quando si configura un'immagine di macchina virtuale di SQL Server in Azure, il portale consente di automatizzare la configurazione dell'archiviazione, ovvero collegare l'archiviazione alla VM, renderla disponibile per SQL Server e ottimizzarla in base alle specifiche esigenze a livello di prestazioni.

Questo argomento illustra come viene configurata l'archiviazione da Azure per le VM di SQL Server, sia durante il provisioning che per le VM esistenti. Questa configurazione è basata sulle [procedure consigliate per le prestazioni](virtual-machines-windows-sql-performance.md) per le VM virtuali di Azure che eseguono SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prerequisiti
Per usare le impostazioni di configurazione automatica dell'archiviazione, la macchina virtuale deve avere le caratteristiche seguenti:

* Provisioning eseguito con un' [immagine della raccolta di SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-create-a-sql-vm-with-per-minute-licensing).
* Uso del [modello di distribuzione Azure Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Uso dell' [archiviazione Premium](../../../storage/common/storage-premium-storage.md).

## <a name="new-vms"></a>Nuove VM
Le sezioni seguenti descrivono come configurare l'archiviazione per le nuove macchine virtuali di SQL Server.

### <a name="azure-portal"></a>Portale di Azure
Durante il provisioning di una VM di Azure con un'immagine della raccolta di SQL Server è possibile scegliere di configurare automaticamente l'archiviazione per la nuova VM. È necessario specificare le dimensioni dell'archiviazione, i limiti per le prestazioni e il tipo di carico di lavoro. Lo screenshot seguente mostra il pannello Configurazione dell'archiviazione usato durante il provisioning di VM di SQL Server.

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

A seconda delle scelte effettuate, Azure esegue le seguenti attività di configurazione dell'archiviazione dopo la creazione della VM:

* Crea e collega i dischi dati di archiviazione Premium alla macchina virtuale.
* Configura i dischi dati in modo che siano accessibili per SQL Server.
* Configura i dischi dati in un pool di archiviazione in base ai requisiti specificati per dimensioni e prestazioni (operazioni di I/O al secondo e velocità effettiva).
* Associa il pool di archiviazione a una nuova unità nella macchina virtuale.
* Ottimizza la nuova unità in base al tipo di carico di lavoro specificato (data warehousing, elaborazione transazionale o generale).

Per ulteriori dettagli su come vengono configurate le impostazioni dell'archiviazione da Azure, vedere la [sezione Configurazione dell'archiviazione](#storage-configuration). Per istruzioni complete su come creare una VM di SQL Server nel portale di Azure, vedere l' [esercitazione sul provisioning](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelli di Resource Manager
Se si usano i modelli di Resource Manager seguenti, vengono collegati per impostazione predefinita due dischi dati Premium, senza configurare un pool di archiviazione. È comunque possibile personalizzare questi modelli per modificare il numero di dischi di dati Premium collegati alla macchina virtuale.

* [Creare una VM con il backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Creare una VM con l'applicazione automatica delle patch](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Creare una VM con l'integrazione di AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VM esistenti
Per le VM di SQL Server esistenti, è possibile modificare alcune impostazioni di archiviazione nel portale di Azure. Selezionare la VM, passare all'area Impostazioni e quindi selezionare Configurazione di SQL Server. Il pannello Configurazione di SQL Server mostra l'utilizzo corrente dell'archiviazione della VM. In questo grafico vengono visualizzate tutte le unità esistenti nella VM. Per ogni unità, lo spazio di archiviazione viene visualizzato in quattro sezioni:

* Dati SQL
* Log SQL
* Altro (archiviazione non SQL)
* Disponibile

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Per configurare l'archiviazione per aggiungere una nuova unità o estendere un'unità esistente, fare clic sul collegamento Modifica sopra il grafico.

Le opzioni di configurazione visualizzate variano a seconda che la funzionalità sia stata usata o meno in precedenza. Per il primo utilizzo è possibile specificare i requisiti di archiviazione per una nuova unità. Se questa funzionalità è stata usata in precedenza per creare un'unità, è possibile scegliere di estendere l'archiviazione dell'unità.

### <a name="use-for-the-first-time"></a>Primo utilizzo
Se si usa questa funzionalità per la prima volta, è possibile specificare le dimensioni dell'archiviazione e i limiti di prestazioni per una nuova unità. Questa esperienza è simile a quella disponibile in fase di provisioning. La differenza principale è che non è consentito specificare il tipo di carico di lavoro. Questa restrizione impedisce di compromettere eventuali configurazioni di SQL Server esistenti nella macchina virtuale.

![Configurare i dispositivi di scorrimento delle opzioni per l'archiviazione di SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crea una nuova unità in base alle specifiche. In questo scenario, Azure esegue le seguenti attività di configurazione dell'archiviazione:

* Crea e collega i dischi dati di archiviazione Premium alla macchina virtuale.
* Configura i dischi dati in modo che siano accessibili per SQL Server.
* Configura i dischi dati in un pool di archiviazione in base ai requisiti specificati per dimensioni e prestazioni (operazioni di I/O al secondo e velocità effettiva).
* Associa il pool di archiviazione a una nuova unità nella macchina virtuale.

Per ulteriori dettagli su come vengono configurate le impostazioni dell'archiviazione da Azure, vedere la [sezione Configurazione dell'archiviazione](#storage-configuration).

### <a name="add-a-new-drive"></a>Aggiungere una nuova unità
Se l'archiviazione è già stata configurata nella VM di SQL Server, per l'espansione dell'archiviazione diventano disponibili due nuove opzioni. La prima opzione prevede l'aggiunta di una nuova unità, aumentando potenzialmente il livello di prestazioni della VM.

![Aggiungere una nuova unità a una VM di SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Dopo aver aggiunto l'unità, tuttavia, è necessario eseguire alcune configurazioni aggiuntive manuali per ottenere il miglioramento delle prestazioni.

### <a name="extend-the-drive"></a>Estendere l'unità
L'altra opzione per l'espansione dell'archiviazione prevede l'estensione dell'unità esistente. Questa opzione aumenta l'archiviazione disponibile per l'unità, ma non incrementa le prestazioni. Con i pool di archiviazione non è possibile modificare il numero di colonne dopo la creazione del pool. Il numero di colonne determina il numero di scritture parallele di cui è possibile eseguire lo striping sui dischi dati. Di conseguenza, qualsiasi disco dati aggiunto non consente di aumentare le prestazioni. L'aggiunta di dischi dati consente esclusivamente di ottenere più spazio di archiviazione per i dati da scrivere. Questa limitazione significa anche che, in caso di estensione dell'unità, il numero di colonne determina il numero minimo di dischi dati che è possibile aggiungere. Se si crea un pool di archiviazione con quattro dischi dati, quindi, anche il numero di colonne è quattro. Ogni volta che si estende l'archiviazione, è necessario aggiungere almeno quattro dischi dati.

![Estendere un'unità per una VM di SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configurazione dell'archiviazione
In questa sezione sono disponibili informazioni di riferimento sulle modifiche della configurazione dell'archiviazione eseguite automaticamente da Azure durante il provisioning o la configurazione di VM di SQL nel portale di Azure.

* Se sono stati selezionati meno di due TB di spazio di archiviazione per la VM, Azure non crea un pool di archiviazione.
* Se sono stati selezionati almeno due TB di spazio di archiviazione per la VM, Azure configura un pool di archiviazione. La sezione successiva di questo argomento fornisce i dettagli della configurazione del pool di archiviazione.
* Per la configurazione automatica dell'archiviazione vengono sempre usati dischi dati P30 di [archiviazione Premium](../../../storage/common/storage-premium-storage.md) . Esiste quindi una corrispondenza 1:1 tra il numero selezionato di terabyte e il numero di dischi dati collegati alla VM.

Per informazioni sui prezzi, vedere la pagina [Prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage) nella scheda **Archiviazione su disco** .

### <a name="creation-of-the-storage-pool"></a>Creazione del pool di archiviazione
Azure usa le impostazioni seguenti per creare il pool di archiviazione nelle VM di SQL.

| Impostazione | Valore |
| --- | --- |
| Dimensioni di striping |256 KB (data warehousing); 64 KB (transazionale) |
| Dimensione disco |1 TB ciascuno |
| Cache |Lettura |
| Dimensioni allocazione |Dimensioni delle unità di allocazione NTFS = 64 KB |
| Inizializzazione file immediata |Enabled |
| Blocco di pagine in memoria |Enabled |
| Ripristino |Recupero con registrazione minima (nessuna resilienza) |
| Numero di colonne |Numero di dischi dati<sup>1</sup> |
| Percorso TempDB |Archiviato sui dischi dati<sup>2</sup> |

<sup>1</sup> Dopo aver creato il pool di archiviazione non è possibile modificare il numero di colonne nel pool.

<sup>2</sup> Questa impostazione si applica solo alla prima unità creata usando la funzionalità di configurazione dell'archiviazione.

## <a name="workload-optimization-settings"></a>Impostazioni di ottimizzazione del carico di lavoro
La tabella seguente descrive le tre opzioni disponibili per il tipo di carico di lavoro e le ottimizzazioni corrispondenti:

| Tipo di carico di lavoro | Descrizione | Ottimizzazioni |
| --- | --- | --- |
| **Generale** |Impostazione predefinita che supporta la maggior parte dei carichi di lavoro |None |
| **Elaborazione transazionale** |Ottimizza l'archiviazione per carichi di lavoro OLTP di database tradizionali |Flag di traccia 1117<br/>Flag di traccia 1118 |
| **Data warehousing** |Ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report |Flag di traccia 610<br/>Flag di traccia 1117 |

> [!NOTE]
> È possibile specificare il tipo di carico di lavoro solo quando si esegue il provisioning di una macchina virtuale di SQL Serer, selezionandolo nel passaggio di configurazione dell'archiviazione.
>
>

## <a name="next-steps"></a>Passaggi successivi
Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
