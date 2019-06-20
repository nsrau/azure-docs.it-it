---
title: Uso di macchine virtuali e gli Nsg in Azure Bastion | Microsoft Docs
description: Questo articolo descrive come incorporare l'accesso di sicurezza di rete con Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191603"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Uso di accesso di sicurezza di rete e Azure Bastion (anteprima)

Quando si usa Azure Bastion, è possibile usare gruppi di sicurezza di rete (Nsg). Per altre informazioni, vedere [gruppi di sicurezza](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architettura](./media/bastion-nsg/nsg_architecture.png)

Nella figura seguente:

* L'host Bastion viene distribuito nella rete virtuale.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente seleziona la macchina virtuale a cui connettersi.
* Con un solo clic, la sessione RDP/SSH apre nel browser.
* Non è necessario alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="nsg"></a>Gruppi di sicurezza di rete

* **AzureBastionSubnet:** Azure Bastion viene distribuito nel AzureBastionSubnet specifico.  
    * **Traffico in ingresso dalla rete internet pubblica:** Il Bastion Azure creerà un indirizzo IP pubblico che è necessaria la porta 443 abilitato sull'indirizzo IP pubblico per il traffico in ingresso. NON è necessario essere aperta sul AzureBastionSubnet porta 3389/22.
    * **Traffico in uscita per le VM di destinazione:** Azure Bastion raggiungeranno la VM di destinazione tramite indirizzo IP privato. Nsg in modo da consentire il traffico in uscita ad altre subnet VM di destinazione.
* **Subnet della macchina virtuale di destinazione:** Si tratta di subnet che contiene la macchina virtuale di destinazione che si desidera eseguire RDP/SSH.
    * **Traffico in ingresso da Azure Bastion:** Azure Bastion per la macchina virtuale di destinazione possa essere raggiunto da indirizzo IP privato. Le porte RDP o SSH (porte 3389 e 22, rispettivamente) deve essere aperta sulla destinazione del lato di macchina virtuale tramite indirizzo IP privato.

## <a name="apply"></a>Applicare gli Nsg a AzureBastionSubnet

Se si applicano gli Nsg per la **AzureBastionSubnet**, consentire i seguenti due tag di servizio per piano di controllo di Azure e l'infrastruttura:

* **(Solo Resource Manager) GatewayManager**: Questo tag identifica i prefissi di indirizzo del servizio Azure Gateway Manager. Se si specifica GatewayManager per il valore, il traffico è consentito o negato per GatewayManager.

* **AzureCloud (solo Resource Manager)** : Questo tag identifica lo spazio di indirizzi IP per Azure include tutti i Data Center gli indirizzi IP pubblici. Se si specifica AzureCloud per il valore, il traffico è consentito o negato a indirizzi IP pubblici di Azure. Se si desidera solo consentire l'accesso al cloud di Azure in un'area specifica, è possibile specificare l'area. Ad esempio, se si desidera consentire l'accesso solo a Azure AzureCloud nell'area Stati Uniti orientali, è possibile specificare AzureCloud.EastUS come tag di servizio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere il [domande frequenti](bastion-faq.md)