---
title: Connettersi a una macchina virtuale Windows con Azure Bastion
description: Questo articolo illustra come connettersi a una macchina virtuale di Azure che esegue Windows usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521536"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connettersi a una macchina virtuale Windows con Azure Bastion

Con Azure Bastion è possibile connettersi in modo sicuro e trasparente alle macchine virtuali tramite SSL direttamente nell'portale di Azure. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o un software aggiuntivo. Questo articolo illustra come connettersi alle macchine virtuali Windows. Per informazioni sulla connessione a una VM Linux, vedere [connettersi a una VM Linux](bastion-connect-vm-ssh.md).

Azure Bastion offre connettività sicura a tutte le macchine virtuali nella rete virtuale in cui viene effettuato il provisioning. L'uso di Azure Bastion consente di proteggere le macchine virtuali dall'esposizione delle porte RDP/SSH all'esterno, offrendo al tempo stesso l'accesso sicuro tramite RDP/SSH. Per ulteriori informazioni, vedere la pagina relativa a [What is Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i criteri seguenti:

* Un VNet con l'host Bastion già installato.

   Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui si trova la VM. Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale nella rete virtuale. Per configurare un host Bastion di Azure, vedere [creare un host Bastion](tutorial-create-host-portal.md#createhost).
* Una macchina virtuale Windows nella rete virtuale.
* I ruoli obbligatori seguenti:
  * Ruolo Lettore nella macchina virtuale.
  * Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.
  * Ruolo Lettore nella risorsa Azure Bastion.
* Porte: per connettersi alla VM Windows, devono essere aperte le porte seguenti nella VM:
  * Porte in ingresso: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Connettersi

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).