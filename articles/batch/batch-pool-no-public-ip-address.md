---
title: Creare un pool di Azure Batch senza indirizzi IP pubblici
description: Informazioni su come creare un pool senza indirizzi IP pubblici
author: pkshultz
ms.topic: how-to
ms.date: 10/08/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: fcc0538dfef1581a244ae5fd9a3515be3470026c
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850932"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Creare un pool di Azure Batch senza indirizzi IP pubblici

Quando si crea un pool di Azure Batch, è possibile effettuare il provisioning del pool di configurazione della macchina virtuale senza un indirizzo IP pubblico. Questo articolo illustra come configurare un pool di batch senza indirizzi IP pubblici.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Perché usare un pool senza indirizzi IP pubblici?

Per impostazione predefinita, a tutti i nodi di calcolo in un pool di configurazione della macchina virtuale Azure Batch viene assegnato un indirizzo IP pubblico. Questo indirizzo viene usato dal servizio batch per pianificare le attività e per la comunicazione con i nodi di calcolo, incluso l'accesso in uscita a Internet.

Per limitare l'accesso a questi nodi e ridurre l'individuabilità di questi nodi da Internet, è possibile effettuare il provisioning del pool senza indirizzi IP pubblici.

> [!IMPORTANT]
> Il supporto per i pool senza indirizzi IP pubblici in Azure Batch è attualmente disponibile in anteprima pubblica per le aree seguenti: Francia centrale, Asia orientale, Stati Uniti centro-occidentali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti orientali, Europa settentrionale, Stati Uniti orientali 2, Stati Uniti centrali, Europa occidentale, Stati Uniti centro-occidentali, Stati Uniti occidentali, Australia orientale
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- **Autenticazione**. Per usare un pool senza indirizzi IP pubblici all'interno di una [rete virtuale](./batch-virtual-network.md), l'API client di batch deve usare l'autenticazione Azure Active Directory (ad). Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md). Se non si crea il pool in una rete virtuale, è possibile usare l'autenticazione Azure AD o l'autenticazione basata su chiavi.

- **Una rete virtuale di Azure**. Se si sta creando il pool in una [rete virtuale](batch-virtual-network.md), attenersi ai requisiti e alle configurazioni seguenti. Per preparare una rete virtuale con una o più subnet, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o altri metodi.
  - La rete virtuale deve essere nella stessa sottoscrizione e area dell'account Batch usato per creare il pool.
  - La subnet specificata per il pool deve disporre di indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione per il pool; questo valore corrisponde alla somma delle proprietà `targetDedicatedNodes` e `targetLowPriorityNodes` del pool. Se la subnet non dispone di sufficienti indirizzi IP non assegnati, il pool alloca parzialmente i nodi di calcolo e si verifica un errore di ridimensionamento.
  - È necessario disabilitare il servizio di collegamento privato e i criteri di rete dell'endpoint. Questa operazione può essere eseguita usando l'interfaccia della riga di comando di Azure: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Per ogni 100 di nodi dedicati o con priorità bassa, batch alloca un servizio di collegamento privato e un servizio di bilanciamento del carico. Queste risorse sono limitate in base alle [quote delle risorse](../azure-resource-manager/management/azure-subscription-service-limits.md) della sottoscrizione. Per i pool di grandi dimensioni, potrebbe essere necessario [richiedere un aumento della quota](batch-quota-limit.md#increase-a-quota) per una o più di queste risorse. Inoltre, non deve essere applicato alcun blocco di risorsa a qualsiasi risorsa creata da batch, perché ciò impedisce la pulizia delle risorse in seguito ad azioni avviate dall'utente, ad esempio l'eliminazione di un pool o il ridimensionamento a zero.

## <a name="current-limitations"></a>Limitazioni correnti

1. I pool senza indirizzi IP pubblici devono usare la configurazione della macchina virtuale e non la configurazione dei servizi cloud.
1. La [configurazione dell'endpoint personalizzato](pool-endpoint-configuration.md) per i nodi di calcolo batch non funziona con i pool senza indirizzi IP pubblici.
1. Poiché non sono presenti indirizzi IP pubblici, non è possibile [usare indirizzi IP pubblici specifici](create-pool-public-ip.md) con questo tipo di pool.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Creare un pool senza indirizzi IP pubblici nel portale di Azure

1. Passare all'account Batch nel portale di Azure.
1. Nella finestra **Impostazioni** a sinistra selezionare **pool**.
1. Nella finestra **pool** selezionare **Aggiungi**.
1. Nella finestra **Aggiungi pool** selezionare l'opzione desiderata nell'elenco a discesa **Tipo di immagine**.
1. Selezionare il **server di pubblicazione/offerta/SKU** corretto dell'immagine.
1. Specificare le impostazioni obbligatorie rimanenti, tra cui le **dimensioni del nodo**, i **nodi dedicati di destinazione**e i nodi con **priorità bassa**, nonché le impostazioni facoltative desiderate.
1. Facoltativamente, selezionare una rete virtuale e una subnet che si desidera utilizzare. Questa rete virtuale deve trovarsi nello stesso gruppo di risorse del pool che si sta creando.
1. In **tipo di provisioning indirizzi IP**selezionare **NoPublicIPAddresses**.

![Screenshot della schermata Aggiungi pool con NoPublicIPAddresses selezionato.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Usare l'API REST di batch per creare un pool senza indirizzi IP pubblici

L'esempio seguente illustra come usare l' [API REST di Azure batch](/rest/api/batchservice/pool/add) per creare un pool che usa indirizzi IP pubblici.

### <a name="rest-api-uri"></a>URI DELL'API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Corpo della richiesta

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Accesso in uscita a Internet

In un pool senza indirizzi IP pubblici, le macchine virtuali non saranno in grado di accedere alla rete Internet pubblica a meno che non si configuri la configurazione di rete in modo appropriato, ad esempio usando la [rete virtuale NAT](../virtual-network/nat-overview.md). Si noti che NAT consente l'accesso in uscita a Internet solo dalle macchine virtuali nella rete virtuale. I nodi di calcolo creati in batch non saranno accessibili pubblicamente, perché non hanno indirizzi IP pubblici associati.

Un altro modo per fornire connettività in uscita consiste nell'usare una route definita dall'utente (UDR). Ciò consente di instradare il traffico a un computer proxy che dispone di accesso a Internet pubblico.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [creazione di pool in una rete virtuale](batch-virtual-network.md).
- Informazioni su come [usare gli endpoint privati con account batch](private-connectivity.md).
