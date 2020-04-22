---
title: Utilizzo di macchine virtuali e gruppi di sicurezza di rete in Azure BastionWorking with VMs and NSGs in Azure Bastion
description: Questo articolo descrive come incorporare l'accesso al gruppo di sicurezza di rete con Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732205"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Utilizzo dell'accesso NSG e del Bastione di AzureWorking with NSG access and Azure Bastion

Quando si usa Azure Bastion, è possibile usare i gruppi di sicurezza di rete. Per ulteriori informazioni, vedere [Gruppi di sicurezza](../virtual-network/security-overview.md). 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

In questo diagramma:

* L'host Bastion viene distribuito nella rete virtuale.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente passa alla macchina virtuale di Azure in RDP/SSH.
* Integrazione della connessione - Sessione RDP/SSH con un solo clic all'interno del browser
* Non è richiesto alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Gruppi di sicurezza di rete

Questa sezione illustra il traffico di rete tra l'utente e il Bastione di Azure e le macchine virtuali di destinazione nella rete virtuale:This section shows you the network traffic between the user and Azure Bastion, and through to target VMs in your virtual network:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Il Bastione di Azure viene distribuito in modo specifico in ***AzureBastionSubnet***.

* **Traffico in ingresso:**

   * **Traffico in ingresso da Internet pubblico:** Il Bastione di Azure creerà un ip pubblico che richiede la porta 443 abilitata nell'IP pubblico per il traffico in ingresso. La porta 3389/22 NON deve essere aperta in AzureBastionSubnet.Port 3389/22 are NOT required to be opened on the AzureBastionSubnet.
   * Traffico in ingresso dal piano di **controllo di Azure Bastion:Ingress Traffic from Azure Bastion control plane:** Per la connettività del piano di controllo, abilitare la porta 443 in ingresso dal tag del servizio **GatewayManager.For control** plane connectivity, enable port 443 inbound from GatewayManager service tag. In questo modo il piano di controllo, ovvero Gateway Manager, sarà in grado di comunicare con Azure Bastion.

* **Traffico in uscita:**

   * **Traffico in uscita per le macchine virtuali di destinazione:Egress Traffic to target VMs:** Il Bastione di Azure raggiungerà le macchine virtuali di destinazione tramite IP privato. I gruppi di sicurezza di rete devono consentire il traffico in uscita verso altre subnet VM di destinazione per le porte 3389 e 22.The NSGs need to allow egress traffic to other target VM subnets for port 3389 and 22.
   * **Traffico in uscita verso altri endpoint pubblici in Azure:Egress Traffic to other public endpoints in Azure:** Il bastione di Azure deve essere in grado di connettersi a vari endpoint pubblici all'interno di Azure, ad esempio per archiviare log di diagnostica e log di misurazione. Per questo motivo, Azure Bastion deve essere in uscita dal 443 al tag del servizio **AzureCloud.For** this reason, Azure Bastion needs outbound to 443 to AzureCloud service tag.

### <a name="target-vm-subnet"></a>Subnet della macchina virtuale di destinazioneTarget VM Subnet
Si tratta della subnet che contiene la macchina virtuale di destinazione in cui si desidera RDP/SSH.

   * **Traffico in ingresso dal Bastione di Azure:Ingress Traffic from Azure Bastion:** Il Bastione di Azure raggiungerà la macchina virtuale di destinazione tramite IP privato. Le porte RDP/SSH (rispettivamente porte 3389/22) devono essere aperte sul lato VM di destinazione su IP privato. Come procedura consigliata, è possibile aggiungere l'intervallo di indirizzi IP della subnet di Azure Bastion in questa regola per consentire solo a Bastion di aprire queste porte nelle macchine virtuali di destinazione nella subnet della macchina virtuale di destinazione.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere le [domande frequenti](bastion-faq.md).
