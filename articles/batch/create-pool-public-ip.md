---
title: Creare un pool con indirizzi IP pubblici specificati
description: Informazioni su come creare un pool di batch che usa indirizzi IP pubblici personalizzati.
ms.topic: how-to
ms.date: 07/20/2020
ms.openlocfilehash: 630da3ff9c1f2318c7ed4da0e8f4b5ee8212f389
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023756"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Creare un pool di Azure Batch con gli indirizzi IP pubblici specificati

Quando si crea un pool di Azure Batch, è possibile effettuare [il provisioning del pool in una subnet di una rete virtuale di Azure](batch-virtual-network.md) (VNet) specificata. È possibile accedere alle macchine virtuali nel pool di batch tramite indirizzi IP pubblici creati da batch. Questi indirizzi IP pubblici possono cambiare nel corso della durata del pool, il che significa che le impostazioni di rete possono diventare obsolete se gli indirizzi IP non vengono aggiornati.

È possibile creare un elenco di indirizzi IP pubblici statici da usare con le macchine virtuali nel pool. Ciò consente di controllare l'elenco degli indirizzi IP pubblici e di assicurarsi che non cambino in modo imprevisto. Questo può essere particolarmente utile se si utilizza un servizio esterno, ad esempio un database, che limita l'accesso a determinati indirizzi IP.

Per informazioni sulla creazione di pool senza indirizzi IP pubblici, vedere [creare un pool di Azure batch senza indirizzi IP pubblici](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Prerequisiti

- **Autenticazione**. Per usare un indirizzo IP pubblico, l'API client di batch deve usare l' [autenticazione Azure Active Directory (ad)](batch-aad-auth.md).

- **Una rete virtuale di Azure**. È necessario usare una [rete virtuale](batch-virtual-network.md) dalla stessa sottoscrizione di Azure in cui si sta creando il pool e gli indirizzi IP. È possibile utilizzare solo reti virtuali basati su Azure Resource Manager. Verificare che il VNet soddisfi tutti i [requisiti generali](batch-virtual-network.md#vnet-requirements).

- Almeno **un indirizzo IP pubblico di Azure**. Per creare uno o più indirizzi IP pubblici, è possibile usare la [portale di Azure](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), l' [interfaccia della riga di comando di Azure (CLI)](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)o [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Assicurarsi di rispettare i requisiti elencati di seguito.

> [!NOTE]
> Batch alloca automaticamente altre risorse di rete nel gruppo di risorse che contiene gli indirizzi IP pubblici. Per ogni nodo dedicato 100, batch alloca in genere un gruppo di sicurezza di rete (NSG) e un servizio di bilanciamento del carico. Queste risorse sono limitate dalle quote delle risorse della sottoscrizione. Quando si usano pool più grandi, potrebbe essere necessario [richiedere un aumento della quota](batch-quota-limit.md#increase-a-quota) per una o più di queste risorse.

## <a name="public-ip-address-requirements"></a>Requisiti per gli indirizzi IP pubblici

Quando si creano gli indirizzi IP pubblici, tenere presenti i requisiti seguenti:

- Gli indirizzi IP pubblici devono trovarsi nella stessa sottoscrizione e nella stessa area dell'account batch usato per creare il pool.
- L' **assegnazione di indirizzi IP** deve essere impostata su **static**.
- Lo **SKU** deve essere impostato su **standard**.
- È necessario specificare un nome DNS.
- Gli indirizzi IP pubblici devono essere usati solo per i pool di configurazione della macchina virtuale. Nessun'altra risorsa deve usare questi indirizzi IP oppure è possibile che si verifichino errori di allocazione nel pool.
- Nessun criterio di sicurezza o blocco delle risorse deve limitare l'accesso dell'utente all'indirizzo IP pubblico.
- Il numero di indirizzi IP pubblici specificati per il pool deve essere sufficientemente grande da contenere il numero di macchine virtuali di destinazione per il pool. Deve essere almeno la somma delle proprietà **targetDedicatedNodes**   e **targetLowPriorityNodes**   del pool. Se non sono disponibili indirizzi IP sufficienti, il pool alloca parzialmente i nodi di calcolo e si verificherà un errore di ridimensionamento. Attualmente, batch usa un indirizzo IP pubblico per ogni VM 100.
- Disporre sempre di un buffer aggiuntivo di indirizzi IP pubblici. È consigliabile aggiungere almeno un indirizzo IP pubblico aggiuntivo oppure circa il 10% degli indirizzi IP pubblici totali aggiunti a un pool, a seconda del valore maggiore. Questo buffer aggiuntivo consente di eseguire il batch con l'ottimizzazione interna durante la riduzione delle prestazioni, oltre a consentire una scalabilità più rapida dopo una scalabilità verticale non riuscita.
- Una volta creato il pool, non è possibile aggiungere o modificare l'elenco di indirizzi IP pubblici usati dal pool. Se è necessario modificare l'elenco, è necessario eliminare il pool e quindi ricrearlo.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Creare un pool di batch con indirizzi IP pubblici

L'esempio seguente illustra come usare l' [API REST del servizio Azure batch](/rest/api/batchservice/pool/add) per creare un pool che usa indirizzi IP pubblici.

### <a name="batch-service-rest-api"></a>API REST per il servizio Batch

URI DELL'API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Request Body

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "maxTasksPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      }, 
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Informazioni sulla [creazione di un pool in una subnet di una rete virtuale di Azure](batch-virtual-network.md).
- Informazioni sulla [creazione di un pool di Azure batch senza indirizzi IP pubblici](./batch-pool-no-public-ip-address.md).

