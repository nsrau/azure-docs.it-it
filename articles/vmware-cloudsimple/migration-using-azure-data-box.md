---
title: Azure VMware Solution - migration using Azure Data Box
description: Come usare Azure Data Box per eseguire la migrazione bulk dei dati alla soluzione Azure VMware.How to use Azure Data Box to bulk-migrate data to Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019758"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrazione dei dati alla soluzione VMware di Azure tramite Azure Data BoxMigrating data to Azure VMware Solution by using Azure Data Box

La soluzione cloud di Microsoft Azure Data Box consente di inviare terabyte (TB) di dati ad Azure in modo rapido, economico e affidabile. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo di archiviazione Data Box. Ogni dispositivo di archiviazione ha una capacità di archiviazione massima utilizzabile di 80 TB e viene trasportato al data center da un operatore regionale. Il dispositivo è imballato in un involucro rigido per proteggere i dati durante il trasporto.

Utilizzando Data Box, è possibile eseguire la migrazione in blocco dei dati VMware nel cloud privato. I dati dell'ambiente VMware vSphere locale vengono copiati in Data Box tramite il protocollo NFS (Network File System). La migrazione in blocco dei dati comporta il salvataggio di una copia temportemporale delle macchine virtuali, della configurazione e dei dati associati in Data Box e quindi la distribuzione manuale in Azure.Bulk data migration involves saving a point-in-time copy of virtual machines, configuration, and associated data to Data Box and then manually shipping it to Azure.

In questo articolo vengono fornite informazioni su:In this article, you learn about:

* Impostazione di Data Box.
* Copia dei dati dall'ambiente VMware locale alla casella dati tramite NFS.
* Preparazione per il ritorno di Data Box.
* Preparazione dei dati BLOB per la copia nella soluzione VMware di Azure.Preparing blob data for copying to Azure VMware Solution.
* Copia dei dati da Azure nel cloud privato.

## <a name="scenarios"></a>Scenari

Utilizzare Data Box negli scenari seguenti per la migrazione di massa dei dati:Use Data Box in the following scenarios for bulk data migration:

* To migrate a large amount of data from on-premises to Azure VMware Solution. Questo metodo stabilisce una linea di base e sincronizza le differenze sulla rete.
* Per eseguire la migrazione di un numero elevato di macchine virtuali disattivate (macchine virtuali fredde).
* Per eseguire la migrazione dei dati delle macchine virtuali per l'impostazione di ambienti di sviluppo e test.
* Per eseguire la migrazione di un numero elevato di modelli di macchina virtuale, file ISO e dischi di macchine virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

* Controllare i prerequisiti e ordinare Data Box tramite il portale di Azure.Check the prerequisites and [order Data Box](../databox/data-box-deploy-ordered.md) through your Azure portal. Durante il processo di ordine, è necessario selezionare un account di archiviazione che consenta l'archiviazione BLOB. Dopo aver ricevuto il dispositivo Data Box, connetterlo alla rete locale e [configurare il dispositivo](../databox/data-box-deploy-set-up.md) con un indirizzo IP raggiungibile dalla rete di gestione vSphere.

* Creare una rete virtuale e un account di archiviazione nella stessa area in cui viene eseguito il provisioning della soluzione VMware di Azure.Create a virtual network and a storage account in the same region where your Azure VMware Solution is provisioned.

* Creare una connessione di rete virtuale di Azure dal cloud privato alla rete virtuale in cui viene creato l'account di archiviazione seguendo la procedura descritta in Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute.Create an [Azure virtual network connection](cloudsimple-azure-network-connection.md) from your private cloud to the virtual network where the storage account is created by following the steps in Connect Azure virtual network to [CloudSimple using ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Impostare Data Box per NFS

Connettersi all'interfaccia utente Web locale di Data Box seguendo la procedura descritta nella sezione "Connessione al dispositivo" di [Esercitazione: Cavo e connessione a Azure Data Box](../databox/data-box-deploy-set-up.md).  Configura casella dati per consentire l'accesso ai client NFS:

1. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia.** In **Impostazioni NFS**selezionare **Accesso client NFS**. 

    ![Configurare l'accesso dei client NFS 1](media/nfs-client-access.png)

2. Immettere l'indirizzo IP degli host VMware ESXi e selezionare **Aggiungi**. È possibile configurare l'accesso per tutti gli host nel cluster vSphere ripetendo questo passaggio. Selezionare **OK**.

    ![Configurare l'accesso dei client NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare i file direttamente nella cartella *radice* nell'account di archiviazione.

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. In Condivisioni per File di Azure, le entità di primo livello sono condivisioni e le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Usare i BLOB di blocco di Azure per copiare i dati VMware.Use Azure Block blobs for copying VMware data.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montare la condivisione NFS come archivio dati nel cluster vCenter locale e copiare i dati

La condivisione NFS dalla casella dati deve essere montata come archivio dati nel cluster vCenter locale o nell'host VMware ESXi per copiare i dati nell'archivio dati NFS:

1. Accedere al server vCenter locale.

2. Fare clic con il pulsante destro del mouse su **Datacenter**, scegliere **Archiviazione**, **Nuovo archivio dati**, quindi **Avanti**.

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 dell'Aggiunta guidata datastore selezionare **NFS** in **Tipo**.

   ![Aggiungi nuovo archivio dati - tipo](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione NFS, quindi scegliere **Avanti**.

   ![Aggiungi nuovo archivio dati - Versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome dell'archivio dati, il percorso e il server. È possibile utilizzare l'indirizzo IP della casella dati per il server. Il percorso della `/<StorageAccountName_BlockBlob>/<ContainerName>/` cartella sarà nel formato.

   ![Aggiungere un nuovo archivio dati - Configurazione NFSAdd new datastore - NFS configuration](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si desidera montare l'archivio dati, quindi scegliere **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati - Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e selezionare **Fine**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiare i dati nell'archivio dati NFS della casella dei dati

Le macchine virtuali possono essere migrate o clonate nel nuovo archivio dati.  Tutte le macchine virtuali inutilizzate di cui si desidera eseguire la migrazione possono essere migrate nell'archivio dati NFS di Data Box utilizzando l'opzione **di archiviazione vMotion.** Le macchine virtuali attive possono essere clonate nell'archivio dati NFS di Data Box.Active virtual machines can be cloned to the Data Box NFS datastore.

* Identificare ed elencare le macchine virtuali che possono essere **spostate.**
* Identificare ed elencare le macchine virtuali che devono essere **clonate.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Spostare una macchina virtuale in un archivio dati di Data BoxMove a virtual machine to a Data Box datastore

1. Fare clic con il pulsante destro del mouse sulla macchina virtuale che si desidera spostare nell'archivio dati della casella dei dati e quindi **scegliere Esegui migrazione**.

    ![Eseguire la migrazione della macchina virtualeMigrate virtual machine](media/databox-migration-vm-migrate.png)

2. Selezionare **Modifica archiviazione solo** per il tipo di migrazione e quindi **Avanti**.

    ![Eseguire la migrazione della macchina virtuale - solo archiviazioneMigrate virtual machine - storage only](media/databox-migration-vm-migrate-change-storage.png)

3. Selezionare **Databox-Datastore** come destinazione, quindi scegliere **Avanti**.

    ![Eseguire la migrazione della macchina virtuale- selezionare l'archivio datiMigrate virtual machine - select datastore](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Esaminare le informazioni e selezionare **Fine**.

5. Ripetere i passaggi da 1 a 4 per altre macchine virtuali.

> [!TIP]
> È possibile selezionare più macchine virtuali che si trovano nello stesso stato di alimentazione (attivate o spente) ed eseguirne la migrazione in blocco.

La macchina virtuale verrà migrata nell'archivio dati NFS da Data Box. Dopo la migrazione di tutte le macchine virtuali, è possibile disattivare (arrestare) le macchine virtuali attive in preparazione per la migrazione dei dati alla soluzione VMware di Azure.After all virtual machines are migrated, you can turn off (shut off) the active virtual machines in preparation for migration of data to Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clonare una macchina virtuale o un modello di macchina virtuale nell'archivio dati di Data BoxClone a virtual machine or a virtual machine template to the Data Box datastore

1. Fare clic con il pulsante destro del mouse su una macchina virtuale o su un modello di macchina virtuale che si desidera clonare. Selezionare **Clona in** > **macchina virtuale**.

    ![Clonazione della macchina virtualeVirtual machine clone](media/databox-migration-vm-clone.png)

2. Selezionare un nome per la macchina virtuale clonata o il modello di macchina virtuale.

3. Selezionare la cartella in cui si desidera inserire l'oggetto clonato, quindi selezionare **Avanti**.

4. Selezionare il cluster o il pool di risorse in cui si desidera inserire l'oggetto clonato, quindi selezionare **Avanti**.

5. Selezionare **Databox-Datastore** come percorso di archiviazione, quindi scegliere **Avanti**.

    ![Clonazione macchina virtuale - selezionare l'archivio datiVirtual machine clone - select datastore](media/databox-migration-vm-clone-select-datastore.png)

6. Se si desidera personalizzare le opzioni per l'oggetto clonato, selezionare le opzioni di personalizzazione e quindi scegliere **Avanti**.

7. Rivedere le configurazioni e selezionare **Fine**.

8. Ripetere i passaggi da 1 a 7 per altre macchine virtuali o modelli di macchine virtuali.

Le macchine virtuali verranno clonate e archiviate nell'archivio dati NFS da Data Box. Dopo la clonazione delle macchine virtuali, assicurarsi che vengano arrestate in preparazione per la migrazione dei dati alla soluzione VMware di Azure.After the virtual machines are cloned, make sure they're shut down in preparation for migration of data to Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copiare i file ISO nell'archivio dati di Data Box

1. Dall'interfaccia utente Web di vCenter locale passare a **Archiviazione**.  Selezionare **Databox-Datastore** e quindi **File**. Creare una nuova cartella per l'archiviazione dei file ISO.

    ![Copia ISO - crea una nuova cartella](media/databox-migration-create-folder.png)

2. Specificare un nome per la cartella in cui verranno archiviati i file ISO.

3. Fare doppio clic sulla cartella appena creata per aprirla.

4. Selezionare **Carica file** e quindi selezionare i file ISO che si desidera caricare.
    
    ![Copia ISO - carica file](media/databox-migration-upload-iso.png)

> [!TIP]
> Se si dispone già di file ISO nell'archivio dati locale, è possibile selezionare i file e **Copia** in per copiare i file nell'archivio dati NFS di Data Box.


## <a name="prepare-data-box-for-return"></a>Preparare la casella dati per la restituzione

Dopo aver copiato tutti i dati della macchina virtuale, i dati del modello di macchina virtuale e tutti i file ISO nell'archivio dati NFS della casella dei dati, è possibile disconnettere l'archivio dati dal vCenter. Tutte le macchine virtuali e i modelli di macchine virtuali devono essere rimossi dall'inventario prima di disconnettere l'archivio dati.

### <a name="remove-objects-from-inventory"></a>Rimuovere oggetti dall'inventario

1. Dall'interfaccia utente Web di vCenter locale passare a **Archiviazione**. Selezionare **Databox-Datastore** e quindi **macchine virtuali**.

    ![Rimuovere le macchine virtuali dall'inventario - disattivato](media/databox-migration-select-databox-vm.png)

2. Assicurarsi che tutte le macchine virtuali siano state arrestate.

3. Selezionare tutte le macchine virtuali, fare clic con il pulsante destro del mouse e quindi **scegliere Rimuovi dall'inventario**.

    ![Rimuovere le macchine virtuali dall'inventarioRemove virtual machines from inventory](media/databox-migration-remove-vm-from-inventory.png)

4. Selezionare Modelli VM in Cartelle e quindi ripetere il passaggio **3.Select VM Templates in Folders** and then repeat step 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Rimuovere l'archivio dati NFS data Box da vCenter

L'archivio dati NFS di Data Box deve essere disconnesso dagli host VMware ESXi prima di prepararsi per la restituzione.

1. Dall'interfaccia utente Web di vCenter locale passare a **Archiviazione**.

2. Fare clic con il pulsante destro del mouse su **Databox-Datastore** e selezionare **Smonta datastore**.

    ![Smonta archivio dati della casella dei dati](media/databox-migration-unmount-datastore.png)

3. Selezionare tutti gli host ESXi in cui è montato l'archivio dati e scegliere **OK**.

    ![Smonta archivio dati data Box - seleziona host](media/databox-migration-unmount-datastore-select-hosts.png)

4. Esaminare e accettare eventuali avvisi e selezionare **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Preparare la casella dati per la restituzione e quindi restituirla

Seguire i passaggi descritti nell'articolo [Restituire Azure Data Box e verificare](../databox/data-box-deploy-picked-up.md) il caricamento dei dati in Azure per restituire la casella di dati. Controllare lo stato della copia dei dati nell'account di archiviazione di Azure.Check the status of the data copy to your Azure storage account. Dopo aver completato lo stato, è possibile verificare i dati nell'account di archiviazione di Azure.After the status shows as completed, you can verify the data in your Azure storage account.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiare i dati dall'archiviazione di Azure alla soluzione VMware di AzureCopy data from Azure storage to Azure VMware Solution

I dati copiati nel dispositivo Data Box saranno disponibili nell'account di archiviazione di Azure dopo che lo stato dell'ordine della casella di dati sarà completato. I dati possono ora essere copiati nella soluzione Azure VMware.The data can now be copied to your Azure VMware Solution. I dati nell'account di archiviazione devono essere copiati nell'archivio dati vSAN del cloud privato usando il protocollo NFS. 

Copiare innanzitutto i dati di archiviazione BLOB in un disco gestito in una macchina virtuale Linux in Azure usando **AzCopy**. Rendere il disco gestito disponibile tramite NFS, montare la condivisione NFS come archivio dati nel cloud privato e quindi copiare i dati. Questo metodo consente una copia più rapida dei dati nel cloud privato.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copiare i dati nel cloud privato usando una macchina virtuale Linux e dischi gestiti, quindi esportare come condivisione NFSCopy data to your private cloud using a Linux virtual machine and managed disks, and then export as NFS share

1. Creare una [macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md) in Azure nella stessa area in cui viene creato l'account di archiviazione e dispone di una connessione di rete virtuale di Azure al cloud privato.

2. Creare un disco gestito la cui capacità di archiviazione è maggiore della quantità di dati BLOB e [collegarlo alla macchina virtuale Linux.](../virtual-machines/linux/attach-disk-portal.md)  Se la quantità di dati BLOB è maggiore della capacità del disco gestito più grande disponibile, i dati devono essere copiati in più passaggi o usando più dischi gestiti.

3. Connettersi alla macchina virtuale Linux e montare il disco gestito.

4. Installare [AzCopy nella macchina virtuale Linux.](../storage/common/storage-use-azcopy-v10.md)

5. Scaricare i dati dall'archiviazione BLOB di Azure nel disco gestito usando AzCopy.Download the data from your Azure Blob storage onto the managed disk using AzCopy.  Sintassi `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`del comando: .  Sostituire `<storage-account-name>` con il nome `<container-name>` dell'account di archiviazione di Azure e con il contenitore che contiene i dati copiati tramite Data Box.Replace with your Azure storage account name and with the container that holds the data copied through Data Box.

6. Installare il server NFS nella macchina virtuale Linux:Install the NFS server on your Linux virtual machine:

    - Su una distribuzione Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    - In una distribuzione `sudo yum install nfs-utils`Enterprise Linux: .

7. Modificare l'autorizzazione della cartella nel disco gestito in cui sono stati copiati i dati dall'archiviazione BLOB di Azure.  Modificare le autorizzazioni per tutte le cartelle che si desidera esportare come condivisione NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Assegnare le autorizzazioni per gli indirizzi IP `/etc/exports` client per accedere alla condivisione NFS modificando il file.

    ```bash
    sudo vi /etc/exports
    ```
    
    Immettere le righe seguenti nel file per ogni IP host ESXi del cloud privato.  Se si creano condivisioni per più cartelle, aggiungere tutte le cartelle.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Esportare le condivisioni `sudo exportfs -a` NFS utilizzando il comando .

10. Riavviare il server kernel `sudo systemctl restart nfs-kernel-server` NFS utilizzando il comando.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montare la condivisione NFS della macchina virtuale Linux come archivio dati in un cluster vCenter cloud privato e quindi copiare i dati

La condivisione NFS dalla macchina virtuale Linux deve essere montata come archivio dati nel cluster vCenter del cloud privato. Dopo l'installazione, i dati possono essere copiati dall'archivio dati NFS all'archivio dati vSAN del cloud privato.

1. Accedere al server vCenter del cloud privato.

2. Fare clic con il pulsante destro del mouse su **Datacenter**, scegliere **Archiviazione**, **Nuovo archivio dati**, quindi **Avanti**.

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 dell'Aggiunta guidata datastore selezionare il tipo **NFS.**

   ![Aggiungi nuovo archivio dati - tipo](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione NFS, quindi scegliere **Avanti**.

   ![Aggiungi nuovo archivio dati - Versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome dell'archivio dati, il percorso e il server.  È possibile utilizzare l'indirizzo IP della macchina virtuale Linux per il server.  Il percorso della `/<folder>/<subfolder>/` cartella sarà nel formato.

   ![Aggiungere un nuovo archivio dati - Configurazione NFSAdd new datastore - NFS configuration](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si desidera montare l'archivio dati, quindi scegliere **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati - Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e quindi selezionare **Fine**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Aggiungere macchine virtuali e modelli di macchine virtuali da un archivio dati NFS all'inventarioAdd virtual machines and virtual machine templates from an NFS datastore to the inventory

1. Dall'interfaccia utente Web del cloud privato vCenter passare a **Archiviazione**.  Selezionare un archivio dati NFS della macchina virtuale Linux, quindi selezionare **File**.

    ![Selezionare i file dall'archivio dati NFS](media/databox-migration-datastore-select-files.png)

2. Selezionare una cartella contenente una macchina virtuale o un modello di macchina virtuale.  Nel riquadro dei dettagli selezionare un file con estensione vmx per una macchina virtuale o un file con estensione vmtx per un modello di macchina virtuale.

3. Selezionare **Registra macchina virtuale** per registrare la macchina virtuale nel vCenter del cloud privato.

    ![Registra macchina virtuale](media/databox-migration-datastore-register-vm.png)

4. Selezionare il data center, la cartella e il pool di cluster/risorse in cui si desidera registrare la macchina virtuale.

4. Ripetere i passaggi 3 e 4 per tutte le macchine virtuali e i modelli di macchine virtuali.

5. Passare alla cartella che contiene i file ISO.  Selezionare i file ISO e quindi selezionare **Copia in** per copiare i file in una cartella nell'archivio dati vSAN.

Le macchine virtuali e i modelli di macchine virtuali sono ora disponibili nel cloud privato vCenter. Queste macchine virtuali devono essere spostate dall'archivio dati NFS all'archivio dati vSAN prima di attivarle. È possibile usare l'opzione **di archiviazione vMotion** e selezionare l'archivio dati vSAN come destinazione per le macchine virtuali.

I modelli di macchina virtuale devono essere clonati dall'archivio dati NFS della macchina virtuale Linux all'archivio dati vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Pulire la macchina virtuale LinuxClean up your Linux virtual machine

Dopo aver copiato tutti i dati nel cloud privato, è possibile rimuovere l'archivio dati NFS dal cloud privato:After all the data is copied to your private cloud, you can remove the NFS datastore from your private cloud:

1. Assicurarsi che tutte le macchine virtuali e i modelli vengano spostati e clonati nell'archivio dati vSAN.

2. Rimuovere dall'inventario tutti i modelli di macchina virtuale dall'archivio dati NFS.

3. Smontare l'archivio dati della macchina virtuale Linux dal vCenter del cloud privato.

4. Eliminare la macchina virtuale e il disco gestito da Azure.Delete the virtual machine and managed disk from Azure.

5. Se non si vuole mantenere i dati trasferiti da Data Box nell'account di archiviazione, eliminare l'account di archiviazione di Azure.If you don't want to keep the data that was transferred by Data Box in your storage account, delete the Azure storage account.  
    


## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni su [Data Box](../databox/data-box-overview.md).
* Altre informazioni sulle diverse opzioni per la migrazione dei carichi di [lavoro nel cloud privato.](migrate-workloads.md)
