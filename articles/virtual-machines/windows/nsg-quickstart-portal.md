---
title: Aprire porte a una VM tramite il portale di Azure | Microsoft Docs
description: Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Resource Manager nel portale di Azure
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Come aprire le porte in una macchina virtuale con il portale di Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
È possibile [eseguire questi passaggi anche tramite Azure PowerShell](nsg-quickstart-powershell.md).

Come prima operazione, creare il gruppo di sicurezza di rete. Selezionare un gruppo di risorse nel portale, scegliere **Aggiungi**, quindi cercare e selezionare **Gruppo di sicurezza di rete**:

![Aggiungere un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/add-nsg.png)

Immettere un nome per il gruppo di sicurezza di rete e selezionare o creare un gruppo di risorse, quindi selezionare una posizione. Al termine selezionare **Crea**:

![Creare un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/create-nsg.png)

Selezionare il nuovo gruppo di sicurezza di rete. Selezionare "Regole di sicurezza in ingresso", quindi fare clic sul pulsante **Aggiungi** per creare una regola:

![Aggiungere una regola in entrata](./media/nsg-quickstart-portal/add-inbound-rule.png)

Per creare una regola che consenta il traffico:

- Selezionare il pulsante **Basic**. Per impostazione predefinita, la finestra **Avanzata** offre alcune opzioni di configurazione aggiuntive, ad esempio per definire un intervallo specifico di porte o indirizzi IP di origine bloccati.
- Scegliere un **servizio** comune dal menu a discesa, ad esempio *HTTP*. È possibile anche selezionare l'opzione *Personalizzata* per indicare una porta specifica da usare. 
- Se si desidera, modificare la priorità o il nome. La priorità determina l'ordine in cui vengono applicate le regole: più basso è il valore numerico, prima viene applicata la regola.
- Quando si è pronti, selezionare **OK** per creare la regola:

![Creare una regola in entrata](./media/nsg-quickstart-portal/create-inbound-rule.png)

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete con una subnet o un'interfaccia di rete specifica. Per associare il gruppo di sicurezza di rete a una subnet. Selezionare **Subnet**, quindi scegliere **Associa**:

![Associare un gruppo di sicurezza di rete con una subnet](./media/nsg-quickstart-portal/associate-subnet.png)

Selezionare la rete virtuale, quindi selezionare la subnet appropriata:

![Associare un gruppo di sicurezza di rete con una rete virtuale](./media/nsg-quickstart-portal/select-vnet-subnet.png)

È stato creato un gruppo di sicurezza di rete, è stata creata una regola in entrata che consente il traffico sulla porta 80 ed è stata associata a una subnet. Tutte le VM che si connettono a questa subnet sono raggiungibili sulla porta 80.

## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Per le applicazioni Web a disponibilità elevata, è consigliabile inserire le macchine virtuali dietro a un Azure Load Balancer. Il bilanciamento del carico distribuisce il traffico alle macchine virtuali, con un gruppo di sicurezza di rete che consente di filtrare il traffico. Per altre informazioni, vedere [Come bilanciare il carico per le macchine virtuali di Linux in Azure per creare un'applicazione a disponibilità elevata](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)