---
title: Usare la cache HPC di Azure e i file netApp di AzureUse Azure HPC Cache and Azure NetApp Files
description: Come usare la cache HPC di Azure per migliorare l'accesso ai dati archiviati con i file di Azure NetAppHow to use Azure HPC Cache to improve access to data stored with Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238688"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Usare la cache HPC di Azure con i file di Azure NetAppUse Azure HPC Cache with Azure NetApp Files

È possibile usare i file di Azure NetApp come destinazione di archiviazione per la cache HPC di Azure.You can use [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) as a storage target for your Azure HPC Cache. Questo articolo spiega come i due servizi possono collaborare e fornisce suggerimenti per la loro configurazione.

File NetApp di Azure combina il sistema operativo ONTAP con la scalabilità e la velocità di Microsoft Azure. Questa combinazione consente agli utenti di spostare i flussi di lavoro stabiliti nel cloud senza riscrivere il codice.

L'aggiunta di un componente Cache HPC di Azure può migliorare l'accesso ai file presentando più volumi di file di Azure NetApp in un unico spazio dei nomi aggregato. Può fornire la memorizzazione nella cache perimetrale per i volumi situati in un'area di servizio diversa. Può inoltre migliorare le prestazioni su richiesta per i volumi creati a livelli di servizio di livello inferiore per risparmiare sui costi.

## <a name="overview"></a>Panoramica

Per usare un sistema di file di Rete di Azure come archivio back-end con la cache HPC di Azure, seguire questo processo.

