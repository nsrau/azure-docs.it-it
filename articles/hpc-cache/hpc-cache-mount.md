---
title: Montare una cache HPC di AzureMount an Azure HPC Cache
description: Come connettere i client a un servizio cache HPC di AzureHow to connect clients to an Azure HPC Cache service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582225"
---
# <a name="mount-the-azure-hpc-cache"></a>Montare la cache HPC di Azure

Dopo aver creato la cache, i client NFS possono accedervi con un semplice comando di montaggio.

Il comando mount è costituito da due elementi:

* Uno degli indirizzi di montaggio della cache (elencati nella pagina di panoramica della cache)
* Il percorso dello spazio dei nomi virtuale impostato al momento della creazione della destinazione di archiviazioneThe virtual namespace path that you set when you created the storage target

![Screenshot della pagina Panoramica dell'istanza della cache HPC di Azure, con una casella di evidenziazione intorno all'elenco degli indirizzi di montaggio in basso a destra](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Gli indirizzi di montaggio della cache corrispondono alle interfacce di rete all'interno della subnet della cache. In un gruppo di risorse, queste schede `-cluster-nic-` di interfaccia di rete sono elencate con nomi che terminano con e un numero. Non modificare o eliminare queste interfacce o la cache non sarà più disponibile.

I percorsi dello spazio dei nomi virtuali vengono visualizzati nella pagina **Destinazioni di archiviazione.** Fare clic sul nome di una singola destinazione di archiviazione per visualizzarne i dettagli, inclusi i percorsi dello spazio dei nomi aggregati associati.

![Screenshot del pannello di destinazione Archiviazione della cache, con una casella di evidenziazione intorno a una voce nella colonna Percorso della tabella](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Sintassi dei comandi di montaggio

Utilizzare un comando di montaggio simile al seguente:

> sudo mount*options* *cache_mount_address*:/*namespace_path* *local_path*

Esempio:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Dopo che questo comando ha esito positivo, il ``hpccache`` contenuto dell'esportazione di archiviazione deve essere visibile nella directory sul client.

> [!NOTE] 
> I client devono essere in grado di accedere alla rete virtuale e alla subnet che ospita la cache. Ad esempio, creare macchine virtuali client all'interno della stessa rete virtuale o usare un endpoint, un gateway o un'altra soluzione nella rete virtuale per l'accesso dall'esterno. Tenere presente che nessun altro può essere ospitato all'interno della subnet della cache.

### <a name="mount-command-options"></a>Opzioni dei comandi Mount

Per un montaggio client affidabile, passare queste impostazioni e argomenti nel comando mount:For a robust client mount, pass these settings and arguments in your mount command: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Impostazioni consigliate per i comandi di montaggio | |
--- | --- 
``hard`` | I soft mount nella cache HPC di Azure sono associati a errori dell'applicazione e a possibili perdite di dati. 
``proto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete NFS.
``mountproto=netid`` | Questa opzione supporta la gestione appropriata degli errori di rete per le operazioni di montaggio.
``retry=n`` | Impostare ``retry=30`` per evitare errori di montaggio temporanei (per i montaggi in primo piano è consigliato un valore diverso).

## <a name="next-steps"></a>Passaggi successivi

* Per spostare i dati nelle destinazioni di archiviazione della cache, vedere Popolare nuova archiviazione BLOB di [Azure.](hpc-cache-ingest.md)
