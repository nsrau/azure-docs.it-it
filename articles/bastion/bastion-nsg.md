---
title: Uso di macchine virtuali e gruppi in Azure Bastion | Microsoft Docs
description: Questo articolo descrive come incorporare l'accesso NSG con Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: 24279ff81daf0a350aa5234e78f27a99b7e4a03e
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528018"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Uso di NSG Access e Azure Bastion

Quando si lavora con Azure Bastion, è possibile usare i gruppi di sicurezza di rete (gruppi). Per ulteriori informazioni, vedere [gruppi di sicurezza](../virtual-network/security-overview.md). 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

In questo diagramma:

* L'host Bastion viene distribuito nella rete virtuale.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente passa alla macchina virtuale di Azure in RDP/SSH.
* Connettere l'integrazione: sessione RDP/SSH con singolo clic all'interno del browser
* Non è richiesto alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="nsg"></a>Gruppi di sicurezza di rete

Questa sezione illustra il traffico di rete tra l'utente e il Bastione di Azure e per le macchine virtuali di destinazione nella rete virtuale:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion viene distribuito in modo specifico a AzureBastionSubnet.

* **Traffico in ingresso:**

   * **Traffico in ingresso da Internet pubblico:** Azure Bastion creerà un IP pubblico che richiede la porta 443 abilitata nell'indirizzo IP pubblico per il traffico in ingresso. NON è necessario aprire la porta 3389/22 nella AzureBastionSubnet.
   * **Traffico in ingresso dal piano di controllo Bastion di Azure:** Per la connettività del piano di controllo, abilitare la porta 443 in ingresso dal tag del servizio **GatewayManager** . In questo modo, il piano di controllo, ovvero Gestione Gateway, sarà in grado di comunicare con Azure Bastion.

* **Traffico in uscita:**

   * **Traffico in uscita per le macchine virtuali di destinazione:** Azure Bastion raggiungerà le VM di destinazione tramite un indirizzo IP privato. Il gruppi deve consentire il traffico in uscita ad altre subnet VM di destinazione per la porta 3389 e 22.
   * **Traffico in uscita verso altri endpoint pubblici in Azure:** Azure Bastion deve essere in grado di connettersi a diversi endpoint pubblici in Azure, ad esempio per archiviare i log di diagnostica e i log di misurazione. Per questo motivo, Azure Bastion deve essere in uscita da 443 al tag del servizio **AzureCloud** .

* **Subnet VM di destinazione:** Si tratta della subnet che contiene la macchina virtuale di destinazione a cui si desidera connettersi tramite RDP/SSH.

   * **Traffico in ingresso da Azure Bastion:** Azure Bastion raggiungerà la macchina virtuale di destinazione tramite un indirizzo IP privato. Le porte RDP/SSH (rispettivamente le porte 3389/22) devono essere aperte sul lato della macchina virtuale di destinazione su un indirizzo IP privato. Come procedura consigliata, è possibile aggiungere l'intervallo di indirizzi IP della subnet di Azure Bastion in questa regola per consentire solo a Bastion di aprire queste porte nelle VM di destinazione nella subnet VM di destinazione.

## <a name="apply"></a>Applicare gruppi a AzureBastionSubnet

Se si crea e si applica un NSG a ***AzureBastionSubnet***, assicurarsi di avere aggiunto le regole seguenti nel NSG. Se non si aggiungono queste regole, la creazione/aggiornamento di NSG avrà esito negativo:

* **Connettività del piano di controllo:** In ingresso su 443 da GatewayManager
* **Registrazione diagnostica e altro:** In uscita da 443 a AzureCloud. I tag a livello di area all'interno di questo tag di servizio non sono ancora supportati.
* **VM di destinazione:** In uscita per 3389 e 22 a VirtualNetwork

Un esempio di regola NSG è disponibile per riferimento in questo [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere le [domande frequenti](bastion-faq.md).