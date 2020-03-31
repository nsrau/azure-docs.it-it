---
title: Connettersi a un set di scalabilità di macchine virtuali Windows usando Azure Bastion Documenti Microsoft
description: In this article, learn how to connect to an Azure virtual machine scale set using Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988091"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Connettersi a un set di scalabilità di macchine virtuali usando Azure BastionConnect to a virtual machine scale set using Azure Bastion

Questo articolo illustra come RDP in modo sicuro e trasparente per l'istanza del set di scalabilità di macchine virtuali Windows in una rete virtuale di Azure usando Azure Bastion.This article shows you how to securely and seamlessly RDP to your Windows virtual machine scale set instance in an Azure virtual network using Azure Bastion. È possibile connettersi a un'istanza del set di scalabilità di macchine virtuali direttamente dal portale di Azure.You can connect to a virtual machine scale set instance directly from the Azure portal. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere [Panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host Di Azure Bastion per la rete virtuale in cui risiede il set di scalabilità della macchina virtuale. Per altre informazioni, vedere Creare un host del Bastione di Azure.For more information, see [Create an Azure Bastion host](bastion-create-host-portal.md). Dopo aver eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a un'istanza del set di scalabilità di macchine virtuali in questa rete virtuale. Bastion presuppone che si stia utilizzando RDP per connettersi a un set di scalabilità di macchine virtuali Windows e SSH per connettersi al set di scalabilità di macchine virtuali Linux.Bastion assumes that you are using RDP to connect to a Windows virtual machine scale set, and SSH to connect to your Linux virtual machine scale set. Per informazioni sulla connessione a una macchina virtuale Linux, vedere [Connettersi a una macchina virtuale - Linux.For](bastion-connect-vm-ssh.md)information about connection to a Linux VM, see Connect to a VM - Linux .

## <a name="connect-using-rdp"></a><a name="rdp"></a>Connettersi tramite RDP

1. Aprire il [portale di Azure](https://portal.azure.com). Passare al set di scalabilità della macchina virtuale a cui si vuole connettersi.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Passare all'istanza del set di scalabilità della macchina virtuale a cui si desidera connettersi, quindi selezionare **Connetti**. Quando si usa una connessione RDP, il set di scalabilità della macchina virtuale deve essere un set di scalabilità di macchine virtuali Windows.When using an RDP connection, the virtual machine scale set should be a Windows virtual machine scale set.

   ![set di scalabilità di macchine virtualiVirtual machine scale set](./media/bastion-connect-vm-scale-set/2.png)
3. Dopo aver selezionato **Connetti**, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Selezionare la scheda **Bastione** dalla barra laterale. Se non è stato eseguito il provisioning di Bastion per la rete virtuale, è possibile selezionare il collegamento per configurare Bastion. Per istruzioni sulla configurazione, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Scheda Bastione](./media/bastion-connect-vm-scale-set/3.png)
4. Nella scheda Bastion immettere il nome utente e la password per il set di scalabilità della macchina virtuale, quindi selezionare **Connetti**.

   ![connessione](./media/bastion-connect-vm-scale-set/4.png)
5. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

## <a name="next-steps"></a>Passaggi successivi

Leggi le domande frequenti sul [Bastione](bastion-faq.md).