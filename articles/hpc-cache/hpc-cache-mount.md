---
title: Montare una cache HPC di Azure
description: Come connettere i client a un servizio cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775351"
---
# <a name="mount-the-azure-hpc-cache"></a>Montare la cache HPC di Azure

Dopo la creazione della cache, i client NFS possono accedervi con un semplice comando di montaggio.

Usare gli indirizzi di montaggio elencati nella pagina Panoramica della cache e il percorso dello spazio dei nomi virtuale impostato al momento della creazione della destinazione di archiviazione. 

![screenshot della pagina Panoramica dell'istanza di cache HPC di Azure, con una casella di evidenziazione intorno all'elenco degli indirizzi di montaggio in basso a destra](media/mount-addresses.png)

> [!NOTE] 
> Gli indirizzi di montaggio della cache corrispondono alle interfacce di rete all'interno della subnet della cache. Queste schede di rete vengono visualizzate nel gruppo di risorse con `-cluster-nic-` i nomi che terminano con e un numero. Non modificare o eliminare queste interfacce oppure la cache non sarà più disponibile.

I percorsi dello spazio dei nomi virtuali vengono visualizzati nella pagina **destinazioni di archiviazione** . 
![screenshot del pannello destinazione di archiviazione della cache, con una casella di evidenziazione intorno a una voce nella colonna percorso della tabella](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>Sintassi del comando Mount

Usare un comando di montaggio simile al seguente:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

Esempio: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

Quando il comando ha esito positivo, il contenuto dell'esportazione dell'archiviazione dovrebbe essere visibile ``hpccache`` nella directory del client. 

> [!NOTE] 
> I client devono essere in grado di accedere alla rete virtuale e alla subnet che ospitano la cache. Ad esempio, creare macchine virtuali client all'interno della stessa rete virtuale o usare un endpoint, un gateway o un'altra soluzione nella rete virtuale per l'accesso dall'esterno. Tenere presente che non è possibile ospitare altri elementi all'interno della subnet della cache.

### <a name="mount-command-options"></a>Opzioni di comando di montaggio

Per un montaggio client affidabile, passare le impostazioni e gli argomenti seguenti nel comando di montaggio: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Impostazioni consigliate per il comando di montaggio | |
--- | --- 
``hard`` | A un soft mount al cluster vFXT sono associati errori dell'applicazione e possibili perdite di dati. 
``proto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=n`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

## <a name="next-steps"></a>Passaggi successivi

* Per spostare i dati nelle destinazioni di archiviazione della cache, vedere [popolare nuovo archivio BLOB di Azure](hpc-cache-ingest.md).
