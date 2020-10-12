---
title: Connettersi a una macchina virtuale Windows con Azure Bastion
description: Questo articolo illustra come connettersi a una macchina virtuale di Azure che esegue Windows usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744324"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connettersi a una macchina virtuale Windows con Azure Bastion

Con Azure Bastion è possibile connettersi in modo sicuro e trasparente alle macchine virtuali tramite SSL direttamente nell'portale di Azure. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o un software aggiuntivo. Questo articolo illustra come connettersi alle macchine virtuali Windows. Per informazioni sulla connessione a una VM Linux, vedere [connettersi a una macchina virtuale con Azure Bastion-Linux](bastion-connect-vm-ssh.md).

Azure Bastion offre connettività sicura a tutte le macchine virtuali nella rete virtuale in cui viene effettuato il provisioning. L'uso di Azure Bastion consente di proteggere le macchine virtuali dall'esposizione delle porte RDP/SSH all'esterno, offrendo al tempo stesso l'accesso sicuro tramite RDP/SSH. Per altre informazioni, vedere la [panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

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

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione VM](./media/bastion-connect-vm-rdp/connect.png)
1. Dopo aver fatto clic su Bastion, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, è possibile fare clic sul collegamento per configurare Bastion. Per istruzioni sulla configurazione, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![scheda Bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. Nella scheda Bastion immettere il nome utente e la password per la macchina virtuale, quindi fare clic su **Connetti**. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   ![Connessione RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastion](bastion-faq.md)
