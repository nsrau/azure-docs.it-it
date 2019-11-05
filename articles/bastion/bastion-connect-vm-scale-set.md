---
title: Connettersi a un set di scalabilità di macchine virtuali Windows con Azure Bastion | Microsoft Docs
description: Questo articolo illustra come connettersi a un set di scalabilità di macchine virtuali di Azure con Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 446784155cf6f72cfaa80523ed3913eacc7e5cfc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513133"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Connettersi a un set di scalabilità di macchine virtuali con Azure Bastion

Questo articolo illustra come eseguire in modo sicuro e semplice il protocollo RDP per l'istanza del set di scalabilità di macchine virtuali Windows in una rete virtuale di Azure con il Bastion di Azure. È possibile connettersi a un'istanza del set di scalabilità di macchine virtuali direttamente dalla portale di Azure. Quando usi Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui risiede il set di scalabilità di macchine virtuali. Per altre informazioni, vedere [creare un host Bastion di Azure](bastion-create-host-portal.md). Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a un'istanza del set di scalabilità di macchine virtuali in questa rete virtuale. Bastion presuppone che si stia usando RDP per connettersi a un set di scalabilità di macchine virtuali Windows e SSH per connettersi al set di scalabilità di macchine virtuali Linux. Per informazioni sulla connessione a una VM Linux, vedere [connettersi a una VM Linux](bastion-connect-vm-ssh.md).

## <a name="rdp"></a>Connetti tramite RDP

1. Aprire il [portale di Azure](https://portal.azure.com). Passare al set di scalabilità di macchine virtuali a cui si vuole connettersi.

   ![Navigare](./media/bastion-connect-vm-scale-set/1.png)
2. Passare all'istanza del set di scalabilità di macchine virtuali a cui si vuole connettersi, quindi selezionare **Connetti**. Quando si usa una connessione RDP, il set di scalabilità di macchine virtuali deve essere un set di scalabilità di macchine virtuali Windows.

   ![set di scalabilità di macchine virtuali](./media/bastion-connect-vm-scale-set/2.png)
3. Dopo aver selezionato **Connetti**, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Selezionare la scheda **Bastion** dalla barra laterale. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, è possibile selezionare il collegamento per configurare Bastion. Per istruzioni sulla configurazione, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Scheda Bastion](./media/bastion-connect-vm-scale-set/3.png)
4. Nella scheda Bastion immettere il nome utente e la password per il set di scalabilità di macchine virtuali, quindi selezionare **Connetti**.

   ![Connettersi](./media/bastion-connect-vm-scale-set/4.png)
5. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nella portale di Azure (su HTML5) usando la porta 443 e il servizio Bastion.

## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).