---
title: Effettuare il provisioning di un pool in una rete virtuale
description: Come creare un pool di Batch in una rete virtuale di Azure in modo che i nodi di calcolo possano comunicare in modo sicuro con altre macchine virtuali nella rete, ad esempio un file server.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: seodec18
ms.openlocfilehash: 3ff8995217521199436e0924acc691dab7c4baef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506605"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Creare un pool di Azure Batch in una rete virtuale

Quando si crea un pool di Azure Batch, è possibile eseguire il provisioning del pool in una subnet di una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) (VNet) specificata. In questo articolo viene illustrato come impostare un pool di Azure Batch in una rete virtuale.

## <a name="why-use-a-vnet"></a>Perché usare una rete virtuale

I nodi di calcolo in un pool possono comunicare tra loro, ad esempio per eseguire attività a istanze diverse, senza richiedere un VNet separato. Tuttavia, per impostazione predefinita, i nodi in un pool non possono comunicare con le macchine virtuali esterne al pool, ad esempio i server licenze o i file server.

Per consentire ai nodi di calcolo di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile effettuare il provisioning del pool in una subnet di una VNet di Azure.

## <a name="prerequisites"></a>Prerequisiti

- **Autenticazione**. Per usare una rete virtuale di Azure, l'API client di Batch deve usare l'autenticazione di Azure Active Directory (AD). Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

- **Una rete virtuale di Azure**. Vedere la sezione seguente per i requisiti e la configurazione della rete virtuale. Per preparare una rete virtuale con una o più subnet, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o altri metodi.
  - Per creare una rete virtuale basata su Azure Resource Manager, vedere [Creare una rete virtuale](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Per le nuove distribuzioni è consigliabile un VNet basato su Gestione risorse ed è supportato solo nei pool che usano la configurazione della macchina virtuale.
  - Per creare una rete virtuale classica, vedere [Creare una rete virtuale (classica) con più subnet](../virtual-network/create-virtual-network-classic.md). Una VNet classica è supportata solo nei pool che usano la configurazione dei servizi cloud.

## <a name="vnet-requirements"></a>Requisiti per la rete virtuale

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Creare un pool con un VNet nel portale di Azure

Dopo aver creato la rete virtuale e assegnato una subnet, è possibile creare un pool di Batch con tale rete. Seguire questa procedura per creare un pool dal portale di Azure: 

1. Passare all'account Batch nel portale di Azure. Questo account deve trovarsi nella stessa area e nella stessa sottoscrizione del gruppo di risorse contenente la rete virtuale che si desidera usare.
2. Nella finestra **Impostazioni** a sinistra scegliere la voce di menu **Pool**.
3. Nella finestra **pool** selezionare **Aggiungi**.
4. Nella finestra **Aggiungi pool** selezionare l'opzione desiderata nell'elenco a discesa **Tipo di immagine**.
5. Selezionare il valore corretto nei campi **Server di pubblicazione/Offerta/SKU** per l'immagine personalizzata.
6. Specificare le rimanenti impostazioni obbligatorie, incluse **Dimensioni nodo**, **Nodi dedicati target** e **Nodi per priorità bassa**, oltre alle impostazioni facoltative desiderate.
7. In **Rete virtuale** selezionare la rete virtuale e la subnet che si desidera usare.

   ![Aggiungere il pool con la rete virtuale](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Route definite dall'utente per il tunneling forzato

Si potrebbero avere requisiti nell'organizzazione per reindirizzare (forzare) il traffico associato a Internet dalla subnet al percorso locale per l'ispezione e la registrazione. Inoltre, è possibile che sia stato abilitato il tunneling forzato per le subnet nella VNet.

Per assicurarsi che i nodi del pool funzionino in una VNet in cui è abilitato il tunneling forzato, è necessario aggiungere le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) (UDR) seguenti per la subnet:

- Il servizio batch deve comunicare con i nodi per la pianificazione delle attività. Per abilitare questa comunicazione, aggiungere una UDR per ogni indirizzo IP usando il servizio Batch nell'area in cui è presente l'account Batch. Per ottenere l'elenco degli indirizzi IP del servizio batch, vedere [tag di servizio in locale](../virtual-network/service-tags-overview.md).

- Verificare che il traffico in uscita verso archiviazione di Azure (in particolare, gli URL con formato `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` ) non siano bloccati dalla rete locale.

Quando si aggiunge una UDR, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare **Tipo hop successivo** su **Internet**.

![Route definita dall'utente](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Gli indirizzi IP del servizio Batch possono cambiare nel tempo. Per evitare interruzioni a causa di una modifica dell'indirizzo IP, creare un processo per aggiornare automaticamente gli indirizzi IP del servizio batch e mantenerli aggiornati nella tabella di route. In alternativa, è possibile [creare un pool con gli indirizzi IP specificati che si controllano](create-pool-public-ip.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Informazioni su come [creare una route definita dall'utente nel portale di Azure](../virtual-network/tutorial-create-route-table-portal.md).
