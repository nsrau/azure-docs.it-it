---
title: Connettersi a una VM Windows tramite Azure Bastion | Microsoft Docs
description: In questo articolo, informazioni su come connettersi a una macchina virtuale che esegue Windows usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478410"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Connettersi a una macchina virtuale Windows tramite Azure Bastion (anteprima)

Questo articolo illustra come in modo sicuro e senza problemi RDP alle macchine virtuali Windows in Azure virtuale di rete tramite Azure Bastion. Puoi connetterti a una macchina virtuale direttamente dal portale di Azure. Quando usi Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi che è stato impostato in un host di Azure Bastion per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host di Azure Bastion](bastion-create-host-portal.md). Una volta che il servizio Bastion è stato effettuato il provisioning e distribuito nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. In questa versione di anteprima Bastion presuppone che si usa il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle VM Linux. Per informazioni sulla connessione a una VM Linux, vedere [Connetti a una macchina virtuale - Linux](bastion-connect-vm-ssh.md).

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

## <a name="rdp"></a>Connettersi tramite RDP

1. Uso [questo collegamento](https://aka.ms/BastionHost) per aprire la pagina del portale di anteprima di Azure Bastion. Passare alla macchina virtuale che si desidera connettersi, quindi fare clic su **Connect**. La macchina virtuale deve essere una macchina virtuale di Windows quando si usa una connessione RDP.

    ![Connessione macchina virtuale](./media/bastion-connect-vm-rdp/connect.png)

1. Dopo aver scelto di Connect, viene visualizzata una barra sul lato che contiene tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se si non esegue il provisioning di Bastion per la rete virtuale, è possibile fare clic sul collegamento per configurare Bastion. Per istruzioni sulla configurazione, vedere [Bastion configurare](bastion-create-host-portal.md). Se non viene visualizzata **Bastion** elencate, non è stato aperto il portale di anteprima. Aprire il portale Usa questa [collegamento di anteprima](https://aka.ms/BastionHost).

    ![Connessione macchina virtuale](./media/bastion-connect-vm-rdp/bastion.png)

1. Nella scheda Bastion, il nome utente e password per la macchina virtuale, quindi fare clic su **Connect**. La connessione RDP alla macchina virtuale tramite Bastion aprirà direttamente nel portale di Azure (su HTML5) usando la porta 443 e il servizio di Bastion.

    ![Connessione macchina virtuale](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passaggi successivi

Lettura di [Bastion domande frequenti](bastion-faq.md)
