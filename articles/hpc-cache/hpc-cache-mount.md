---
title: Montare una cache HPC di Azure
description: Come connettere i client a un servizio cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651429"
---
# <a name="mount-the-azure-hpc-cache"></a>Montare la cache HPC di Azure

Dopo la creazione della cache, i client NFS possono accedervi con un semplice `mount` comando. Il comando connette un percorso di destinazione di archiviazione specifico nella cache HPC di Azure a una directory locale nel computer client.

Il comando di montaggio è costituito da questi elementi:

* Uno degli indirizzi di montaggio della cache (elencati nella pagina Panoramica della cache)
* Un percorso dello spazio dei nomi virtuale impostato per la destinazione di archiviazione (elencato nella pagina spazio dei nomi della cache)
* Percorso locale da usare nel client
* Parametri di comando che ottimizzano la riuscita di questo tipo di montaggio NFS

La pagina **istruzioni di montaggio** per la cache raccoglie le informazioni e le opzioni consigliate per l'utente e crea un comando di montaggio prototipo che è possibile copiare. Per informazioni dettagliate, vedere [usare l'utilità istruzioni di montaggio](#use-the-mount-instructions-utility) .

## <a name="prepare-clients"></a>Preparare i client

Assicurarsi che i client siano in grado di montare la cache HPC di Azure seguendo le istruzioni riportate in questa sezione.

### <a name="provide-network-access"></a>Fornire l'accesso alla rete

I computer client devono avere accesso di rete alla rete virtuale e alla subnet privata della cache.

Ad esempio, creare macchine virtuali client all'interno della stessa rete virtuale o usare un endpoint, un gateway o un'altra soluzione nella rete virtuale per l'accesso dall'esterno. Tenere presente che solo la cache deve essere ospitata all'interno della subnet della cache.

### <a name="install-utilities"></a>Installare le utilità

Installare il software di utilità Linux appropriato per supportare il comando di montaggio NFS:

* Per Red Hat Enterprise Linux o SuSE: `sudo yum install -y nfs-utils`
* Per Ubuntu o Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Creare un percorso locale

Creare un percorso di directory locale in ogni client per connettersi alla cache. Creare un percorso per ogni percorso dello spazio dei nomi che si desidera montare.

Esempio: `sudo mkdir -p /mnt/hpc-cache-1/target3`

La pagina [istruzioni di montaggio](#use-the-mount-instructions-utility) nella portale di Azure include un comando prototipo che è possibile copiare.

Quando si connette il computer client alla cache, questo percorso viene associato a un percorso dello spazio dei nomi virtuale che rappresenta un'esportazione di destinazione di archiviazione. Creare directory per ognuno dei percorsi dello spazio dei nomi virtuali che il client userà.

## <a name="use-the-mount-instructions-utility"></a>Usare l'utilità istruzioni di montaggio

È possibile usare la pagina **istruzioni di montaggio** nella portale di Azure per creare un comando di montaggio copiabile. Aprire la pagina dalla sezione **Configura** della visualizzazione cache nel portale.

Prima di usare il comando in un client, verificare che il client soddisfi i prerequisiti e disponga del software necessario per usare il `mount` comando NFS come descritto in [preparare i client](#prepare-clients).

![Screenshot di un'istanza di cache HPC di Azure nel portale, con la pagina Configura istruzioni di montaggio > caricata](media/mount-instructions.png)

Seguire questa procedura per creare il comando di montaggio.

1. Personalizzare il campo **percorso client** . Questo campo fornisce un comando di esempio che è possibile usare per creare un percorso locale nel client. Il client accede al contenuto dalla cache HPC di Azure in locale in questa directory.

   Fare clic sul campo e modificare il comando in modo che contenga il nome della directory desiderata. Il nome viene visualizzato alla fine della stringa dopo `sudo mkdir -p`

   ![screenshot del campo percorso client con cursore posizionato alla fine](media/mount-edit-client.png)

   Al termine della modifica del campo, il comando di montaggio nella parte inferiore della pagina viene aggiornato con il nuovo percorso client.

1. Scegliere l' **indirizzo di montaggio della cache** dall'elenco. Questo menu elenca tutti i [punti di montaggio client](#find-mount-command-components)della cache.

   Bilanciare il carico del client tra tutti gli indirizzi di montaggio disponibili per migliorare le prestazioni della cache.

   ![screenshot del campo relativo all'indirizzo di montaggio della cache, con selettore che mostra tre indirizzi IP tra cui scegliere](media/mount-select-ip.png)

1. Scegliere il **percorso dello spazio dei nomi virtuale** da usare per il client. Questi percorsi sono collegati alle esportazioni nel sistema di archiviazione back-end.

   ![Screenshot che mostra il campo "percorso spazio dei nomi virtuale" con selettore aperto.](media/mount-select-target.png)

   È possibile visualizzare e modificare i percorsi dello spazio dei nomi virtuali nella pagina del portale **dello spazio dei nomi** . Leggere [configurare lo spazio dei nomi aggregato](add-namespace-paths.md) per vedere come.

   Per altre informazioni sulla funzionalità spazio dei nomi aggregato della cache HPC di Azure, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md).

1. Il campo del **comando di montaggio** nel passaggio 3 viene popolato automaticamente con un comando di montaggio personalizzato che usa l'indirizzo di montaggio, il percorso dello spazio dei nomi virtuale e il percorso client impostati nei campi precedenti.

   Fare clic sul simbolo di copia sul lato destro del campo per copiarlo automaticamente negli Appunti.

   ![screenshot del campo del comando di montaggio del prototipo che mostra il testo del passaggio del mouse per il pulsante "copia negli Appunti"](media/mount-command-copy.png)

1. Usare il comando di montaggio copiato nel computer client per connetterlo alla cache HPC di Azure. È possibile eseguire il comando direttamente dalla riga di comando del client o includere il comando di montaggio in uno script o un modello di installazione client.

## <a name="understand-mount-command-syntax"></a>Informazioni sulla sintassi del comando di montaggio

Il comando di montaggio ha il formato seguente:

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

Esempio:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Quando il comando ha esito positivo, il contenuto dell'esportazione dell'archiviazione sarà visibile nella ``hpccache`` directory del client.

### <a name="mount-command-options"></a>Opzioni di comando di montaggio

Per un montaggio client affidabile, passare le impostazioni e gli argomenti seguenti nel comando di montaggio:

> Mount-o hard, proto = TCP, mountproto = TCP, Retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Impostazioni consigliate per il comando di montaggio | Description |
--- | ---
``hard`` | I montaggi soft per la cache HPC di Azure sono associati agli errori dell'applicazione e alla possibile perdita di dati.
``proto=tcp`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=tcp`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=<value>`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

### <a name="find-mount-command-components"></a>Trovare i componenti del comando di montaggio

Se si desidera creare un comando di montaggio senza utilizzare la pagina **istruzioni di montaggio** , è possibile trovare gli indirizzi di montaggio nella pagina **Panoramica** della cache e i percorsi dello spazio dei nomi virtuali nella pagina **spazio dei nomi** .

![screenshot della pagina Panoramica dell'istanza di cache HPC di Azure, con una casella di evidenziazione intorno all'elenco degli indirizzi di montaggio in basso a destra](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Gli indirizzi di montaggio della cache corrispondono alle interfacce di rete all'interno della subnet della cache. In un gruppo di risorse queste schede di rete sono elencate con nomi che terminano con `-cluster-nic-` e un numero. Non modificare o eliminare queste interfacce oppure la cache non sarà più disponibile.

I percorsi dello spazio dei nomi virtuali vengono visualizzati nella pagina impostazioni **spazio dei nomi** della cache.

![screenshot delle impostazioni del portale > pagina dello spazio dei nomi con una casella di evidenziazione intorno alla prima colonna della tabella: "percorso dello spazio dei nomi"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Passaggi successivi

* Per spostare i dati nelle destinazioni di archiviazione della cache, vedere [popolare nuovo archivio BLOB di Azure](hpc-cache-ingest.md).
