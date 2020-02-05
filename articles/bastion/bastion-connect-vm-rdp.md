---
title: Connettersi a una macchina virtuale Windows con Azure Bastion | Microsoft Docs
description: Questo articolo illustra come connettersi a una macchina virtuale di Azure che esegue Windows usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990488"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connettersi a una macchina virtuale Windows con Azure Bastion

Questo articolo illustra come eseguire la connessione RDP in modo sicuro e trasparente alle macchine virtuali Windows in una rete virtuale di Azure usando il Bastion di Azure. Puoi connetterti a una macchina virtuale direttamente dal portale di Azure. Quando usi Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host Bastion di Azure](bastion-create-host-portal.md). Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale.

Bastion presuppone che si usi il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle macchine virtuali Linux. Per informazioni sulla connessione a una VM Linux, vedere [connettersi a una VM Linux](bastion-connect-vm-ssh.md).

### <a name="required-roles"></a>Ruoli necessari
Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

Per connettersi alla macchina virtuale Windows tramite RDP, è necessario che le porte seguenti siano aperte nella macchina virtuale Windows:

* Porte in ingresso: RDP (3389)

## <a name="rdp"></a>Connetti tramite RDP

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si desidera connettersi, quindi fare clic su **Connetti**. La VM deve essere una macchina virtuale Windows quando si usa una connessione RDP.

   ![Connessione VM](./media/bastion-connect-vm-rdp/connect.png)
1. Dopo aver fatto clic su Connetti, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, è possibile fare clic sul collegamento per configurare Bastion. Per istruzioni sulla configurazione, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Connessione VM](./media/bastion-connect-vm-rdp/bastion.png)
1. Nella scheda Bastion, il nome utente e la password per la macchina virtuale, quindi fare clic su **Connetti**. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nella portale di Azure (su HTML5) usando la porta 443 e il servizio Bastion.

   ![Connessione VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastion](bastion-faq.md)