1. Creare il sistema di file di Rete di Azure e i volumi in base alle indicazioni fornite in [Pianificare il sistema, di seguito.](#plan-your-azure-netapp-files-system)
1. Creare la cache HPC di Azure nell'area in cui è necessario l'accesso ai file. (Usare le istruzioni in Creare una [cache HPC](hpc-cache-create.md)di Azure.)
1. [Definire le destinazioni di archiviazione](#create-storage-targets-in-the-cache) nella cache che puntano ai volumi di File NetApp di Azure.Define storage targets in the cache that point to the Azure NetApp Files volumes. Creare una destinazione di archiviazione della cache per ogni indirizzo IP univoco utilizzato per accedere ai volumi.
1. Chiedere ai client di montare direttamente la cache HPC di Azure anziché creare direttamente i volumi dei file NetApp di Azure.Have clients mount the [Azure HPC Cache](#mount-storage-targets) instead of mounting Azure NetApp Files volumes directly.

## <a name="plan-your-azure-netapp-files-system"></a>Pianificare il sistema di file netApp di AzurePlan your Azure NetApp Files system

Quando si pianifica il sistema di file di Azure NetApp, prestare attenzione agli elementi in questa sezione per assicurarsi di poterlo integrare senza problemi con la cache HPC di Azure.When planning your Azure NetApp Files system, prest attention to the items in this section to make sure it can integrate it smoothly with Azure HPC Cache.

Leggere anche la documentazione relativa ai [file di Azure NetApp](../azure-netapp-files/index.yml) prima di creare volumi da usare con la cache HPC di Azure.Also read the Azure NetApp Files documentation before creating volumes for use with Azure HPC Cache.

### <a name="nfs-client-access-only"></a>Solo accesso client NFS

La cache HPC di Azure attualmente supporta solo l'accesso NFS. Non può essere utilizzato con volumi di bit in modalità ACL o POSIX SMB.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Subnet esclusiva per i file NetApp di AzureExclusive subnet for Azure NetApp Files

File NetApp di Azure usa una singola subnet delegata per i volumi. Nessun'altra risorsa può usare tale subnet. Inoltre, solo una subnet in una rete virtuale può essere usata per i file NetApp di Azure.Also, only one subnet in a virtual network can be used for Azure NetApp Files. Per altre informazioni, vedere Linee guida per la pianificazione della rete di file NetApp di Azure .Learn more in [Guidelines for Azure NetApp Files network planning](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Dimensioni subnet delegate

Use the minimum size for the delegated subnet when creating an Azure NetApp Files system for use with Azure HPC Cache.

La dimensione minima, specificata con la netmask /28, fornisce 16 indirizzi IP. In pratica, File NetApp di Azure usa solo tre di questi indirizzi IP disponibili per l'accesso al volume. Ciò significa che è sufficiente creare tre destinazioni di archiviazione nella cache HPC di Azure per coprire tutti i volumi.

Se la subnet delegata è troppo grande, è possibile che i volumi di File netApp di Azure utilizzino più indirizzi IP di quelli che può essere gestita da una singola istanza della cache HPC di Azure.If the delegated subnet is too large, it's possible for the Azure NetApp Files volumes to use more IP addresses than a single Azure HPC Cache instance can handle. Una singola cache può avere al massimo dieci destinazioni di archiviazione.

L'esempio della guida introduttiva nella documentazione relativa ai file NetApp di Azure usa 10.7.0.0/16 per la subnet delegata, che fornisce una subnet troppo grande.

### <a name="capacity-pool-service-level"></a>Livello di servizio del pool di capacitàCapacity pool service level

Quando si sceglie il livello di servizio per il pool di capacità, prendere in considerazione il flusso di lavoro. Se si scrivono spesso i dati nel volume File NetApp di Azure, le prestazioni della cache possono essere limitate se il tempo di writeback è lento. Scegliere un livello di servizio elevato per i volumi con scritture frequenti.

I volumi con livelli di servizio bassi potrebbero anche mostrare un certo ritardo all'inizio di un'attività, mentre la cache precompila il contenuto. Dopo che la cache è in esecuzione con un buon working set di file, il ritardo dovrebbe diventare impercettibile.

È importante pianificare il livello di servizio del pool di capacità in anticipo, perché non può essere modificato dopo la creazione. È necessario creare un nuovo volume in un pool di capacità diverso e copiare i dati.

Si noti che è possibile modificare la quota di archiviazione di un volume e le dimensioni del pool di capacità senza interrompere l'accesso.

## <a name="create-storage-targets-in-the-cache"></a>Creare destinazioni di archiviazione nella cacheCreate storage targets in the cache

Dopo aver configurato il sistema di file di Azure NetApp e aver creato la cache HPC di Azure, definire le destinazioni di archiviazione nella cache che puntano ai volumi del file system.

Creare una destinazione di archiviazione per ogni indirizzo IP usato dai volumi di File NetApp di Azure.Create one storage target for each IP address used by your Azure NetApp Files volumes. L'indirizzo IP è elencato nella pagina delle istruzioni di montaggio del volume.

Se più volumi condividono lo stesso indirizzo IP, è possibile usare una destinazione di archiviazione per tutti.  

Seguire le istruzioni di [montaggio nella documentazione relativa](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) ai file NetApp di Azure per trovare gli indirizzi IP da usare.

È anche possibile trovare gli indirizzi IP con l'interfaccia della riga di comando di Azure:You can also find IP addresses with the Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

I nomi di esportazione nel sistema dei file di Rete di Azure dispongono di un singolo componente per corsi. Non tentare di creare una destinazione ``/`` di archiviazione per l'esportazione radice nei file NetApp di Azure, perché tale esportazione non fornisce l'accesso ai file.

Non esistono restrizioni speciali sui percorsi dello spazio dei nomi virtuali per queste destinazioni di archiviazione.

## <a name="mount-storage-targets"></a>Montare gli obiettivi di archiviazione

I computer client devono montare la cache anziché montare direttamente i volumi di File NetApp di Azure.Client machines should mount the cache instead of mounting the Azure NetApp Files volumes directly. Seguire le istruzioni in [Montare la cache HPC](hpc-cache-mount.md)di Azure .

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla configurazione e l'uso dei file NetApp di AzureRead more about setting up and using [Azure NetApp Files](../azure-netapp-files/index.yml)
* Per informazioni sulla pianificazione e la configurazione del sistema di cache HPC di Azure per l'uso dei file netApp di Azure, [contattare il supporto tecnico](hpc-cache-support-ticket.md).
