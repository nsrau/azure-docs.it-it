---
title: Montare una cache HPC di AzureMount an Azure HPC Cache
description: Come connettere i client a un servizio cache HPC di AzureHow to connect clients to an Azure HPC Cache service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458375"
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

Creare un percorso di directory locale in ogni client per connettersi alla cache. Creare un percorso per ogni percorso dello spazio dei nomi che si desidera montare.

Esempio: `sudo mkdir -p /mnt/hpc-cache-1/target3`

La pagina Istruzioni di [montaggio](#use-the-mount-instructions-utility) nel portale di Azure include un comando prototipo che è possibile copiare.

Quando si connette il computer client alla cache, questo percorso verrà associato a un percorso dello spazio dei nomi virtuale che rappresenta un'esportazione della destinazione di archiviazione. Creare directory per ognuno dei percorsi dello spazio dei nomi virtuali che verranno utilizzati dal client.

## <a name="use-the-mount-instructions-utility"></a>Utilizzare l'utilità delle istruzioni di montaggio

È possibile usare la pagina **Istruzioni** di montaggio nel portale di Azure per creare un comando di montaggio copiabile. Aprire la pagina dalla sezione **Configura** della visualizzazione cache nel portale.

Prima di utilizzare il comando su un client, assicurarsi che il client `mount` soddisfi i prerequisiti e disponga del software necessario per utilizzare il comando NFS come descritto in precedenza in Preparare i [client](#prepare-clients).

![Screenshot di un'istanza della cache HPC di Azure nel portale, con la pagina Configura istruzioni > montaggio caricata](media/mount-instructions.png)

Seguire questa procedura per creare il comando mount.

1. Personalizzare il campo **Percorso client.** Questo campo fornisce un comando di esempio che è possibile utilizzare per creare un percorso locale sul client. Il client accede al contenuto dalla cache HPC di Azure localmente in questa directory.

   Fare clic sul campo e modificare il comando in modo che contenga il nome di directory desiderato. Il nome viene visualizzato alla fine della stringa dopo`sudo mkdir -p`

   ![schermata del campo del percorso client con cursore posizionato alla fine](media/mount-edit-client.png)

   Al termine della modifica del campo, il comando mount nella parte inferiore della pagina viene aggiornato con il nuovo percorso client.

1. Scegliere l'indirizzo di **montaggio Cache** dall'elenco. Questo menu elenca tutti i punti di [montaggio client](#find-mount-command-components)della cache.

   Bilanciare il carico del client tra tutti gli indirizzi di montaggio disponibili per migliorare le prestazioni della cache.

   ![screenshot del campo dell'indirizzo di montaggio della cache, con il selettore che mostra tre indirizzi IP tra cui scegliere](media/mount-select-ip.png)

1. Scegliere il **percorso dello spazio dei nomi virtuale** da utilizzare per il client. Questi percorsi si collegano alle esportazioni nel sistema di archiviazione back-end.

   ![schermata del campo dei percorsi dello spazio dei nomi, con il selettore aperto](media/mount-select-target.png)

   È possibile visualizzare e modificare i percorsi dello spazio dei nomi virtuali nella pagina del portale Destinazioni di archiviazione. Leggere [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) per vedere come.

   Per altre informazioni sulla funzionalità dello spazio dei nomi aggregato della cache HPC di Azure, vedere [Pianificare lo spazio dei nomi aggregato.](hpc-cache-namespace.md)

1. Il campo **comando Mount** nel passaggio tre viene compilato automaticamente con un comando mount personalizzato che utilizza l'indirizzo di montaggio, il percorso dello spazio dei nomi virtuale e il percorso client impostati nei campi precedenti.

   Fare clic sul simbolo di copia sul lato destro del campo per copiarlo automaticamente negli Appunti.

   ![schermata del campo dei percorsi dello spazio dei nomi, con il selettore aperto](media/mount-command-copy.png)

1. Usare il comando mount copiato nel computer client per connetterlo alla cache HPC di Azure.Use the copied mount command on the client machine to connect it to the Azure HPC Cache. È possibile eseguire il comando direttamente dalla riga di comando del client o includere il comando mount in uno script o modello di installazione client.

## <a name="understand-mount-command-syntax"></a>Comprendere la sintassi del comando di montaggioUnderstand mount command syntax

Il comando mount ha il seguente formato:

> sudo mount -*opzioni*: *cache_mount_address*:/*namespace_path* *local_path*

Esempio:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
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

Se si desidera creare un comando di montaggio senza usare la pagina **Istruzioni** di montaggio, è possibile trovare gli indirizzi di montaggio nella pagina **Panoramica** cache e i percorsi dello spazio dei nomi virtuali nella pagina **Destinazione archiviazione.**

![Screenshot della pagina Panoramica dell'istanza della cache HPC di Azure, con una casella di evidenziazione intorno all'elenco degli indirizzi di montaggio in basso a destra](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Gli indirizzi di montaggio della cache corrispondono alle interfacce di rete all'interno della subnet della cache. In un gruppo di risorse, queste schede `-cluster-nic-` di interfaccia di rete sono elencate con nomi che terminano con e un numero. Non modificare o eliminare queste interfacce o la cache non sarà più disponibile.

I percorsi dello spazio dei nomi virtuali vengono visualizzati nella pagina dei dettagli di ogni destinazione di archiviazione. Fare clic sul nome di una singola destinazione di archiviazione per visualizzarne i dettagli, inclusi i percorsi dello spazio dei nomi aggregati associati.

![screenshot della pagina dei dettagli di una destinazione di archiviazione (intestazione "Aggiorna destinazione di archiviazione"). È presente una casella di evidenziazione intorno a una voce nella colonna Percorso spazio dei nomi virtuali della tabella](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Passaggi successivi

* Per spostare i dati nelle destinazioni di archiviazione della cache, vedere Popolare nuova archiviazione BLOB di [Azure.](hpc-cache-ingest.md)
