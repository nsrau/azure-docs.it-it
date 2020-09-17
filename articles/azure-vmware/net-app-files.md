---
title: File NetApp per la soluzione VMware di Azure
description: Usare Azure NetApp Files con macchine virtuali VMware di Azure per eseguire la migrazione e sincronizzare i dati tra server locali, VM della soluzione VMware di Azure e infrastrutture cloud.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 0adf137db358d3561365a3a1ed2def338eeeb158
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708926"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>File NetApp per la soluzione VMware di Azure

Questo articolo illustra i passaggi per l'integrazione di Azure NetApp Files con i carichi di lavoro basati su soluzioni VMware di Azure. [Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) è un servizio di Azure per la migrazione e l'esecuzione di carichi di lavoro di file aziendali nel cloud senza modifiche al codice. Consente la migrazione semplificata dei dati in infrastrutture locali e cloud. La sincronizzazione dei dati rapida e con sicurezza avanzata semplifica gli scenari di migrazione e DevOps con funzionalità quali snapshot istantaneo, ripristino e integrazione Active Directory.

## <a name="topology"></a>Topologia

In questo scenario, per testare e verificare la condivisione del pool di Azure NetApp Files, Azure NetApp Files viene configurato in Azure con pool di capacità, pool di volumi e subnet. È stato usato il protocollo NFS. Sia Azure NetApp Files che la soluzione VMware di Azure vengono creati nella stessa area di Azure, Stati Uniti orientali. La connessione alla soluzione VMware di Azure è tramite Azure ExpressRoute.

