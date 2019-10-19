---
title: Soluzione VMware di Azure-migrazione con Azure Data Box
description: Come usare Azure Data Box per eseguire la migrazione in blocco dei dati in una soluzione VMware di Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552898"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrazione dei dati alla soluzione VMware di Azure tramite Azure Data Box

La soluzione Microsoft Azure Data Box cloud consente di inviare terabyte (TBs) di dati ad Azure in modo rapido, economico e affidabile. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo di archiviazione Data Box. Ogni dispositivo di archiviazione ha una capacità di archiviazione massima utilizzabile di 80 TB e viene trasportato nel Data Center da un vettore regionale. Il dispositivo è imballato in un involucro rigido per proteggere i dati durante il trasporto.

Con Data Box, è possibile eseguire la migrazione in blocco dei dati VMware nel cloud privato. I dati dell'ambiente di VMware vSphere locale vengono copiati in Data Box tramite il protocollo NFS (Network File System). La migrazione dei dati in blocco comporta il salvataggio di una copia temporizzata di macchine virtuali, configurazione e dati associati a Data Box e quindi la spedizione manuale in Azure.

In questo articolo vengono fornite informazioni su:

* Impostazione Data Box.
* La copia dei dati dall'ambiente VMware locale al Data Box da tramite NFS.
* Preparazione per la restituzione di Data Box.
* Preparazione dei dati BLOB per la copia in una soluzione VMware di Azure.
* Copia dei dati da Azure nel cloud privato.

## <a name="scenarios"></a>Scenari

Utilizzare Data Box negli scenari seguenti per la migrazione dei dati in blocco:

* Per eseguire la migrazione di una grande quantità di dati da una soluzione VMware locale ad Azure. Questo metodo stabilisce una linea di base e sincronizza le differenze sulla rete.
* Per eseguire la migrazione di un numero elevato di macchine virtuali disattivate (macchine virtuali a freddo).
* Per eseguire la migrazione dei dati delle macchine virtuali per la configurazione di ambienti di sviluppo e test.
* Per eseguire la migrazione di un numero elevato di modelli di macchina virtuale, file ISO e dischi di macchine virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

* Verificare i prerequisiti e [ordinare Data Box](../databox/data-box-deploy-ordered.md) tramite il portale di Azure. Durante il processo di ordine, è necessario selezionare un account di archiviazione che Abilita l'archiviazione BLOB. Dopo aver ricevuto il dispositivo Data Box, connetterlo alla rete locale e [configurare il dispositivo](../databox/data-box-deploy-set-up.md) con un indirizzo IP raggiungibile dalla rete di gestione vSphere.

* Creare una rete virtuale e un account di archiviazione nella stessa area in cui viene effettuato il provisioning della soluzione VMware di Azure.

* Creare una [connessione di rete virtuale di Azure](cloudsimple-azure-network-connection.md) dal cloud privato alla rete virtuale in cui viene creato l'account di archiviazione seguendo i passaggi descritti in [connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Configurare Data Box per NFS

Connettersi all'interfaccia utente Web locale di Data Box seguendo la procedura descritta nella sezione "connettersi al dispositivo" di [esercitazione: cablaggio e connessione al Azure Data Box](../databox/data-box-deploy-set-up.md).  Configurare Data Box per consentire l'accesso ai client NFS:

1. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia** . In **impostazioni NFS**selezionare **accesso client NFS**. 

    ![Configurare l'accesso dei client NFS 1](media/nfs-client-access.png)

2. Immettere l'indirizzo IP del VMware ESXi host e selezionare **Aggiungi**. È possibile configurare l'accesso per tutti gli host nel cluster vSphere ripetendo questo passaggio. Selezionare **OK**.

    ![Configurare l'accesso dei client NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare i file direttamente nella cartella *radice* dell'account di archiviazione.

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. In condivisioni per File di Azure, le entità di primo livello sono condivisioni e le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Usare i BLOB in blocchi di Azure per la copia dei dati VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montare la condivisione NFS come archivio dati nel cluster vCenter locale e copiare i dati

La condivisione NFS dal Data Box deve essere montata come archivio dati nel cluster vCenter locale o VMware ESXi host per poter copiare i dati nell'archivio dati NFS:

1. Accedere al server vCenter locale.

2. Fare clic con il pulsante destro del mouse su **datacenter**, scegliere **archiviazione**, selezionare **nuovo archivio dati**, quindi selezionare **Avanti**.

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare **NFS** in **tipo**.

   ![Aggiungi nuovo tipo di archivio dati](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione NFS e quindi fare clic su **Avanti**.

   ![Aggiungi nuovo archivio dati-versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome dell'archivio dati, il percorso e il server. È possibile usare l'indirizzo IP del Data Box per il server. Il percorso della cartella sarà nel formato `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Aggiungi nuovo archivio dati-configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si vuole montare l'archivio dati e quindi fare clic su **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati-Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e selezionare **fine**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiare i dati nell'archivio dati NFS di Data Box

È possibile eseguire la migrazione o la clonazione delle macchine virtuali nel nuovo archivio dati.  È possibile eseguire la migrazione di tutte le macchine virtuali non usate di cui si vuole eseguire la migrazione nell'archivio dati NFS di Data Box usando l'opzione **Storage vMotion** . Le macchine virtuali attive possono essere clonate nell'archivio dati NFS Data Box.

* Identificare ed elencare le macchine virtuali che possono essere **spostate**.
* Identificare ed elencare le macchine virtuali che devono essere **clonate**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Spostare una macchina virtuale in un archivio dati Data Box

1. Fare clic con il pulsante destro del mouse sulla macchina virtuale che si desidera spostare nell'archivio dati Data Box, quindi selezionare **Esegui migrazione**.

    ![Esegui migrazione macchina virtuale](media/databox-migration-vm-migrate.png)

2. Selezionare **modifica archiviazione solo** per il tipo di migrazione e quindi fare clic su **Avanti**.

    ![Esegui la migrazione della macchina virtuale-solo archiviazione](media/databox-migration-vm-migrate-change-storage.png)

3. Selezionare **databox-datastore** come destinazione e quindi fare clic su **Next (avanti**).

    ![Esegui migrazione macchina virtuale-seleziona Archivio dati](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Esaminare le informazioni e selezionare **fine**.

5. Ripetere i passaggi da 1 a 4 per altre macchine virtuali.

> [!TIP]
> È possibile selezionare più macchine virtuali che si trovano nello stesso stato di alimentazione (attivato o disattivato) ed eseguirne la migrazione in blocco.

Verrà eseguita la migrazione della macchina virtuale all'archivio dati NFS da Data Box. Dopo la migrazione di tutte le macchine virtuali, è possibile disattivare (arrestare) le macchine virtuali attive in preparazione per la migrazione dei dati alla soluzione VMware di Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clonare una macchina virtuale o un modello di macchina virtuale nell'archivio dati Data Box

1. Fare clic con il pulsante destro del mouse su una macchina virtuale o su un modello di macchina virtuale che si desidera clonare. Selezionare **clona**  > **clona nella macchina virtuale**.

    ![Clone della macchina virtuale](media/databox-migration-vm-clone.png)

2. Selezionare un nome per la macchina virtuale clonata o il modello di macchina virtuale.

3. Selezionare la cartella in cui si vuole inserire l'oggetto clonato e quindi fare clic su **Avanti**.

4. Selezionare il cluster o il pool di risorse in cui si vuole inserire l'oggetto clonato, quindi fare clic su **Avanti**.

5. Selezionare **databox-datastore** come percorso di archiviazione e quindi fare clic su **Avanti**.

    ![Clone della macchina virtuale: selezionare l'archivio dati](media/databox-migration-vm-clone-select-datastore.png)

6. Se si desidera personalizzare le opzioni per l'oggetto clonato, selezionare le opzioni di personalizzazione e quindi fare clic su **Avanti**.

7. Esaminare le configurazioni e selezionare **fine**.

8. Ripetere i passaggi da 1 a 7 per le macchine virtuali o i modelli di macchina virtuale aggiuntivi.

Le macchine virtuali verranno clonate e archiviate nell'archivio dati NFS da Data Box. Dopo aver clonato le macchine virtuali, assicurarsi che siano arrestate in preparazione alla migrazione dei dati nella soluzione VMware di Azure.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copia i file ISO nell'archivio dati Data Box

1. Dall'interfaccia utente Web di vCenter locale passare ad **archiviazione**.  Selezionare **databox-datastore** e quindi selezionare **file**. Creare una nuova cartella per l'archiviazione dei file ISO.

    ![Copia ISO-Crea nuova cartella](media/databox-migration-create-folder.png)

2. Consente di specificare un nome per la cartella in cui verranno archiviati i file ISO.

3. Fare doppio clic sulla cartella appena creata per aprirla.

4. Selezionare **Carica file** e quindi selezionare i file ISO che si desidera caricare.
    
    ![Copia file ISO-caricamento](media/databox-migration-upload-iso.png)

> [!TIP]
> Se sono già presenti file ISO nell'archivio dati locale, è possibile selezionare i file e **copiarli** in per copiare i file nell'archivio dati NFS data box.


## <a name="prepare-data-box-for-return"></a>Prepara Data Box per la restituzione

Dopo che tutti i dati della macchina virtuale, i dati dei modelli di macchina virtuale e tutti i file ISO vengono copiati nell'archivio dati NFS di Data Box, è possibile disconnettere l'archivio dati da vCenter. Tutte le macchine virtuali e i modelli di macchina virtuale devono essere rimossi dall'inventario prima di disconnettere l'archivio dati.

### <a name="remove-objects-from-inventory"></a>Rimuovi oggetti dall'inventario

1. Dall'interfaccia utente Web di vCenter locale passare ad **archiviazione**. Selezionare **databox-datastore** e quindi selezionare **VM**.

    ![Rimuovi le macchine virtuali dall'inventario-disattivato](media/databox-migration-select-databox-vm.png)

2. Verificare che tutte le macchine virtuali siano arrestate.

3. Selezionare tutte le macchine virtuali, fare clic con il pulsante destro del mouse e quindi scegliere **Rimuovi da inventario**.

    ![Rimuovi le macchine virtuali dall'inventario](media/databox-migration-remove-vm-from-inventory.png)

4. Selezionare **modelli di macchina virtuale in cartelle** e quindi ripetere il passaggio 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Rimuovere l'archivio dati NFS di Data Box da vCenter

È necessario disconnettere l'archivio dati NFS Data Box dagli host VMware ESXi prima di prepararsi per la restituzione.

1. Dall'interfaccia utente Web di vCenter locale passare ad **archiviazione**.

2. Fare clic con il pulsante destro del mouse su **databox-datastore** e scegliere **Disinstalla archivio dati**.

    ![Smontare Data Box archivio dati](media/databox-migration-unmount-datastore.png)

3. Selezionare tutti gli host ESXi in cui è installato l'archivio dati e selezionare **OK**.

    ![Smontare Data Box archivio dati-selezionare gli host](media/databox-migration-unmount-datastore-select-hosts.png)

4. Esaminare e accettare eventuali avvisi e selezionare **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Preparare Data Box per la restituzione e quindi restituirla

Seguire i passaggi descritti nell'articolo [restituire Azure Data Box e verificare il caricamento dei dati in Azure](../databox/data-box-deploy-picked-up.md) per restituire l'data box. Verificare lo stato della copia dei dati nell'account di archiviazione di Azure. Quando lo stato viene visualizzato come completato, è possibile verificare i dati nell'account di archiviazione di Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiare dati da archiviazione di Azure in una soluzione VMware di Azure

I dati copiati nel dispositivo Data Box saranno disponibili nell'account di archiviazione di Azure dopo che lo stato dell'ordine del Data Box viene visualizzato come completato. Ora i dati possono essere copiati nella soluzione VMware di Azure. I dati nell'account di archiviazione devono essere copiati nell'archivio dati rete VSAN del cloud privato usando il protocollo NFS. 

Prima di tutto, copiare i dati di archiviazione BLOB in un disco gestito in una macchina virtuale Linux in Azure usando **AzCopy**. Rendere disponibile il disco gestito tramite NFS, montare la condivisione NFS come archivio dati nel cloud privato e quindi copiare i dati. Questo metodo consente una copia più veloce dei dati nel cloud privato.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copiare i dati nel cloud privato usando una macchina virtuale Linux e dischi gestiti, quindi esportare come condivisione NFS

1. Creare una [macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md) in Azure nella stessa area in cui è stato creato l'account di archiviazione e con una connessione di rete virtuale di Azure al cloud privato.

2. Creare un disco gestito con capacità di archiviazione superiore alla quantità di dati BLOB e [collegarlo alla macchina virtuale Linux](../virtual-machines/linux/attach-disk-portal.md).  Se la quantità di dati BLOB è maggiore della capacità del disco gestito più grande disponibile, i dati devono essere copiati in più passaggi o usando più dischi gestiti.

3. Connettersi alla macchina virtuale Linux e montare il disco gestito.

4. Installare [AzCopy nella macchina virtuale Linux](../storage/common/storage-use-azcopy-v10.md).

5. Scaricare i dati dall'archiviazione BLOB di Azure nel disco gestito usando AzCopy.  Sintassi del comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Sostituire `<storage-account-name>` con il nome dell'account di archiviazione di Azure e `<container-name>` con il contenitore che include i dati copiati tramite Data Box.

6. Installare il server NFS nella macchina virtuale Linux:

    - In una distribuzione Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    - In una distribuzione Enterprise Linux: `sudo yum install nfs-utils`.

7. Modificare l'autorizzazione della cartella nel disco gestito in cui sono stati copiati i dati dall'archivio BLOB di Azure.  Modificare le autorizzazioni per tutte le cartelle che si desidera esportare come una condivisione NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Assegnare le autorizzazioni per gli indirizzi IP del client per accedere alla condivisione NFS modificando il file di `/etc/exports`.

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

9. Esportare le condivisioni NFS usando il comando `sudo exportfs -a`.

10. Riavviare il server kernel NFS usando il comando `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montare la condivisione NFS della macchina virtuale Linux come archivio dati in un cluster vCenter del cloud privato e quindi copiare i dati

La condivisione NFS dalla macchina virtuale Linux deve essere montata come archivio dati nel cluster vCenter del cloud privato. Dopo che è stato montato, i dati possono essere copiati dall'archivio dati NFS nell'archivio dati rete VSAN del cloud privato.

1. Accedere al server vCenter del cloud privato.

2. Fare clic con il pulsante destro del mouse su **datacenter**, scegliere **archiviazione**, selezionare **nuovo archivio dati**, quindi selezionare **Avanti**.

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare il tipo di **NFS** .

   ![Aggiungi nuovo tipo di archivio dati](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione NFS e quindi fare clic su **Avanti**.

   ![Aggiungi nuovo archivio dati-versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome dell'archivio dati, il percorso e il server.  È possibile usare l'indirizzo IP della macchina virtuale Linux per il server.  Il percorso della cartella sarà nel formato `/<folder>/<subfolder>/`.

   ![Aggiungi nuovo archivio dati-configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si vuole montare l'archivio dati e quindi fare clic su **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati-Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e quindi fare clic su **fine**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Aggiungere le macchine virtuali e i modelli di macchina virtuale da un archivio dati NFS all'inventario

1. Dall'interfaccia utente Web del cloud privato vCenter, passare ad **archiviazione**.  Selezionare un archivio dati NFS della macchina virtuale Linux e quindi selezionare **file**.

    ![Selezionare i file da archivio dati NFS](media/databox-migration-datastore-select-files.png)

2. Selezionare una cartella che contiene una macchina virtuale o un modello di macchina virtuale.  Nel riquadro dei dettagli selezionare un file con estensione VMX per una macchina virtuale o un file con estensione vmtx per un modello di macchina virtuale.

3. Selezionare **Register VM** per registrare la macchina virtuale nel cloud privato vCenter.

    ![Registra macchina virtuale](media/databox-migration-datastore-register-vm.png)

4. Selezionare il Data Center, la cartella e il cluster/pool di risorse in cui si vuole registrare la macchina virtuale.

4. Ripetere i passaggi 3 e 4 per tutte le macchine virtuali e i modelli di macchina virtuale.

5. Passare alla cartella che contiene i file ISO.  Selezionare i file ISO e quindi selezionare **copia per** copiare i file in una cartella nell'archivio dati rete VSAN.

Le macchine virtuali e i modelli di macchina virtuale sono ora disponibili nel cloud privato vCenter. Prima di attivare queste macchine virtuali, è necessario spostarle dall'archivio dati NFS all'archivio dati rete VSAN. È possibile usare l'opzione **Storage vMotion** e selezionare l'archivio dati rete VSAN come destinazione per le macchine virtuali.

I modelli di macchina virtuale devono essere clonati dall'archivio dati NFS della macchina virtuale Linux all'archivio dati rete VSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Pulire la macchina virtuale Linux

Dopo che tutti i dati sono stati copiati nel cloud privato, è possibile rimuovere l'archivio dati NFS dal cloud privato:

1. Assicurarsi che tutte le macchine virtuali e i modelli vengano spostati e clonati nell'archivio dati rete VSAN.

2. Rimuovere dall'inventario tutti i modelli di macchina virtuale dall'archivio dati NFS.

3. Smontare l'archivio dati della macchina virtuale Linux dal cloud privato vCenter.

4. Eliminare la macchina virtuale e il disco gestito da Azure.

5. Se non si vogliono conservare i dati trasferiti da Data Box nell'account di archiviazione, eliminare l'account di archiviazione di Azure.  
    


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Data Box](../databox/data-box-overview.md).
* Scopri di più sulle diverse opzioni per [la migrazione dei carichi di lavoro nel cloud privato](migrate-workloads.md).
