---
title: Aprire le porte per una VM usando il portale di Azure
description: Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Resource Manager nel portale di Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: fe9b0f590dbb728a54ac66fcaa49bf3d00417e41
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033115"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Come aprire le porte in una macchina virtuale con il portale di Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. Cercare e selezionare un gruppo di risorse per la macchina virtuale, scegliere **Aggiungi**, quindi cercare e selezionare **Gruppo di sicurezza di rete**.

2. Selezionare **Create**.

    Viene visualizzata la finestra **Crea gruppo di sicurezza di rete**.

    ![Creare un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/create-nsg.png)

2. Immettere un nome per il gruppo di sicurezza di rete. 

3. Selezionare o creare un gruppo di risorse e quindi selezionare un percorso.

4. Selezionare **Crea** per creare il gruppo di sicurezza di rete.

## <a name="create-an-inbound-security-rule"></a>Creare una regola di sicurezza in ingresso

1. Selezionare il nuovo gruppo di sicurezza di rete. 

2. Selezionare **Regole di sicurezza in ingresso** e quindi **Aggiungi**.

    ![Aggiungere una regola in ingresso](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selezionare **Advanced** (Avanzate). 

4. Scegliere un **servizio** comune dal menu a discesa, ad esempio **HTTP**. È possibile anche selezionare l'opzione **Personalizzata** per indicare una porta specifica da usare. 

5. Facoltativamente, modificare il valore di **Priorità** o **Nome**. La priorità determina l'ordine in cui vengono applicate le regole: più basso è il valore numerico, prima viene applicata la regola.

6. Selezionare **Aggiungi** per creare la regola.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associare il gruppo di sicurezza di rete a una subnet

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete a una subnet o un'interfaccia di rete specifica. Per questo esempio, si assocerà il gruppo di sicurezza di rete a una subnet. 

1. Selezionare **Subnet** e quindi **Associa**.

    ![Associare un gruppo di sicurezza di rete a una subnet](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selezionare la rete virtuale, quindi selezionare la subnet appropriata.

    ![Associazione di un gruppo di sicurezza di rete a una rete virtuale](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Le macchine virtuali che si connettono alla subnet sono ora raggiungibili sulla porta 80.

## <a name="additional-information"></a>Informazioni aggiuntive

È possibile [eseguire i passaggi descritti in questo articolo anche tramite Azure PowerShell](nsg-quickstart-powershell.md).

I comandi descritti in questo articolo consentono di impostare rapidamente la direzione del traffico verso la macchina virtuale. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la granularità necessaria per controllare l'accesso alle risorse. Per altre informazioni, vedere [Filtrare il traffico di rete con un gruppo di sicurezza di rete](../../virtual-network/tutorial-filter-network-traffic.md).

Per le applicazioni Web a disponibilità elevata, è consigliabile posizionare le macchine virtuali dietro un'istanza di Azure Load Balancer. Quest'ultima distribuisce il traffico alle macchine virtuali, con un gruppo di sicurezza di rete che consente di filtrare il traffico. Per altre informazioni, vedere [Bilanciare il carico per le macchine virtuali Windows in Azure per creare un'applicazione a disponibilità elevata](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato un gruppo di sicurezza di rete, è stata definita una regola in ingresso che consente il traffico HTTP sulla porta 80 ed è stata associata tale regola a una subnet. 

È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:
- [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Gruppi di sicurezza](../../virtual-network/security-overview.md)