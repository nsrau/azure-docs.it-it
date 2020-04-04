---
title: Montare una cache HPC di AzureMount an Azure HPC Cache
description: Come connettere i client a un servizio cache HPC di AzureHow to connect clients to an Azure HPC Cache service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657380"
---
# <a name="mount-the-azure-hpc-cache"></a>Montare la cache HPC di Azure

Dopo aver creato la cache, i client `mount` NFS possono accedervi con un semplice comando. Il comando connette un percorso di destinazione di archiviazione specifico nella cache HPC di Azure a una directory locale nel computer client.

Il comando mount è costituito da questi elementi:

* Uno degli indirizzi di montaggio della cache (elencati nella pagina di panoramica della cache)
* Il percorso dello spazio dei nomi virtuale impostato al momento della creazione della destinazione di archiviazioneThe virtual namespace path that you set when you created the storage target
* Percorso locale da utilizzare nel client
* Parametri di comando che ottimizzano il successo di questo tipo di montaggio NFS

La pagina Istruzioni di **montaggio** per la cache raccoglie le informazioni e le opzioni consigliate e crea un prototipo di comando di montaggio che è possibile copiare. Leggere [Utilizzare l'utilità delle istruzioni](#use-the-mount-instructions-utility) di montaggio per i dettagli.

## <a name="prepare-clients"></a>Preparare i clienti

Assicurarsi che i client siano in grado di montare la cache HPC di Azure seguendo le linee guida riportate in questa sezione.

### <a name="provide-network-access"></a>Fornire l'accesso alla rete

I computer client devono disporre dell'accesso di rete alla rete virtuale e alla subnet privata della cache.

Ad esempio, creare macchine virtuali client all'interno della stessa rete virtuale o usare un endpoint, un gateway o un'altra soluzione nella rete virtuale per l'accesso dall'esterno. Tenere presente che la cache deve essere ospitata all'interno della subnet della cache.

### <a name="install-utilities"></a>Utilità di installazione

Installare il software di utilità Linux appropriato per supportare il comando mount NFS:

* Per Red Hat Enterprise Linux o SuSE:`sudo yum install -y nfs-utils`
* Per Ubuntu o Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Creare un percorso localeCreate a local path

Creare un percorso di directory locale in ogni client per connettersi alla cache. Creare un percorso per ogni destinazione di archiviazione che si desidera montare.

Esempio: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Utilizzare l'utilità delle istruzioni di montaggio

Aprire la pagina **Istruzioni di montaggio** dalla sezione Configura della visualizzazione cache nel portale di Azure.Open the Mount instructions page from the **Configure** section of the cache view in the Azure portal.

![Screenshot di un'istanza della cache HPC di Azure nel portale, con la pagina Configura istruzioni > montaggio caricata](media/mount-instructions.png)

La pagina dei comandi di montaggio include informazioni sul processo di montaggio client e sui prerequisiti, oltre ai campi che è possibile utilizzare per creare un comando di montaggio copiabile.

Per utilizzare questa pagina, attenersi alla seguente procedura:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Esaminare i prerequisiti client e installare `mount` le utilità necessarie per utilizzare il comando NFS come descritto in precedenza in [Preparare i client](#prepare-clients).

1. Fase uno di **Montaggio del file system**<!-- label will change --> fornisce un comando di esempio per la creazione del percorso locale sul client. Questo è il percorso che il client utilizzerà per accedere al contenuto dalla cache HPC di Azure.This is the path that the client will use to access the content from the Azure HPC Cache.

   Prendere nota del nome del percorso in modo da poterlo modificare nel comando, se necessario.

1. Nel passaggio due, selezionare uno degli indirizzi IP disponibili. Tutti i punti di [montaggio client](#find-mount-command-components) della cache sono elencati qui. Assicurarsi di disporre di un sistema per bilanciare il carico tra tutti gli indirizzi IP.

1. Il campo nel passaggio tre viene compilato automaticamente con un comando di montaggio prototipo. Fare clic sul simbolo di copia sul lato destro del campo per copiarlo automaticamente negli Appunti.

   > [!NOTE]
   > Controllare il comando di copia prima di utilizzarlo. Potrebbe essere necessario personalizzare il percorso di montaggio del client e il percorso dello spazio dei nomi virtuale di destinazione dell'archiviazione, che non sono ancora selezionabili in questa interfaccia. È inoltre necessario aggiornare le opzioni del comando di montaggio per riflettere le [opzioni consigliate](#mount-command-options) di seguito. Per informazioni sulla sintassi dei comandi di [montaggio, vedere Informazioni sulla sintassi](#understand-mount-command-syntax) dei comandi di montaggio.

1. Usare il comando mount copiato (con modifiche, se necessario) nel computer client per connetterlo alla destinazione di archiviazione nella cache HPC di Azure.Use the copied mount command (with edits, if needed) on the client machine to connect it to the storage target on the Azure HPC Cache. È possibile eseguire il comando direttamente dalla riga di comando del client o includere il comando mount in uno script o modello di installazione client.

## <a name="understand-mount-command-syntax"></a>Comprendere la sintassi del comando di montaggioUnderstand mount command syntax

Il comando mount ha il seguente formato:

> sudo mount*options* *cache_mount_address*:/*namespace_path* *local_path*

Esempio:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

Dopo che questo comando ha esito positivo, il ``hpccache`` contenuto dell'esportazione di archiviazione sarà visibile nella directory sul client.

### <a name="mount-command-options"></a>Opzioni dei comandi Mount

Per un montaggio client affidabile, passare queste impostazioni e argomenti nel comando mount:For a robust client mount, pass these settings and arguments in your mount command:

> montare -o hard,proto-tcp,mountproto,tcp,retry, 30 CACHE_IP_ADDRESS" : / NAMESPACE_PATH LOCAL_FILESYSTEM_MOUNT_POINT .

| Impostazioni consigliate per i comandi di montaggio | |
--- | ---
``hard`` | I soft mount nella cache HPC di Azure sono associati a errori dell'applicazione e a possibili perdite di dati.
``proto=tcp`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=tcp`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=<value>`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

### <a name="find-mount-command-components"></a>Trovare i componenti del comando di montaggio

Se si desidera creare un comando di montaggio senza usare la pagina **Istruzioni** di montaggio, è possibile trovare gli indirizzi di montaggio nella pagina **Panoramica** cache e i percorsi dello spazio dei nomi virtuali nella pagina **Destinazioni di archiviazione.**

![Screenshot della pagina Panoramica dell'istanza della cache HPC di Azure, con una casella di evidenziazione intorno all'elenco degli indirizzi di montaggio in basso a destra](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Gli indirizzi di montaggio della cache corrispondono alle interfacce di rete all'interno della subnet della cache. In un gruppo di risorse, queste schede `-cluster-nic-` di interfaccia di rete sono elencate con nomi che terminano con e un numero. Non modificare o eliminare queste interfacce o la cache non sarà più disponibile.

I percorsi dello spazio dei nomi virtuali vengono visualizzati nella pagina **Destinazioni di archiviazione.** Fare clic sul nome di una singola destinazione di archiviazione per visualizzarne i dettagli, inclusi i percorsi dello spazio dei nomi aggregati associati.

![Screenshot del pannello di destinazione Archiviazione della cache, con una casella di evidenziazione intorno a una voce nella colonna Percorso della tabella](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Passaggi successivi

* Per spostare i dati nelle destinazioni di archiviazione della cache, vedere Popolare nuova archiviazione BLOB di [Azure.](hpc-cache-ingest.md)
