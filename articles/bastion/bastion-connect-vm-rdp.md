---
title: Connettersi a una macchina virtuale Windows con Azure Bastion
description: Questo articolo illustra come connettersi a una macchina virtuale di Azure che esegue Windows usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978133"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connettersi a una macchina virtuale Windows con Azure Bastion

Con Azure Bastion è possibile connettersi in modo sicuro e trasparente alle macchine virtuali tramite SSL direttamente nell'portale di Azure. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o un software aggiuntivo. Questo articolo illustra come connettersi alle macchine virtuali Windows. Per informazioni sulla connessione a una VM Linux, vedere [connettersi a una macchina virtuale con Azure Bastion-Linux](bastion-connect-vm-ssh.md).

Azure Bastion offre connettività sicura a tutte le macchine virtuali nella rete virtuale in cui viene effettuato il provisioning. L'uso di Azure Bastion consente di proteggere le macchine virtuali dall'esposizione delle porte RDP/SSH all'esterno, offrendo al tempo stesso l'accesso sicuro tramite RDP/SSH. Per altre informazioni, vedere la [panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-the-bastion-host"></a>Installare l'host Bastion

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui si trova la VM. Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale nella rete virtuale. Per configurare un host Bastion di Azure, vedere [creare un host Bastion di Azure](bastion-create-host-portal.md).

### <a name="required-roles"></a>Ruoli necessari

Per effettuare una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

Per connettersi alla macchina virtuale Windows, è necessario che le porte seguenti siano aperte nella macchina virtuale Windows:

* Porte in ingresso: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Connettersi

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).
