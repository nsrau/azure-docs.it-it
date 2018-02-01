---
title: Eseguire il provisioning del pool di Azure Batch in una rete virtuale |Microsoft Docs
description: "È possibile creare un pool di Azure Batch in una rete virtuale in modo che i nodi di calcolo possano comunicare in modo sicuro con altre macchine virtuali nella rete, ad esempio un file server."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/16/2017
ms.author: danlep
ms.openlocfilehash: 647a123e403b0ed6458bc26e7b26a73f3c1f8e91
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Creare un pool di Azure Batch in una rete virtuale


Quando si crea un pool di Azure Batch, è possibile eseguire il provisioning del pool in una subnet di una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) (VNet) specificata. In questo articolo viene illustrato come impostare un pool di Azure Batch in una rete virtuale. 



## <a name="why-use-a-vnet"></a>Perché usare una rete virtuale


Un pool di Azure Batch include impostazioni che consentono ai nodi di calcolo di comunicare tra loro, ad esempio per eseguire attività a istanze multiple. Queste impostazioni non richiedono una rete virtuale separata. Tuttavia, per impostazione predefinita, i nodi non possono comunicare con macchine virtuali che non fanno parte del pool di Azure Batch, ad esempio un server licenze o un file server. Per consentire ai nodi di calcolo del pool di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile eseguire il provisioning del pool in una subnet di una rete virtuale di Azure. 



## <a name="prerequisites"></a>prerequisiti

* **Autenticazione**. Per usare una rete virtuale di Azure, l'API client di Batch deve usare l'autenticazione di Azure Active Directory (AD). Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md). 

* **Una rete virtuale di Azure**. Per preparare una rete virtuale con una o più subnet, è possibile usare il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o altri metodi. Per creare una rete virtuale basata su Azure Resource Manager, vedere [Creare una rete virtuale con più subnet](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Per creare una rete virtuale classica, vedere [Creare una rete virtuale (classica) con più subnet](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>Requisiti per la rete virtuale
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Creare un pool con una rete virtuale nel portale

Dopo aver creato la rete virtuale e assegnato una subnet, è possibile creare un pool di Batch con tale rete. Seguire questa procedura per creare un pool dal portale di Azure: 



1. Passare all'account Batch nel portale di Azure. Questo account deve trovarsi nella stessa area e nella stessa sottoscrizione del gruppo di risorse contenente la rete virtuale che si desidera usare. 
2. Nella finestra **Impostazioni** a sinistra scegliere la voce di menu **Pool**.
3. Nella finestra **Pool** selezionare il comando **Aggiungi**.
4. Nella finestra **Aggiungi pool** selezionare l'opzione desiderata nell'elenco a discesa **Tipo di immagine**. 
5. Selezionare il valore corretto nei campi **Server di pubblicazione/Offerta/SKU** per l'immagine personalizzata.
6. Specificare le rimanenti impostazioni obbligatorie, incluse **Dimensioni nodo**, **Nodi dedicati target** e **Nodi per priorità bassa**, oltre alle impostazioni facoltative desiderate.
7. In **Rete virtuale** selezionare la rete virtuale e la subnet che si desidera usare.
  
  ![Aggiungere il pool con la rete virtuale](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Route definite dall'utente per il tunneling forzato

È possibile che l'organizzazione richieda il reindirizzamento (forzatura) del traffico associato a Internet dalla subnet alla posizione locale con finalità di ispezione e registrazione. È possibile che il tunneling forzato sia abilitato per le subnet nella rete virtuale in uso. 

Per garantire che i nodi di calcolo dei pool di Azure Batch funzionino correttamente in una rete virtuale con il tunneling forzato abilitato, è necessario aggiungere le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) seguenti per tali subnet:

* Il servizio Batch deve comunicare con nodi di calcolo dei pool per la pianificazione delle attività. Per abilitare questa comunicazione, aggiungere una route definita dall'utente per ogni indirizzo IP usando il servizio Batch nell'area in cui è presente l'account Batch. Per ottenere l'elenco di indirizzi IP del servizio Batch, contattare il supporto tecnico di Azure.

* Verificare che il traffico in uscita verso il servizio Archiviazione di Azure (in particolare, gli URL con formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`) non sia bloccato tramite un Network Appliance locale.

Quando si aggiunge una route definita dall'utente, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare **Tipo hop successivo** su **Internet**. Vedere l'esempio seguente:

![Route definita dall'utente](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
- Per altre informazioni sulla creazione di una route definita dall'utente, vedere [Creare una route definita dall'utente - Portale di Azure](../virtual-network/create-user-defined-route-portal.md).
