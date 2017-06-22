---
title: Aprire porte a una VM tramite il portale di Azure | Microsoft Docs
description: Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Resource Manager nel portale di Azure
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 20aa8247a16195310a8cb03c13c34186456fd7af
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Come aprire le porte in una macchina virtuale con il portale di Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
È possibile [eseguire questi passaggi anche tramite Azure PowerShell](nsg-quickstart-powershell.md).

Come prima operazione, creare il gruppo di sicurezza di rete. Selezionare un gruppo di risorse nel portale, fare clic su **Aggiungi**, quindi cercare e selezionare "Gruppo di sicurezza di rete":

![Aggiungere un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/add-nsg.png)

Immettere un nome per il gruppo di sicurezza di rete e selezionare o creare un gruppo di risorse, quindi selezionare una posizione. Al termine, fare clic su **Crea**:

![Creare un gruppo di sicurezza di rete](./media/nsg-quickstart-portal/create-nsg.png)

Selezionare il nuovo gruppo di sicurezza di rete. Selezionare "Regole di sicurezza in ingresso", quindi fare clic sul pulsante **Aggiungi** per creare una regola:

![Aggiungere una regola in entrata](./media/nsg-quickstart-portal/add-inbound-rule.png)

Fornire un nome per la regola. La porta 80 è già inserita per impostazione predefinita. Questo pannello consente di modificare l'origine, il protocollo e la destinazione durante l'aggiunta di altre regole al gruppo di sicurezza di rete. Fare clic su **OK** per creare la regola:

![Creare una regola in entrata](./media/nsg-quickstart-portal/create-inbound-rule.png)

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete con una subnet o un'interfaccia di rete specifica. Per associare il gruppo di sicurezza di rete a una subnet. Selezionare "Subnet", quindi fare clic su **Associa**:

![Associare un gruppo di sicurezza di rete con una subnet](./media/nsg-quickstart-portal/associate-subnet.png)

Selezionare la rete virtuale, quindi selezionare la subnet appropriata:

![Associare un gruppo di sicurezza di rete con una rete virtuale](./media/nsg-quickstart-portal/select-vnet-subnet.png)

È stato creato un gruppo di sicurezza di rete, è stata creata una regola in entrata che consente il traffico sulla porta 80 ed è stata associata a una subnet. Tutte le VM che si connettono a questa subnet sono raggiungibili sulla porta 80.

## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
* [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../../load-balancer/load-balancer-arm.md)


