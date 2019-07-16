---
title: Esercitazione per copiare dati da dischi rigidi virtuali in dischi gestiti con Azure Data Box Heavy | Microsoft Docs
description: Informazioni su come copiare in Azure Data Box Heavy i dati di dischi rigidi virtuali relativi a carichi di lavoro di macchine virtuali locali
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a29cd142b3322c958f70aad8d5cad2bc30b87d76
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670832"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Esercitazione: Usare Data Box Heavy per importare dati come dischi gestiti in Azure

Questa esercitazione descrive come usare Azure Data Box Heavy per la migrazione di dischi rigidi virtuali locali in dischi gestiti in Azure. I dischi rigidi virtuali di macchine virtuali locali vengono copiati in Data Box Heavy come BLOB di pagine e caricati in Azure come dischi gestiti. Questi dischi gestiti possono quindi essere collegati a macchine virtuali di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Esaminare i prerequisiti
> * Connettersi a Data Box Heavy
> * Copiare i dati in Data Box Heavy


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Aver ricevuto Data Box Heavy e che lo stato dell'ordine nel portale sia **Recapitato**.
3. Essere connessi a una rete ad alta velocità. Per una velocità di copia più veloce, è possibile utilizzare in parallelo due connessioni da 40 GbE (una per nodo). Se non si dispone di una connessione da 40 GbE, si consiglia di avere almeno due connessioni da 10 GbE (una per nodo). 
4. Aver esaminato quanto segue:

    - Le [dimensioni dei dischi gestiti supportate nei limiti delle dimensioni degli oggetti di Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introduzione ad Azure Managed Disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Connettersi a Data Box Heavy

In base ai gruppi di risorse specificati, Data Box Heavy crea una condivisione per ogni gruppo di risorse associato per nodo. Se ad esempio sono stati creati `mydbmdrg1` e `mydbmdrg2` quando è stato effettuato l'ordine, verranno create le condivisioni seguenti:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

In ogni condivisione vengono create le tre cartelle seguenti, che corrispondono a contenitori nell'account di archiviazione.

- SSD Premium
- HDD Standard
- SSD Standard

La tabella seguente illustra i percorsi UNC delle condivisioni in Data Box Heavy.
 
|        Protocollo di connessione           |             Percorso UNC della condivisione                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

I passaggi per la connessione variano a seconda che venga usato il protocollo SMB o NFS per la connessione alle condivisioni di Data Box Heavy.

> [!NOTE]
> - La connessione tramite REST non è supportata per questa funzionalità.
> - Ripetere le istruzioni di connessione per connettersi al secondo nodo di Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Connettersi a Data Box Heavy tramite SMB

Se si usa un computer host Windows Server, eseguire le operazioni seguenti per connettersi a Data Box Heavy.

1. Innanzitutto è necessario autenticare e avviare una sessione. Selezionare **Connetti e copia**. Fare clic su **Ottieni credenziali** per ottenere le credenziali di accesso per le condivisioni associate al gruppo di risorse. È anche possibile ottenere le credenziali di accesso da **Dettagli dispositivo** nel portale di Azure.

    > [!NOTE]
    > Le credenziali sono le stesse per tutte le condivisioni di dischi gestiti.

    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Nella finestra di dialogo Accedi alla condivisione e copia i dati copiare i valori di **Nome utente** e **Password** per la condivisione. Fare clic su **OK**.
    
    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Per accedere alle condivisioni associate alla risorsa (*mydbmdrg1* nell'esempio seguente) dal computer host, aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    I percorsi di condivisione UNC in questo esempio sono i seguenti:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Quando richiesto, immettere la password della condivisione. L'esempio seguente mostra la connessione a una condivisione tramite il comando precedente.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>\<ShareName>`. Fare clic su **OK** per aprire Esplora file.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Ora dovrebbero essere visibili le cartelle seguenti, create preventivamente in ogni condivisione.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Connettersi a Data Box Heavy tramite NFS

Se si usa un computer host Linux, eseguire la procedura seguente per configurare il dispositivo in modo da consentire l'accesso ai client NFS.

1. Specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS** fare clic su **Accesso client NFS**.

    ![Configurare l'accesso dei client NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Specificare l'indirizzo IP del client NFS e fare clic su **Aggiungi**. Per configurare l'accesso per più client NFS, ripetere questa procedura. Fare clic su **OK**.

    ![Configurare l'accesso dei client NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Verificare che nel computer host Linux sia installata una [versione supportata](data-box-system-requirements.md) del client NFS. Usare la versione specifica della distribuzione Linux in uso.

3. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS nel dispositivo:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione di Data Box o Data Box Heavy. L'indirizzo IP del dispositivo Data Box o Data Box Heavy è `169.254.250.200` e la condivisione `mydbmdrg1_MDisk` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Copiare i dati in Data Box Heavy

Dopo aver stabilito la connessione al server dati, il passaggio successivo consiste nel copiare i dati. Il file VHD viene copiato nell'account di archiviazione di staging come BLOB di pagine. Il BLOB di pagine viene quindi convertito in un disco gestito e spostato in un gruppo di risorse.

Prima di procedere con la copia dei dati, rivedere le considerazioni seguenti:

- Copiare sempre i dischi rigidi virtuali in una delle cartelle create preventivamente. Se vengono copiati al di fuori di queste cartelle o in una cartella creata dall'utente, i dischi rigidi virtuali verranno caricati nell'account di archiviazione di Azure come BLOB di pagine e non come dischi gestiti.
- Per la creazione di dischi gestiti possono essere caricati solo dischi rigidi virtuali a dimensione fissa. I file VHDX o i dischi rigidi virtuali differenziali o dinamici non sono supportati.
- In tutte le cartelle create preventivamente può essere presente un solo disco gestito con un determinato nome in un gruppo di risorse. I dischi rigidi virtuali caricati nelle cartelle create preventivamente devono quindi avere nomi univoci. Verificare che il nome assegnato non corrisponda a un disco gestito già esistente in un gruppo di risorse.
- Esaminare i limiti dei dischi gestiti in [Limiti delle dimensioni degli oggetti di Azure](data-box-heavy-limits.md#azure-object-size-limits).

A seconda che si esegua la connessione tramite SMB o NFS, è possibile usare:

- [Copiare dati tramite SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Copiare dati tramite NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Attendere il completamento dei processi di copia. Prima di procedere con il passaggio successivo, assicurarsi che i processi di copia vengano completati senza errori.

![Nessun errore nella pagina **Connetti e copia**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Se si verificano errori durante il processo di copia, scaricare i log dalla pagina **Connetti e copia**.

- Se è stato copiato un file non allineato su 512 byte, il file non verrà caricato come BLOB di pagine nell'account di archiviazione di staging. Verrà visualizzato un errore nei log. Rimuovere il file e copiare un file allineato su 512 byte.

- Se è stato copiato un file VHDX (formato non supportato) con un nome lungo, verrà visualizzato un errore nei log.

    ![Errore nei log dalla pagina **Connetti e copia**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Risolvere gli errori prima di procedere con il passaggio successivo.

Per assicurare l'integrità dei dati, il checksum viene calcolato inline durante la copia dei dati. Al termine della copia, verificare lo spazio occupato e lo spazio disponibile nel dispositivo.
    
![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Al termine del processo di copia, si può passare a **Prepara per la spedizione**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box Heavy, ad esempio:

> [!div class="checklist"]
> * Esaminare i prerequisiti
> * Connettersi a Data Box Heavy
> * Copiare i dati in Data Box Heavy


Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box Heavy a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box Heavy a Microsoft](./data-box-heavy-deploy-picked-up.md)

