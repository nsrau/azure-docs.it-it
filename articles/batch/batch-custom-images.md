---
title: Usare un'immagine gestita per creare un pool di immagini personalizzato
description: Creare un pool di immagini personalizzate di batch da un'immagine gestita per eseguire il provisioning dei nodi di calcolo con il software e i dati dell'applicazione.
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 45bf0f8b3cb335b7025ff06189bf6bc4e0a896ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85851299"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Usare un'immagine gestita per creare un pool di immagini personalizzato

Per creare un pool di immagini personalizzato per le macchine virtuali (VM) del pool di batch, è possibile usare un'immagine gestita per creare un' [immagine della raccolta di immagini condivise](batch-sig-images.md). È supportato anche l'uso di una sola immagine gestita, ma solo per le versioni API fino alla versione 2019-08-01 inclusa. 

> [!IMPORTANT]
> Nella maggior parte dei casi, è consigliabile creare immagini personalizzate usando la Raccolta immagini condivise. Con la Raccolta immagini condivise è possibile effettuare il provisioning dei pool più velocemente, dimensionare quantità maggiori di macchine virtuali e migliorare l'affidabilità durante il provisioning delle macchine virtuali. Per altre informazioni, vedere [Usare Raccolta immagini condivise per creare un pool personalizzato](batch-sig-images.md).

In questo argomento viene illustrato come creare un pool di immagini personalizzato utilizzando solo un'immagine gestita.

## <a name="prerequisites"></a>Prerequisiti

- **Una risorsa immagine gestita**. Per creare un pool di macchine virtuali usando un'immagine personalizzata, è necessario possedere o creare una risorsa immagine gestita nella stessa sottoscrizione di Azure e nella stessa area dell'account Batch. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati.
  - Usare un'immagine personalizzata univoca per ogni pool che si crea.
  - Per creare un pool con l'immagine usando le API Batch, specificare l'**ID risorsa** dell'immagine, che si presenta nel formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`.
  - La risorsa immagine gestita deve esistere per tutta la durata del pool per consentire il ridimensionamento orizzontale e può essere rimossa dopo che il pool è stato eliminato.

- **Autenticazione con Azure Active Directory (Azure AD)** . L'API client di Batch deve usare l'autenticazione di Azure AD. Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Preparare un'immagine gestita

All'interno di Azure è possibile preparare un'immagine gestita da:

- Snapshot dei dischi dati e sistema operativo di una macchina virtuale di Azure
- Una macchina virtuale generalizzata di Azure con dischi gestiti
- Un disco rigido virtuale locale generalizzato caricato nel cloud

Per dimensionare i pool di Batch in modo affidabile con un'immagine gestita, si consiglia di creare l'immagine gestita usando *solo* il primo modo, ovvero usando gli snapshot dei dischi della macchina virtuale. I passaggi seguenti illustrano come preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine gestita dallo snapshot.

### <a name="prepare-a-vm"></a>Preparare una VM

Se si crea una nuova macchina virtuale per l'immagine, usare un'immagine produttore di Azure Marketplace supportata da Batch come immagine di base per l'immagine gestita. Solo le immagini produttore possono essere usate come immagine di base. Per ottenere un elenco completo di riferimenti a immagini di Azure Marketplace supportate da Azure Batch, vedere l'operazione [List node agent SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms).

- Assicurarsi che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
- Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
- Quando si usano dischi dati allegati è necessario montare e formattare i dischi all'interno di una macchina virtuale per poterli usare.
- Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
- Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). È possibile creare un'immagine specificando uno snapshot del disco del sistema operativo e, facoltativamente, uno o più snapshot dei dischi dati.

## <a name="create-a-pool-from-a-managed-image"></a>Creare un pool da un'immagine gestita

Dopo aver trovato l'ID risorsa dell'immagine gestita, creare un pool di immagini personalizzato da tale immagine. Nei passaggi seguenti viene illustrato come creare un pool di immagini personalizzato usando il servizio Batch o la gestione Batch.

> [!NOTE]
> Assicurarsi che l'identità usata per l'autenticazione di Azure AD disponga delle autorizzazioni per la risorsa immagine. Vedere [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).
>
> La risorsa per l'immagine gestita deve esistere per la durata del pool. Se la risorsa sottostante viene eliminata, non sarà possibile dimensionare il pool.

### <a name="batch-service-net-sdk"></a>.NET SDK del servizio Batch

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
```

### <a name="batch-management-rest-api"></a>API REST di gestione Batch

URI DELL'API REST

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Request Body

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia di macchine virtuali e oltre usando un'immagine personalizzata, è importante seguire le indicazioni precedenti per usare un'immagine creata dallo snapshot di una macchina virtuale.

Tenere presenti anche le seguenti considerazioni:

- **Limiti dimensioni**: Batch limita le dimensioni del pool a 2500 nodi di calcolo dedicati o a 1000 nodi con priorità bassa, quando si usa un'immagine personalizzata.

  Se si usa la stessa immagine (o più immagini basate sullo stesso snapshot) per creare più pool, il numero totale di nodi di calcolo nel pool non può superare i limiti sopra indicati. Non è consigliabile usare un'immagine o il relativo snapshot sottostante per più di un pool.

  I limiti possono essere anche inferiori se si configura il pool con i [pool NAT in ingresso](pool-endpoint-configuration.md).

- **Timeout ridimensionamento**: se il pool contiene un numero fisso di nodi (senza scalabilità automatica), aumentare il valore della proprietà resizeTimeout del pool a un valore come 20-30 minuti. Se il pool non raggiunge la dimensione di destinazione entro il periodo di timeout, eseguire un'altra [operazione di ridimensionamento](/rest/api/batchservice/pool/resize).

  Se si prevede di creare un pool con oltre 300 nodi di calcolo, può essere necessario ridimensionare il pool più volte per raggiungere le dimensioni di destinazione.
  
Con la [Raccolta immagini condivise](batch-sig-images.md) è possibile creare pool di dimensioni maggiori con le immagini personalizzate, insieme a un maggior numero di repliche di Immagini condivise. Con Immagini condivise, il tempo necessario affinché il pool raggiunga lo stato stabile diminuisce del 25% e la latenza di inattività della macchina virtuale si riduce fino al 30%.

## <a name="considerations-for-using-packer"></a>Considerazioni sull'uso di Packer

La creazione di una risorsa immagine gestita direttamente con Packer può essere eseguita solo con account Batch in modalità sottoscrizione utente. Per gli account in modalità servizio Batch, è prima necessario creare un disco rigido virtuale, quindi importare il disco rigido virtuale in una risorsa immagine gestita. A seconda della modalità di allocazione del pool (sottoscrizione utente o servizio Batch), i passaggi per creare una risorsa immagine gestita variano.

Assicurarsi che la risorsa usata per creare l'immagine gestita esista per la durata di qualsiasi pool che faccia riferimento all'immagine personalizzata. In caso contrario, possono verificarsi errori di allocazione del pool e/o errori di ridimensionamento.

Se l'immagine o la risorsa sottostante è stata rimossa, è possibile che venga visualizzato un errore simile al seguente: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se viene ricevuto questo errore, assicurarsi che la risorsa sottostante non sia stata rimossa.

Per altre informazioni sull'uso di Packer per creare una macchina virtuale, vedere [Creare un'immagine Linux con Packer](../virtual-machines/linux/build-image-with-packer.md) o [Creare un'immagine Windows con Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare la [Raccolta immagini condivise](batch-sig-images.md) per creare un pool personalizzato.
- Per una panoramica approfondita di Batch, vedere [Flusso di lavoro e risorse del servizio Batch](batch-service-workflow-features.md).
