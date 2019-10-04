---
title: Soluzione VMware di Azure per CloudSimple-migrazione con Azure Data Box
description: Migrazione dei dati in blocco alla soluzione VMware di Azure tramite CloudSimple usando Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817462"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Migrazione dei dati alla soluzione VMware di Azure tramite CloudSimple usando Azure Data Box

La soluzione cloud Microsoft Azure Data Box consente di inviare terabyte di dati ad Azure in modo rapido, economico e affidabile. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo di archiviazione Data Box. Ogni dispositivo di archiviazione ha una capacità di archiviazione massima utilizzabile di 80 TB e viene consegnato al data center dell'utente tramite un corriere locale. Il dispositivo è imballato in una scatola rigida per proteggere i dati durante il trasporto.

Con Azure Data Box, è possibile eseguire la migrazione bulk dei dati VMware nel cloud privato.  I dati dell'ambiente vSphere locale vengono copiati nella Data Box usando il protocollo NFS.  La migrazione dei dati in blocco comporta la copia di una copia temporizzata di macchine virtuali, configurazione e dati associati al Data Box e spediti in Azure.

In questo articolo vengono fornite informazioni su:

* Impostazione Azure Data Box.
* La copia dei dati dall'ambiente VMware locale al Data Box tramite NFS.
* Preparazione per la restituzione di Azure Data Box.
* Preparare i dati BLOB per la copia in una soluzione VMware di Azure con CloudSimple.
* Copia dei dati da Azure nel cloud privato.

## <a name="scenarios"></a>Scenari

Azure Data Box deve essere utilizzato negli scenari seguenti per la migrazione dei dati in blocco.

* Eseguire la migrazione di una grande quantità di dati da una soluzione VMware locale ad Azure CloudSimple per essere usati come base e sincronizzare le differenze sulla rete.
* Eseguire la migrazione di una grande quantità di macchine virtuali spente (macchine virtuali a freddo).
* Eseguire la migrazione dei dati delle macchine virtuali per la configurazione di ambienti di sviluppo e test.
* Eseguire la migrazione di un numero elevato di modelli di macchina virtuale, ISO e dischi di macchine virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

* Verificare i prerequisiti e l' [ordine data box](../databox/data-box-deploy-ordered.md) dalla portale di Azure.  Durante il processo di ordine, è necessario selezionare un account di archiviazione che consenta l'archiviazione BLOB.  Una volta ricevuta la Data Box, connetterla alla rete locale e [configurare la data box](../databox/data-box-deploy-set-up.md) con un indirizzo IP raggiungibile dalla rete di gestione di vSphere.

* Creare una rete virtuale e un account di archiviazione nella stessa area di Azure in cui è stato effettuato il provisioning della soluzione VMware di Azure per CloudSimple.

* Creare una [connessione di rete virtuale di Azure](cloudsimple-azure-network-connection.md) dal cloud privato alla rete virtuale in cui è stato creato l'account di archiviazione usando i passaggi indicati nell'articolo connettere la [rete virtuale di Azure a CloudSimple usando ExpressRoute](virtual-network-connection.md)

## <a name="set-up-azure-data-box-for-nfs"></a>Configurare Azure Data Box per NFS

Connettersi al Azure Data Box interfaccia utente Web locale usando i passaggi indicati nella sezione **connettersi al dispositivo** nell'articolo [Tutorial: Cablare e connettersi alla Azure Data Box @ no__t-0.  Configurare Data Box per consentire l'accesso ai client NFS.

1. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS** fare clic su **Accesso client NFS**. 

    ![Configurare l'accesso dei client NFS 1](media/nfs-client-access.png)

2. Immettere l'indirizzo IP degli host VMware ESXi e fare clic su **Aggiungi**. È possibile configurare l'accesso per tutti gli host nel cluster vSphere ripetendo questo passaggio. Fare clic su **OK**.

    ![Configurare l'accesso dei client NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure le entità di primo livello sono condivisioni, mentre le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Usare i BLOB in blocchi di Azure per la copia dei dati VMware.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Montare la condivisione NFS come archivio dati nel cluster vCenter locale e copiare i dati

La condivisione NFS dal Azure Data Box deve essere montata come archivio dati nel cluster vCenter locale o VMware ESXi host per la copia dei dati.  Una volta montati, i dati possono essere copiati nell'archivio dati NFS.

1. Accedere al server vCenter locale.

2. Fare clic con il pulsante destro del mouse sul **Data Center**, scegliere **archiviazione** Aggiungi fare clic su **nuovo archivio dati** e fare clic su **Avanti** .

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare il tipo **NFS**.

   ![Aggiungi nuovo tipo di archivio dati](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 Selezionare **NFS 3** come versione NFS e fare clic su **Avanti**.

   ![Aggiungi nuovo archivio dati-versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 specificare il nome dell'archivio dati, il percorso e il server.  È possibile usare l'indirizzo IP del Data Box per il server.  Il percorso della cartella sarà nel formato `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Aggiungi nuovo archivio dati-configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 Selezionare gli host ESXi in cui si vuole montare l'archivio dati e fare clic su **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati-Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 esaminare il riepilogo e fare clic su **fine** .

## <a name="copy-data-to-data-box-nfs-datastore"></a>Copiare dati in Data Box archivio dati NFS

È possibile eseguire la migrazione o la clonazione delle macchine virtuali nel nuovo archivio dati.  È possibile eseguire la migrazione di tutte le macchine virtuali non usate, di cui è necessario eseguire la migrazione a Data Box archivio dati NFS mediante **Storage vMotion**.  Le macchine virtuali attive possono essere **clonate** nell'archivio dati NFS data box.

* Identificare l'elenco di macchine virtuali che è possibile **spostare**.
* Identificare l'elenco di macchine virtuali che è necessario **clonare**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Spostare la macchina virtuale nell'archivio dati Data Box

1. Fare clic con il pulsante destro del mouse sulla macchina virtuale che si desidera spostare Data Box archivio dati e selezionare **migrazione**.

    ![Esegui migrazione macchina virtuale](media/databox-migration-vm-migrate.png)

2. Selezionare **modifica solo archiviazione** per tipo di migrazione e fare clic su **Avanti**.

    ![Esegui la migrazione della macchina virtuale-solo archiviazione](media/databox-migration-vm-migrate-change-storage.png)

3. Selezionare Data Box archivio dati come destinazione e fare clic su **Avanti**.

    ![Esegui migrazione macchina virtuale-seleziona Archivio dati](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Esaminare le informazioni e fare clic su **fine**.

5. Ripetere i passaggi da 1 a 4 per le altre macchine virtuali.

> [!TIP]
> È possibile selezionare più macchine virtuali nello stesso stato di alimentazione (acceso o spento) ed eseguirne la migrazione in blocco.

Lo spazio di archiviazione per la macchina virtuale verrà migrato nell'archivio dati NFS da Azure Data Box.  Una volta eseguita la migrazione di tutte le macchine virtuali, è possibile arrestare le macchine virtuali accese in preparazione per la migrazione dei dati alla soluzione VMware di Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Clonare una macchina virtuale o un modello di macchina virtuale per Data Box archivio dati

1. Fare clic con il pulsante destro del mouse su una macchina virtuale o su un modello di macchina virtuale che si desidera clonare.  Selezionare **clona**, **Clona in macchina virtuale**

    ![Clone della macchina virtuale](media/databox-migration-vm-clone.png)

2. Selezionare un nome per la macchina virtuale clonata o il modello di macchina virtuale.

3. Selezionare la cartella in cui si desidera inserire l'oggetto clonato e fare clic su **Avanti**.

4. Selezionare il cluster o il pool di risorse in cui si vuole usare l'oggetto clonato e fare clic su **Avanti**.

5. Selezionare il Azure Data Box archivio dati NFS come percorso di archiviazione e fare clic su **Avanti**.

    ![Clone della macchina virtuale: selezionare l'archivio dati](media/databox-migration-vm-clone-select-datastore.png)

6. Selezionare le opzioni Personalizza se si desidera personalizzare le opzioni per l'oggetto clonato e fare clic su **Avanti**.

7. Esaminare le configurazioni e fare clic su **fine**.

8. Ripetere i passaggi da 1 a 7 per le macchine virtuali aggiuntive o i modelli di macchina virtuale.

Le macchine virtuali verranno clonate e archiviate nell'archivio dati NFS da Azure Data Box.  Una volta clonate le macchine virtuali, assicurarsi che le macchine virtuali clonate siano spente in preparazione per la migrazione dei dati alla soluzione VMware di Azure.

### <a name="copy-iso-files-to-data-box-datastore"></a>Copia i file ISO nell'archivio dati Data Box

1. Dall'interfaccia utente Web di vCenter locale passare alla risorsa di **archiviazione**.  Selezionare Data Box archivio dati NFS e fare clic su **file**.  Creare una **nuova cartella** per l'archiviazione dei file ISO.

    ![Copia ISO-Crea nuova cartella](media/databox-migration-create-folder.png)

2. Assegnare un nome alla cartella in cui verranno archiviati i file ISO.

3. Fare doppio clic sulla cartella appena creata per accedere al contenuto.

4. Fare clic su **Carica file** e selezionare le immagini ISO che si vuole caricare.
    
    ![Copia file ISO-caricamento](media/databox-migration-upload-iso.png)

> [!TIP]
> Se sono già presenti file ISO nell'archivio dati locale, è possibile selezionare i file e **copiarli** nell'archivio dati NFS data box.


## <a name="prepare-azure-data-box-for-return"></a>Prepara Azure Data Box per la restituzione

Quando tutti i dati della macchina virtuale, i dati del modello di macchina virtuale e tutti i file ISO vengono copiati nell'archivio dati NFS di Data Box, l'archivio dati può essere disconnesso da vCenter.  Tutte le macchine virtuali e i modelli di macchina virtuale devono essere rimossi dall'inventario prima di poter disconnettere l'archivio dati.

### <a name="remove-objects-from-inventory"></a>Rimuovi oggetti dall'inventario

1. Dall'interfaccia utente Web di vCenter locale passare alla risorsa di **archiviazione**.  Selezionare Data Box archivio dati NFS e fare clic su **macchine virtuali**.

2. Verificare che tutte le macchine virtuali siano spente.

    ![Rimuovere le macchine virtuali dall'inventario-spento](media/databox-migration-select-databox-vm.png)

3. Selezionare tutte le macchine virtuali, fare clic con il pulsante destro del mouse e scegliere **Rimuovi da inventario**.

    ![Rimuovi le macchine virtuali dall'inventario](media/databox-migration-remove-vm-from-inventory.png)

4. Selezionare **modelli di macchina virtuale in cartelle** dai pulsanti in alto e ripetere il passaggio 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Rimuovere Azure Data Box archivio dati NFS da vCenter

Data Box archivio dati NFS deve essere disconnesso dagli host VMware ESXi prima di prepararsi per la restituzione.

1. Dall'interfaccia utente Web di vCenter locale passare alla risorsa di **archiviazione**.

2. Fare clic con il pulsante destro del mouse sull'archivio dati NFS Data Box e selezionare **smontare archivio dati**.

    ![Smontare Data Box archivio dati](media/databox-migration-unmount-datastore.png)

3. Selezionare tutti gli host ESXi in cui è installato l'archivio dati e fare clic su **OK**.

    ![Smontare Data Box archivio dati-selezionare gli host](media/databox-migration-unmount-datastore-select-hosts.png)

4. Esaminare e accettare eventuali avvisi e fare clic su **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Preparare Data Box per restituire e restituire l'Data Box

Seguire i passaggi descritti nell'articolo [restituire Azure Data Box e verificare il caricamento dei dati in Azure](../databox/data-box-deploy-picked-up.md) per restituire l'data box.  Verificare lo stato della copia dei dati nell'account di archiviazione di Azure e, una volta visualizzato il messaggio stato completato, è possibile verificare i dati nell'account di archiviazione di Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Copiare i dati da archiviazione di Azure alla soluzione VMware di Azure tramite CloudSimple

I dati copiati nel Azure Data Box saranno disponibili nell'account di archiviazione di Azure una volta che lo stato dell'ordine del Data Box viene visualizzato come completato.  È ora possibile copiare i dati nella soluzione VMware di Azure CloudSimple.  I dati nell'account di archiviazione devono essere copiati usando il protocollo NFS nell'archivio dati rete VSAN del cloud privato.  Prima di tutto, copiare i dati dell'archivio BLOB in un disco gestito in una macchina virtuale Linux in Azure usando **AzCopy**.  Rendere disponibile il disco gestito tramite il protocollo NFS e montare la condivisione NFS come archivio dati nel cloud privato e copiare i dati.  Questo metodo consente una copia più veloce dei dati nel cloud privato. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Copiare i dati nel cloud privato usando una macchina virtuale Linux e i dischi gestiti ed esportare come condivisione NFS

1. Creare una [macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md) in Azure nella stessa area in cui è stato creato l'account di archiviazione e con una connessione di rete virtuale di Azure al cloud privato.

2. Creare un disco gestito, che è più grande della quantità di dati BLOB e [collegarlo alla macchina virtuale Linux](../virtual-machines/linux/attach-disk-portal.md).  Se la quantità di dati BLOB è superiore al disco gestito più grande disponibile, i dati devono essere copiati in più passaggi o usando più dischi gestiti.

3. Connettersi alla macchina virtuale Linux e montare il disco gestito.

4. Installare [AzCopy nella macchina virtuale Linux](../storage/common/storage-use-azcopy-v10.md).

5. Scaricare i dati dall'archiviazione BLOB di Azure al disco gestito usando AzCopy.  Sintassi del comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Sostituire `<storage-account-name>` con il nome dell'account di archiviazione di Azure e `<container-name>` con il contenitore, che contiene i dati copiati con Azure Data Box.

6. Installare il server NFS nella macchina virtuale Linux.

    1. In una distribuzione Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    2. In una distribuzione Enterprise Linux: `sudo yum install nfs-utils`.

7. Modificare l'autorizzazione della cartella nel disco gestito in cui sono stati copiati i dati dall'archivio BLOB di Azure.  Modificare le autorizzazioni per tutte le cartelle che si desidera esportare come condivisione NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Assegnare le autorizzazioni per gli indirizzi IP del client per accedere alla condivisione NFS modificando il file `/etc/exports`.

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

10. Riavviare il server kernel NFS utilizzando il comando `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Montare la condivisione NFS della macchina virtuale Linux come archivio dati nel cluster vCenter del cloud privato e copiare i dati

La condivisione NFS dalla macchina virtuale Linux deve essere montata come archivio dati nel cluster vCenter del cloud privato per la copia dei dati.  Una volta montati, i dati possono essere copiati dall'archivio dati NFS al cloud privato rete VSAN datastore.

1. Accedere al server vCenter del cloud privato.

2. Fare clic con il pulsante destro del mouse sul **Data Center**, scegliere **archiviazione** Aggiungi fare clic su **nuovo archivio dati** e fare clic su **Avanti** .

   ![Aggiungi nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare il tipo **NFS**.

   ![Aggiungi nuovo tipo di archivio dati](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 Selezionare **NFS 3** come versione NFS e fare clic su **Avanti**.

   ![Aggiungi nuovo archivio dati-versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 specificare il nome dell'archivio dati, il percorso e il server.  È possibile usare l'indirizzo IP della macchina virtuale Linux per il server.  Il percorso della cartella sarà nel formato `/<folder>/<subfolder>/`.

   ![Aggiungi nuovo archivio dati-configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 Selezionare gli host ESXi in cui si vuole montare l'archivio dati e fare clic su **Avanti**.  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungi nuovo archivio dati-Seleziona host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 esaminare il riepilogo e fare clic su **fine** .

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Aggiungere le macchine virtuali e i modelli di macchina virtuale dall'archivio dati NFS all'inventario

1. Dal cloud privato interfaccia utente Web di vCenter, passare ad **archiviazione**.  Selezionare l'archivio dati NFS della macchina virtuale Linux e fare clic su **file**.

    ![Selezionare i file da archivio dati NFS](media/databox-migration-datastore-select-files.png)

2. Selezionare una cartella che contiene una macchina virtuale o un modello di macchina virtuale.  Dal riquadro dei dettagli selezionare il file `.vmx` per una macchina virtuale o un file `.vmtx` per un modello di macchina virtuale.

3. Fare clic su **registra** macchina virtuale per registrare la macchina virtuale nel cloud privato vCenter.

    ![Registra macchina virtuale](media/databox-migration-datastore-register-vm.png)

4. Selezionare il Data Center, la cartella e il cluster/pool di risorse in cui si vuole registrare la macchina virtuale.

4. Ripetere i passaggi 3 e 4 per tutte le macchine virtuali e i modelli di macchina virtuale.

5. Passare alla cartella che contiene i file ISO.  Selezionare file ISO e **copiarli in** una cartella nell'archivio dati rete VSAN.

Le macchine virtuali e i modelli di macchina virtuale sono ora disponibili nel cloud privato vCenter.  Queste macchine virtuali devono essere spostate dall'archivio dati NFS all'archivio dati rete VSAN prima di accenderle.  È possibile eseguire un vMotion di archiviazione delle macchine virtuali e selezionare rete VSAN datastore come destinazione della macchina virtuale.

I modelli di macchina virtuale devono essere clonati dall'archivio dati NFS della macchina virtuale Linux all'archivio dati rete VSAN.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Pulire la macchina virtuale Linux

Una volta che tutti i dati sono stati copiati nel cloud privato, è possibile rimuovere l'archivio dati NFS dal cloud privato.

1. Assicurarsi che tutte le macchine virtuali e i modelli vengano spostati e clonati nell'archivio dati rete VSAN.

2. Rimuovere tutti i modelli di macchina virtuale dall'archivio dati NFS dall'inventario.

3. Smontare l'archivio dati della macchina virtuale Linux dal cloud privato vCenter.

4. Eliminare la macchina virtuale e il disco gestito da Azure.

5. Se non si vuole che i dati vengano trasferiti con Azure Data Box nell'account di archiviazione, eliminare l'account di archiviazione di Azure.  
    


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Data Box](../databox/data-box-overview.md)
* Scopri di più sulle diverse opzioni per la [migrazione dei carichi di lavoro nel cloud privato](migrate-workloads.md)
