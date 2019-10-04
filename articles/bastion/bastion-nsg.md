---
title: Uso di macchine virtuali e gruppi in Azure Bastion | Microsoft Docs
description: Questo articolo descrive come incorporare l'accesso NSG con Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694936"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Uso di NSG Access e Azure Bastion (anteprima)

Quando si lavora con Azure Bastion, è possibile usare i gruppi di sicurezza di rete (gruppi). Per ulteriori informazioni, vedere [gruppi di sicurezza](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architettura](./media/bastion-nsg/nsg_architecture.png)

In questo diagramma:

* Il bastion host viene distribuito nella rete virtuale.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente seleziona la macchina virtuale a cui connettersi.
* Con un solo clic, la sessione RDP/SSH viene aperta nel browser.
* Non è richiesto alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="nsg"></a>Gruppi di sicurezza di rete

* **AzureBastionSubnet:** Azure Bastion viene distribuito nella AzureBastionSubnet specifica.  
    * **Traffico in ingresso da Internet pubblico:** Azure Bastion creerà un IP pubblico che richiede la porta 443 abilitata nell'indirizzo IP pubblico per il traffico in ingresso. NON è necessario aprire la porta 3389/22 nella AzureBastionSubnet.
    * **Traffico in uscita per le macchine virtuali di destinazione:** Azure Bastion raggiungerà le VM di destinazione tramite un indirizzo IP privato. Il gruppi deve consentire il traffico in uscita ad altre subnet VM di destinazione.
* **Subnet VM di destinazione:** Si tratta della subnet che contiene la macchina virtuale di destinazione a cui si desidera connettersi tramite RDP/SSH.
    * **Traffico in ingresso da Azure Bastion:** Azure Bastion raggiungerà la macchina virtuale di destinazione tramite un indirizzo IP privato. Le porte RDP/SSH (rispettivamente le porte 3389 e 22) devono essere aperte sul lato della macchina virtuale di destinazione su un indirizzo IP privato.

## <a name="apply"></a>Applicare gruppi a AzureBastionSubnet

Se si applica gruppi al **AzureBastionSubnet**, consentire i due tag di servizio seguenti per l'infrastruttura e il piano di controllo di Azure:

* **GatewayManager (solo gestione risorse)** : Questo tag identifica i prefissi di indirizzo del servizio Azure Gateway Manager. Se si specifica GatewayManager per il valore, il traffico è consentito o negato a GatewayManager.  Se si sta creando gruppi in AzureBastionSubnet, abilitare il tag GatewayManager per il traffico in ingresso.

* **AzureCloud (solo gestione risorse)** : Questo tag denota lo spazio degli indirizzi IP per Azure, inclusi tutti gli indirizzi IP pubblici dei data center. Se si specifica AzureCloud per il valore, il traffico è consentito o negato per gli indirizzi IP pubblici di Azure. Se si vuole consentire l'accesso solo a AzureCloud in un'area specifica, è possibile specificare l'area. Se ad esempio si vuole consentire l'accesso solo ad Azure AzureCloud nell'area Stati Uniti orientali, è possibile specificare AzureCloud. Eastus come tag di servizio. Se si sta creando gruppi in AzureBastionSubnet, abilitare il tag AzureCloud per il traffico in uscita. Se si apre la porta 443 per il traffico in ingresso verso Internet, non è necessario abilitare il tag AzureCloud per il traffico in ingresso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere le [domande frequenti](bastion-faq.md)
