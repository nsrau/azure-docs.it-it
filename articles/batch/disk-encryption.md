---
title: Creare un pool con la crittografia disco abilitata
description: Informazioni su come usare la configurazione della crittografia del disco per crittografare i nodi con una chiave gestita dalla piattaforma.
author: pkshultz
ms.topic: how-to
ms.date: 08/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 9b0f7f9963ee0edd3986f7ec808a8a4060d857f8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267046"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Creare un pool con la crittografia disco abilitata

Quando si crea un pool di Azure Batch usando la configurazione della macchina virtuale, è possibile crittografare i nodi di calcolo nel pool con una chiave gestita dalla piattaforma specificando la configurazione della crittografia del disco.

Questo articolo illustra come creare un pool di batch con la crittografia del disco abilitata.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Perché usare un pool con la configurazione della crittografia del disco?

Con un pool di batch, è possibile accedere e archiviare i dati nel sistema operativo e nei dischi temporanei del nodo di calcolo. La crittografia del disco sul lato server con una chiave gestita dalla piattaforma proteggerà questi dati con un sovraccarico e una convenienza ridotti.  

Batch applicherà una di queste tecnologie di crittografia del disco nei nodi di calcolo, in base alla configurazione del pool e al supporto tecnico regionale.

- [Crittografia dei dischi gestiti inattivi con chiavi gestite dalla piattaforma](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [Crittografia nell'host mediante una chiave gestita dalla piattaforma](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

> [!IMPORTANT]
> Il supporto per la crittografia nell'host con una chiave gestita dalla piattaforma in Azure Batch è attualmente disponibile in anteprima pubblica per le aree Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, US Gov Virginia e US Gov Arizona.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Non sarà possibile specificare quale metodo di crittografia verrà applicato ai nodi nel pool. Al contrario, si forniscono i dischi di destinazione che si vuole crittografare sui rispettivi nodi e batch può scegliere il metodo di crittografia appropriato, assicurando che i dischi specificati siano crittografati nel nodo di calcolo.
 
## <a name="azure-portal"></a>Portale di Azure 

Quando si crea un pool di batch nella portale di Azure, selezionare **TemporaryDisk** o **OsAndTemporaryDisk** in **Configurazione crittografia dischi**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Screenshot dell'opzione di configurazione della crittografia del disco nel portale di Azure.":::

Dopo aver creato il pool, è possibile visualizzare le destinazioni di configurazione della crittografia del disco nella sezione **Proprietà** del pool.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Screenshot che mostra le destinazioni di configurazione della crittografia del disco nel portale di Azure.":::

## <a name="examples"></a>Esempi

Gli esempi seguenti illustrano come crittografare il sistema operativo e i dischi temporanei in un pool di batch usando batch .NET SDK, l'API REST di batch e l'interfaccia della riga di comando di Azure.

### <a name="batch-net-sdk"></a>SDK di .NET per Batch

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>API Batch REST

URL DELL'API REST:
```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```
Corpo della richiesta:
```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [crittografia lato server dei archiviazione su disco di Azure](../virtual-machines/windows/disk-encryption.md).
- Per una panoramica approfondita di Batch, vedere [Flusso di lavoro e risorse del servizio Batch](batch-service-workflow-features.md).
