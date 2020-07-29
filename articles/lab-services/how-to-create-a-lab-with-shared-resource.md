---
title: Come creare un Lab con una risorsa condivisa | Azure Lab Services
description: Informazioni su come creare un Lab che richiede una risorsa condivisa tra gli studenti.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443367"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Come creare un Lab con una risorsa condivisa in Azure Lab Services

In alcuni casi, quando si crea un Lab in aula, è possibile che alcune risorse debbano essere condivise tra tutti gli studenti di un Lab.  Si dispone, ad esempio, di un server licenze o SQL Server per una classe database.  Questo articolo illustra i passaggi per abilitare la risorsa condivisa per un Lab.  Si parlerà anche di come limitare l'accesso a tale risorsa condivisa.

## <a name="architecture"></a>Architecture

Come illustrato nel diagramma seguente, sarà disponibile un account Lab con un Lab.  L'account Lab avrà le impostazioni di peering di VNET in modo che la rete virtuale per il Lab sia connessa alla rete della risorsa condivisa.  Nel diagramma seguente sono presenti due reti virtuali con intervalli IP non sovrapposti.  Questi intervalli IP sono solo intervalli di esempio.  Si noti anche che la rete virtuale di risorse condivise si trova nella stessa sottoscrizione dell'account Lab.

![Lab Services con architettura di risorse condivise](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Configurare la risorsa condivisa

Prima di creare il Lab, è necessario creare la rete virtuale per la risorsa condivisa.  Per ulteriori informazioni su come creare una rete virtuale, vedere [creare una rete virtuale](../virtual-network/quick-create-portal.md).  È importante pianificare gli intervalli di reti virtuali in modo che non si sovrappongano con l'indirizzo IP dei computer lab.  Per ulteriori informazioni sulla pianificazione della rete, vedere l'articolo [pianificare le reti virtuali](../virtual-network/virtual-network-vnet-plan-design-arm.md) . In questo esempio, la risorsa condivisa si trova in una rete virtuale con l'intervallo 10.2.0.0/16.  Se non è già stato fatto, [creare una subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) che contenga la risorsa condivisa.  Nell'esempio viene usato l'intervallo 10.2.0.0/24, ma l'intervallo può variare a seconda delle esigenze della rete.

La risorsa condivisa può essere il software in esecuzione in una macchina virtuale o un servizio fornito da Azure. La risorsa condivisa dovrebbe essere disponibile tramite un indirizzo IP privato.  Rendendo la risorsa condivisa disponibile solo tramite IP privato, è possibile limitare l'accesso alla risorsa condivisa.

Il diagramma mostra anche un gruppo di sicurezza di rete (NSG) che può essere usato per limitare il traffico proveniente dalla macchina virtuale per studenti.  Ad esempio, è possibile scrivere una regola di sicurezza che indica che il traffico proveniente dagli indirizzi IP della macchina virtuale studente può accedere solo a una risorsa condivisa e nient'altro.  Per altre informazioni su come impostare le regole di sicurezza, vedere [gestire un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md#work-with-security-rules). Se si vuole limitare l'accesso a una risorsa condivisa a un lab specifico, ottenere l'indirizzo IP per il Lab dalle [impostazioni Lab dall'account Lab](manage-labs.md#view-labs-in-a-lab-account) e impostare una regola in ingresso per consentire l'accesso solo da tale indirizzo IP.  Non dimenticare di consentire le porte da 49152 a 65535 per l'indirizzo IP.  Facoltativamente, è possibile trovare l'indirizzo IP privato delle VM dello studente usando la [pagina del pool di macchine virtuali](how-to-set-virtual-machine-passwords.md).

Se la risorsa condivisa è una macchina virtuale di Azure che esegue il software necessario, potrebbe essere necessario modificare le regole del firewall predefinite per la macchina virtuale.

## <a name="lab-account"></a>Account Lab

Per usare una risorsa condivisa, è necessario configurare l'account Lab per l'uso di una [rete virtuale con peering](how-to-connect-peer-virtual-network.md).  In questo caso, verrà utilizzato il peering alla rete virtuale che include la risorsa condivisa.

>[!WARNING]
>Il Lab per la classe deve essere creato **dopo che** è stato eseguito il peering dell'account Lab alla rete virtuale di risorse condivise.  
Computer modello

Una volta eseguito il peering dell'account Lab alla rete virtuale, il computer modello dovrebbe ora accedere alla risorsa condivisa.  Potrebbe essere necessario aggiornare le regole del firewall, a seconda della risorsa condivisa a cui si accede.
