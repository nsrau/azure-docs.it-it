---
title: Aprire porte in una macchina virtuale usando il portale di Azure
description: Informazioni su come aprire una porta o creare un endpoint nella macchina virtuale Windows usando il portale di Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 12db42b0edb3d3e27756593f3dfb079804a4145f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170043"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Come aprire le porte in una macchina virtuale con il portale di Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. Cercare e selezionare un gruppo di risorse per la macchina virtuale, scegliere **Aggiungi**, quindi cercare e selezionare **Gruppo di sicurezza di rete**.

1. Selezionare **Crea**.

    Viene visualizzata la finestra **Crea gruppo di sicurezza di rete**.

    ![Creare un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/create-nsg.png)

1. Immettere un nome per il gruppo di sicurezza di rete. 

1. Selezionare o creare un gruppo di risorse e quindi selezionare un percorso.

1. Selezionare **Crea** per creare il gruppo di sicurezza di rete.

## <a name="create-an-inbound-security-rule"></a>Creare una regola di sicurezza in ingresso

1. Selezionare il nuovo gruppo di sicurezza di rete. 

1. Selezionare **Regole di sicurezza in ingresso** nel menu a sinistra, quindi selezionare **Aggiungi**.

    ![Passare alla pagina Avanzate](./media/nsg-quickstart-portal/advanced.png)

1. Nella parte superiore della pagina **Aggiungi regola di sicurezza in ingresso** passare ad **Avanzate** da **Base**. 

1. Scegliere un **servizio** comune dal menu a discesa, ad esempio **HTTP**. È possibile anche selezionare l'opzione **Personalizzata** per indicare una porta specifica da usare. 

1. Facoltativamente, modificare il valore di **Priorità** o **Nome**. La priorità determina l'ordine in cui vengono applicate le regole: più basso è il valore numerico, prima viene applicata la regola.

1. Selezionare **Aggiungi** per creare la regola.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associare il gruppo di sicurezza di rete a una subnet

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete a una subnet o un'interfaccia di rete specifica. Per questo esempio, si assocerà il gruppo di sicurezza di rete a una subnet. 

1. Selezionare **Subnet** dal menu a sinistra, quindi selezionare **Associa**.

1. Selezionare la rete virtuale, quindi selezionare la subnet appropriata.

    ![Associazione di un gruppo di sicurezza di rete a una rete virtuale](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Al termine, selezionare **OK**.

## <a name="additional-information"></a>Informazioni aggiuntive

È possibile [eseguire i passaggi descritti in questo articolo anche tramite Azure PowerShell](nsg-quickstart-powershell.md).

I comandi descritti in questo articolo consentono di impostare rapidamente la direzione del traffico verso la macchina virtuale. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la granularità necessaria per controllare l'accesso alle risorse. Per altre informazioni, vedere [Filtrare il traffico di rete con un gruppo di sicurezza di rete](../../virtual-network/tutorial-filter-network-traffic.md).

Per le applicazioni Web a disponibilità elevata, è consigliabile posizionare le macchine virtuali dietro un'istanza di Azure Load Balancer. Quest'ultima distribuisce il traffico alle macchine virtuali, con un gruppo di sicurezza di rete che consente di filtrare il traffico. Per altre informazioni, vedere [Bilanciare il carico per le macchine virtuali Windows in Azure per creare un'applicazione a disponibilità elevata](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato un gruppo di sicurezza di rete, è stata definita una regola in ingresso che consente il traffico HTTP sulla porta 80 ed è stata associata tale regola a una subnet. 

È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:
- [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Gruppi di sicurezza](../../virtual-network/security-overview.md)
