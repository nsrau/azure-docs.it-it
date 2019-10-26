---
title: Usare la cache HPC di Azure (anteprima) e Azure NetApp Files
description: Come usare la cache HPC di Azure per migliorare l'accesso ai dati archiviati con Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: rohogue
ms.openlocfilehash: 35d7a11c4f39f15c6b2f904df77b88b85c2208e5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954942"
---
# <a name="use-azure-hpc-cache-preview-with-azure-netapp-files"></a>Usare la cache HPC di Azure (anteprima) con Azure NetApp Files

È possibile usare [Azure NetApp files](https://azure.microsoft.com/services/netapp/) come destinazione di archiviazione per la cache HPC di Azure. Questo articolo illustra il modo in cui i due servizi possono interagire tra loro e fornisce suggerimenti per la loro configurazione.

Azure NetApp Files combina il sistema operativo ONTAP con la scalabilità e la velocità di Microsoft Azure. Questa combinazione consente agli utenti di spostare i flussi di lavoro stabiliti nel cloud senza riscrivere il codice.

L'aggiunta di un componente della cache HPC di Azure può migliorare l'accesso ai file presentando più volumi Azure NetApp Files in uno spazio dei nomi aggregato. Può fornire la memorizzazione nella cache perimetrale per i volumi che si trovano in un'area diversa del servizio. Consente inoltre di migliorare le prestazioni su richiesta per i volumi creati a livelli di servizio di livello inferiore per ridurre i costi.

## <a name="overview"></a>Panoramica

Per usare un sistema di Azure NetApp Files come archiviazione back-end con la cache HPC di Azure, seguire questa procedura.

1. Creare il sistema Azure NetApp Files e i volumi in base alle indicazioni fornite in [pianificare il sistema, più avanti](#plan-your-azure-netapp-files-system).
1. Creare la cache HPC di Azure nell'area in cui è necessario l'accesso ai file. Usare le istruzioni in [creare una cache HPC di Azure](hpc-cache-create.md).
1. [Definire le destinazioni di archiviazione](#create-storage-targets-in-the-cache) nella cache che puntano ai volumi Azure NetApp files. Creare una destinazione di archiviazione della cache per ogni indirizzo IP univoco usato per accedere ai volumi.
1. Consentire ai client di [montare la cache HPC di Azure](#mount-storage-targets) anziché montare direttamente Azure NetApp files volumi.

## <a name="plan-your-azure-netapp-files-system"></a>Pianificare il sistema di Azure NetApp Files

Quando si pianifica il sistema di Azure NetApp Files, prestare attenzione agli elementi in questa sezione per assicurarsi che sia possibile integrarli senza problemi con la cache HPC di Azure.

Leggere anche la [documentazione di Azure NetApp files](../azure-netapp-files/index.yml) prima di creare volumi da usare con la cache HPC di Azure.

### <a name="nfs-client-access-only"></a>Solo accesso client NFS

La cache HPC di Azure attualmente supporta solo l'accesso NFS. Non può essere usato con i volumi di bit SMB o POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Subnet esclusiva per Azure NetApp Files

Azure NetApp Files usa una singola subnet delegata per i volumi. Nessun'altra risorsa può utilizzare tale subnet. È inoltre possibile utilizzare una sola subnet in una rete virtuale per Azure NetApp Files. Per altre informazioni, vedere [linee guida per la pianificazione di Azure NetApp files rete](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Dimensioni subnet delegate

Usare la dimensione minima per la subnet delegata durante la creazione di un sistema di Azure NetApp Files per l'uso con la cache HPC di Azure.

La dimensione minima, specificata con netmask/28, fornisce 16 indirizzi IP. In pratica, Azure NetApp Files utilizza solo tre di questi indirizzi IP disponibili per l'accesso al volume. Ciò significa che è necessario creare solo tre destinazioni di archiviazione nella cache HPC di Azure per coprire tutti i volumi.

Se la subnet delegata è troppo grande, è possibile che i volumi Azure NetApp Files usino più indirizzi IP di quelli che possono essere gestiti da una singola istanza di cache HPC di Azure. Una singola cache può avere al massimo dieci destinazioni di archiviazione.

Nell'esempio di Guida introduttiva in Azure NetApp Files documentazione viene usato 10.7.0.0/16 per la subnet delegata, che fornisce una subnet troppo grande.

### <a name="capacity-pool-service-level"></a>Livello di servizio del pool di capacità

Quando si sceglie il livello di servizio per il pool di capacità, prendere in considerazione il flusso di lavoro. Se si riscrivono spesso i dati nel volume Azure NetApp Files, le prestazioni della cache possono essere limitate se il tempo di writeback è lento. Scegliere un livello di servizio elevato per i volumi che avranno scritture frequenti.

Anche i volumi con livelli di servizio bassi potrebbero indicare un ritardo all'inizio di un'attività mentre la cache precompila il contenuto. Quando la cache è attiva e in esecuzione con un working set valido di file, il ritardo dovrebbe diventare inosservabile.

È importante pianificare il livello di servizio del pool di capacità in anticipo, perché non può essere modificato dopo la creazione. È necessario creare un nuovo volume in un pool di capacità diverso e i dati copiati sopra.

Si noti che è possibile modificare la quota di archiviazione di un volume e le dimensioni del pool di capacità senza compromettere l'accesso.

## <a name="create-storage-targets-in-the-cache"></a>Creare destinazioni di archiviazione nella cache

Dopo la configurazione del sistema di Azure NetApp Files e la creazione della cache HPC di Azure, definire le destinazioni di archiviazione nella cache che puntano ai volumi del file system.

Creare una destinazione di archiviazione per ogni indirizzo IP usato dai volumi Azure NetApp Files. L'indirizzo IP è elencato nella pagina istruzioni di montaggio del volume.

Se più volumi condividono lo stesso indirizzo IP, è possibile usare una destinazione di archiviazione per tutti i volumi.  

Per trovare gli indirizzi IP da usare, seguire le istruzioni di montaggio riportate [nella documentazione di Azure NetApp files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .

È anche possibile trovare gli indirizzi IP con l'interfaccia della riga di comando di Azure:

```bash
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

I nomi delle esportazioni nel sistema Azure NetApp Files hanno un singolo componente del percorso. Non tentare di creare una destinazione di archiviazione per l'esportazione radice ``/`` in Azure NetApp Files, perché tale esportazione non fornisce l'accesso ai file.

Non esistono restrizioni speciali sui percorsi dello spazio dei nomi virtuali per queste destinazioni di archiviazione.

## <a name="mount-storage-targets"></a>Monta destinazioni di archiviazione

I computer client devono montare la cache anziché montare direttamente i volumi Azure NetApp Files. Seguire le istruzioni riportate in [montare la cache HPC di Azure](hpc-cache-mount.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla configurazione e l'uso di [Azure NetApp files](../azure-netapp-files/index.yml)
* Per informazioni sulla pianificazione e la configurazione del sistema di cache HPC di Azure per l'uso di Azure NetApp Files, [contattare il supporto tecnico](hpc-cache-support-ticket.md).