![File NetApp per la topologia della soluzione VMware di Azure.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Prerequisiti 

> [!div class="checklist"]
> * Sottoscrizione di Azure con Azure NetApp Files
> * Subnet per il file di Azure NetApp
> * VM Linux in una soluzione VMware di Azure
> * VM Windows in una soluzione VMware di Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Verificare la configurazione di Azure NetApp Files 

Prima di tutto, verificare la configurazione di Azure NetApp Files, creata in Azure su un disco Premium.

1. Nel portale di Azure, in **archiviazione**selezionare **Azure NetApp files**. Viene visualizzato un elenco dei Azure NetApp Files configurati.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Elenco di Azure NetApp Files."::: 

2. Se si seleziona un account NetApp, è possibile visualizzare diverse impostazioni. Ad esempio, se si seleziona **Contoso-ANF2**, verranno visualizzate le relative impostazioni. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Visualizzare le impostazioni di un account NetApp."::: 

3. Selezionare **pool di capacità** per verificare il pool configurato. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Verificare il pool configurato.":::

    È possibile osservare che il pool di archiviazione è configurato come 4 TiB con disco Premium.

4. Selezionare i **volumi** (vedere screenshot del passaggio 2) per visualizzare i volumi creati nel pool di capacità.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files volume.":::

5. Selezionare un volume per visualizzarne la configurazione.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Dettagli di configurazione di un volume.":::

    È possibile osservare che il pool di volumi anfpool, con 200 GiB, è stato creato come condivisione file NetApp. È stata creata una rete virtuale IP privato per Azure NetApp Files che fornisce il percorso NFS da montare nelle macchine virtuali della soluzione VMware di Azure. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protocolli supportati da Azure NetApp Files  

Azure NetApp Files supporta il mapping delle condivisioni SMB (Server Message Block) e NFS (Network File System). 

- **Condivisione SMB**: per distribuire un volume SMB, è prima necessario creare una connessione Active Directory. I controller di dominio specificati devono essere accessibili dalla subnet delegata di Azure NetApp Files per una connessione corretta. Una volta configurata Active Directory all'interno dell'account Azure NetApp Files, verrà visualizzata come elemento selezionabile durante la creazione di volumi SMB. 

- **Condivisione NFS**: Azure NetApp files contribuisce alla creazione di volumi tramite NFS (NFSv3 e NFSv 4.1), SMBv3 o un protocollo duale (NFSV3 e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. È possibile montare NFS in un server Linux tramite una riga di comando.

## <a name="create-azure-netapp-files"></a>Crea Azure NetApp Files 

1. Accedere al [Portale di Azure](https://rc.portal.azure.com/#home). In servizi di Azure selezionare **Azure NetApp files**. 

2. Selezionare **+ Aggiungi** per creare un nuovo volume Azure NetApp files. 

3. Compilare i campi obbligatori (nome, sottoscrizione, gruppo di risorse e località) e selezionare **Crea**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Aggiungere pool di capacità a Azure NetApp Files 

1. Nella portale di Azure selezionare **Azure NetApp files**, selezionare pool di **capacità** e quindi **Aggiungi pool**. 

2. Immettere i dettagli necessari per il nome del pool di volumi, il livello di servizio e le dimensioni del disco (FQDN), l'indirizzo IP e il peso. Selezionare **Aggiungi**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Aggiungere un pool di capacità ai file NetApp.":::

3. Per creare volumi nel pool di capacità, selezionare **volumi** nel riquadro di ricerca e selezionare **+ Aggiungi volume**. 
 
4. Compilare i campi obbligatori come illustrato nello screenshot seguente.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Aggiungere i volumi nel pool di capacità.":::

5. Nella pagina successiva selezionare la condivisione tipo di protocollo. Selezionare le versioni se si tratta di una condivisione NFS e selezionare il dominio Active Directory se si tratta di una condivisione SMB.  

6. Se si tratta di una condivisione NFS, aggiungere l'indirizzo IP di origine da cui verrà eseguito l'accesso alla condivisione del tipo di protocollo. Selezionare **Rivedi e crea**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Selezionare i dettagli del volume.":::
 
    In Azure NetApp Files portale di Azure la condivisione NFS è pronta per l'uso.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="La condivisione NFS è pronta.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Montare una condivisione file NFS nelle VM della soluzione VMware di Azure

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Montare una condivisione file NFS in una macchina virtuale Windows di Azure soluzione VMware

- Aprire un prompt dei comandi ed eseguire il comando per eseguire il mapping della condivisione file NFS. Gli screenshot seguenti mostrano l'unità di condivisione mappata in una macchina virtuale Windows in una soluzione VMware di Azure.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Condividere un'unità mappata in una macchina virtuale Windows in una soluzione VMware di Azure.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Condividi unità mappata in una macchina virtuale Windows.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Montare una condivisione file NFS in una VM Linux della soluzione VMware di Azure

#### <a name="setting-up-your-azure-instance"></a>Configurazione dell'istanza di Azure

1. Nel portale di Azure associare l'istanza di Azure a una subnet definita nella stessa rete virtuale del volume.

    > [!NOTE]
    > Per la subnet è necessaria una regola del gruppo di sicurezza di rete che consenta il traffico sulle porte NFS (2049, 111), UDP e TCP.

2. Aprire un client SSH e connettersi all'istanza di Azure. Per altre informazioni, vedere [come usare le chiavi SSH con Windows in Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Installare il client NFS nell'istanza di Azure:
   - Nell'istanza Red Hat Enterprise Linux o SUSE Linux: `sudo yum install -y nfs-utils`
   - In un'istanza Ubuntu o Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Montaggio del file system

1. Creare una nuova directory nell'istanza di Azure: `sudo mkdir ANFPOOL`

2. Selezionare un indirizzo IP di destinazione di montaggio.

3. Montare i file system: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Test radice.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Creare una connessione Active Directory per una condivisione SMB

1. Selezionare un account NetApp nell'portale di Azure.

2. In Azure NetApp Files selezionare **Active Directory connessioni**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Connessioni di Active Directory."::: 

3. Selezionare **join** per aggiungere la condivisione SMB a Active Directory. Lo screenshot seguente mostra i dettagli del dominio della condivisione SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Dettagli del dominio della condivisione SMB."::: 

    La condivisione file SMB di Azure è pronta per l'uso.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="La condivisione file SMB è pronta."::: 

4. Eseguire il mapping della condivisione SMB alla macchina virtuale della soluzione VMware di Windows Azure. Usare il percorso di montaggio SMB, come illustrato nella schermata precedente. Per altre informazioni, vedere [eseguire il mapping di un'unità di rete in Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [gerarchia di archiviazione di Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Vedere [gestione del ciclo di vita delle macchine virtuali della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md)
- Vedere [integrare una soluzione VMware di Azure in un'architettura Hub e spoke](concepts-avs-hub-and-spoke-integration.md)
