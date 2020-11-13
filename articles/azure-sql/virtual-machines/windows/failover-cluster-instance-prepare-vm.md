---
title: Preparare le macchine virtuali per un'istanza FCI
description: Preparare le macchine virtuali di Azure per usarle con un'istanza del cluster di failover e SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: a9289fad6f7ae1030628bedcf1a62cacc0b1e23a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564481"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Preparare le macchine virtuali per un'istanza FCI (SQL Server in macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come preparare macchine virtuali (VM) di Azure per usarle con un'istanza del cluster di failover di SQL Server (FCI). Le impostazioni di configurazione variano a seconda della soluzione di archiviazione FCI, quindi convalidare la scelta della configurazione corretta per adattarla all'ambiente e all'azienda. 

Per altre informazioni, vedere Panoramica di [FCI con SQL Server nelle macchine virtuali di Azure](failover-cluster-instance-overview.md) e [procedure consigliate per cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Prerequisiti 

- Una sottoscrizione di Microsoft Azure. Inizia subito [gratuitamente](https://azure.microsoft.com/free/). 
- Un dominio Windows in macchine virtuali di Azure o un Data Center locale esteso ad Azure con associazione di rete virtuale.
- Un account che dispone delle autorizzazioni per creare oggetti in macchine virtuali di Azure e in Active Directory.
- Una rete virtuale e una subnet di Azure con uno spazio indirizzi IP sufficiente per questi componenti:
   - Entrambe le macchine virtuali
   - Indirizzo IP del cluster di failover di Windows
   - Un indirizzo IP per ogni FCI
- DNS configurato nella rete di Azure, che punta ai controller di dominio.

## <a name="choose-an-fci-storage-option"></a>Scegliere un'opzione di archiviazione FCI

Le impostazioni di configurazione per la macchina virtuale variano in base all'opzione di archiviazione che si intende usare per l'istanza del cluster di failover di SQL Server. Prima di preparare la macchina virtuale, rivedere le [Opzioni di archiviazione FCI disponibili](failover-cluster-instance-overview.md#storage) e scegliere l'opzione più adatta all'ambiente e alle esigenze aziendali. Quindi selezionare con attenzione le opzioni di configurazione della VM appropriate in questo articolo in base alla selezione dell'archiviazione. 

## <a name="configure-vm-availability"></a>Configurare la disponibilità della macchina virtuale 

Per la funzionalità cluster di failover di è necessario che le macchine virtuali siano inserite in un [set di disponibilità](../../../virtual-machines/linux/tutorial-availability-sets.md) o in una [zona di disponibilità](../../../availability-zones/az-overview.md#availability-zones). Se si scelgono i set di disponibilità, è possibile usare i [gruppi di posizionamento di prossimità](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) per individuare le VM più vicine. Infatti, i gruppi di posizionamento di prossimità sono un prerequisito per l'uso dei dischi condivisi di Azure. 

Selezionare con attenzione l'opzione della disponibilità della macchina virtuale corrispondente alla configurazione del cluster desiderata: 

 - **Dischi condivisi di Azure** : [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) configurato con dominio di errore e dominio di aggiornamento impostato su 1 e inserito all'interno di un gruppo di [posizionamento di prossimità](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
 - **Condivisioni file Premium** : [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) o [zona di disponibilità](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address). Le condivisioni file Premium sono l'unica opzione di archiviazione condivisa se si scelgono le zone di disponibilità come configurazione della disponibilità per le macchine virtuali. 
 - **Spazi di archiviazione diretta** : [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

>[!IMPORTANT]
>Non è possibile impostare o modificare il set di disponibilità dopo aver creato una macchina virtuale.

## <a name="create-the-virtual-machines"></a>Creare le macchine virtuali

Una volta configurata la disponibilità della macchina virtuale, si è pronti per creare le macchine virtuali. È possibile scegliere di usare un'immagine di Azure Marketplace in cui o non è già installato SQL Server. Tuttavia, se si sceglie un'immagine per SQL Server nelle macchine virtuali di Azure, sarà necessario disinstallare SQL Server dalla macchina virtuale prima di configurare l'istanza del cluster di failover. 

### <a name="considerations"></a>Considerazioni
In un cluster di failover guest di macchine virtuali IaaS di Azure è consigliabile usare una sola scheda di rete per ogni server (nodo del cluster) e una sola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure.

Inserire entrambe le macchine virtuali:

- Nello stesso gruppo di risorse di Azure del set di disponibilità, se si usano i set di disponibilità.
- Nella stessa rete virtuale del controller di dominio.
- In una subnet con spazio indirizzi IP sufficiente per entrambe le macchine virtuali e tutte le istanze del cluster di failover che si potrebbero usare nel cluster.
- Nel set di disponibilità o nella zona di disponibilità di Azure.

È possibile creare una macchina virtuale di Azure usando un'immagine [con](sql-vm-create-portal-quickstart.md) o [senza](../../../virtual-machines/windows/quick-create-portal.md) SQL Server preinstallata. Se si sceglie l'immagine SQL Server, sarà necessario disinstallare manualmente l'istanza di SQL Server prima di installare l'istanza del cluster di failover. 


## <a name="uninstall-sql-server"></a>Disinstallare SQL Server

Come parte del processo di creazione dell'istanza FCI, è necessario installare SQL Server come istanza cluster nel cluster di failover. *Se è stata distribuita una macchina virtuale con un'immagine di Azure Marketplace senza SQL Server, è possibile ignorare questo passaggio.* Se è stata distribuita un'immagine con SQL Server preinstallata, è necessario annullare la registrazione della macchina virtuale SQL Server dall'estensione SQL IaaS Agent, quindi disinstallare SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Annullare la registrazione dall'estensione SQL IaaS Agent

SQL Server le immagini di VM da Azure Marketplace vengono registrate automaticamente con l'estensione SQL IaaS Agent. Prima di disinstallare l'istanza di SQL Server preinstallata, è necessario prima [annullare la registrazione di ogni macchina virtuale SQL Server dall'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Disinstallare SQL Server

Dopo aver annullato la registrazione dall'estensione, è possibile disinstallare SQL Server. Seguire questa procedura in ogni macchina virtuale: 

1. Connettersi alla macchina virtuale tramite RDP.

   Quando ci si connette per la prima volta a una macchina virtuale con RDP, un messaggio chiede se si vuole rendere il PC individuabile sulla rete. Selezionare **Sì**.

1. Se si usa una delle immagini di macchina virtuale basate su SQL Server, rimuovere l'istanza di SQL Server:

   1. In **Programmi e funzionalità** fare clic con il pulsante destro del mouse su **Microsoft SQL Server 201_ (64-bit)** e selezionare **Disinstalla/Cambia**.
   1. Selezionare **Rimuovi**.
   1. Selezionare l'istanza predefinita.
   1. Rimuovere tutte le funzionalità in **Servizi motore di database**. Non rimuovere elementi in **funzionalità condivise**. Il contenuto visualizzato sarà simile allo screenshot seguente:

      ![Selezione delle funzionalità](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Selezionare **Avanti** e quindi **Rimuovi**.
   1. Al termine della rimozione dell'istanza, riavviare la macchina virtuale. 

## <a name="open-the-firewall"></a>Aprire il firewall 

In ogni macchina virtuale aprire la porta Windows Firewall TCP utilizzata da SQL Server. Per impostazione predefinita, si tratta della porta 1433. Tuttavia, è possibile modificare la porta SQL Server in una distribuzione di macchine virtuali di Azure, quindi aprire la porta usata da SQL Server nell'ambiente in uso. Questa porta viene aperta automaticamente in SQL Server immagini distribuite da Azure Marketplace. 

Se si usa un servizio di [bilanciamento del carico](failover-cluster-instance-vnn-azure-load-balancer-configure.md), è necessario aprire anche la porta utilizzata dal probe di integrità. Per impostazione predefinita, si tratta della porta 59999. Tuttavia, può trattarsi di qualsiasi porta TCP specificata al momento della creazione del servizio di bilanciamento del carico. 

In questa tabella vengono illustrate in dettaglio le porte che potrebbero essere necessarie per aprire, a seconda della configurazione dell'istanza del cluster di failover: 

   | Scopo | Porta | Note
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Porta normale per le istanze predefinite di SQL Server. Se è stata usata un'immagine della raccolta, questa porta è automaticamente aperta. </br> </br> **Utilizzato da** : tutte le configurazioni dell'istanza FCI. |
   | Probe di integrità | TCP 59999 | Qualsiasi porta TCP aperta. Configurare il [Probe di integrità](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) del servizio di bilanciamento del carico e il cluster per usare questa porta. </br> </br> **Usato da** : FCI con Load Balancer. |
   | Condivisione file | UDP 445 | Porta utilizzata dal servizio Condivisione file. </br> </br> **Usato da** : FCI con la condivisione file Premium. |

## <a name="join-the-domain"></a>Accedere al dominio

È anche necessario aggiungere le macchine virtuali al dominio. A tale scopo, è possibile usare un [modello di avvio rapido](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Verificare la configurazione dell'archiviazione

Le macchine virtuali create da Azure Marketplace sono dotate di archiviazione collegata. Se si prevede di configurare l'archiviazione FCI usando condivisioni file Premium o dischi condivisi di Azure, è possibile rimuovere la risorsa di archiviazione collegata per risparmiare sui costi perché l'archiviazione locale non viene usata per l'istanza del cluster di failover. Tuttavia, è possibile usare l'archiviazione collegata per Spazi di archiviazione diretta le soluzioni FCI, quindi rimuoverle in questo caso potrebbe non essere utile. Esaminare la soluzione di archiviazione FCI per determinare se la rimozione dell'archiviazione collegata è ottimale per il risparmio dei costi. 


## <a name="next-steps"></a>Passaggi successivi

Ora che è stato preparato l'ambiente della macchina virtuale, si è pronti per configurare l'istanza del cluster di failover. 

Scegliere una delle seguenti guide per configurare l'ambiente FCI appropriato per l'azienda: 
- [Configurare FCI con i dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Configurare FCI con una condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Configurare FCI con Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Per altre informazioni, vedere una panoramica di [FCI con SQL Server in macchine virtuali di Azure](failover-cluster-instance-overview.md) e [configurazioni HADR supportate](hadr-cluster-best-practices.md). 

Per altre informazioni, vedere: 
- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
