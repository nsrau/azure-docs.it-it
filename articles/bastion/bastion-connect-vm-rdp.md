---
title: Connettersi a una macchina virtuale Windows usando Azure BastionConnect to a Windows VM using Azure Bastion
description: In this article, learn how to connect to an Azure Virtual Machine running Windows by using Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597346"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Connettersi a una macchina virtuale Windows usando Azure BastionConnect to a Windows virtual machine using Azure Bastion

Con Azure Bastion è possibile connettersi in modo sicuro e trasparente alle macchine virtuali tramite SSL direttamente nel portale di Azure.Using Azure Bastion, you can securely and seamlessly connect to your virtual machines over SSL directly in the Azure portal. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Questo articolo illustra come connettersi alle macchine virtuali Windows.This article shows you how to connect to your Windows VMs. Per informazioni sulla connessione a una macchina virtuale Linux, vedere [Connettersi a una macchina virtuale usando Azure Bastion - Linux.For](bastion-connect-vm-ssh.md)information about connecting to a Linux VM, see Connect to a VM using Azure Bastion - Linux.

Azure Bastion fornisce connettività sicura a tutte le macchine virtuali nella rete virtuale in cui viene eseguito il provisioning. L'uso di Azure Bastion consente di proteggere le macchine virtuali dall'esposizione delle porte RDP/SSH all'esterno, offrendo al tempo stesso l'accesso sicuro tramite RDP/SSH. Per ulteriori informazioni, vedere [Panoramica](bastion-overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host di Azure Bastion per la rete virtuale in cui si trova la macchina virtuale. Dopo aver eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale nella rete virtuale. Per configurare un host Di Azure Bastion, vedere Creare un host Bastion di Azure.To set up [an Azure Bastion host, see Create an Azure Bastion host.](bastion-create-host-portal.md)

### <a name="required-roles"></a>Ruoli obbligatori

Per effettuare una connessione, sono necessari i ruoli seguenti:To make a connection, the following roles are required:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

To connect to the Windows VM, you must have the following ports open on your Windows VM:

* Porte in ingresso: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>connettersi

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione VMVM connect](./media/bastion-connect-vm-rdp/connect.png)
1. Dopo aver fatto clic su Bastione, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato eseguito il provisioning di Bastion per la rete virtuale, la scheda Bastione è attiva per impostazione predefinita. Se non è stato eseguito il provisioning di Bastion per la rete virtuale, è possibile fare clic sul collegamento per configurare Bastione. Per istruzioni sulla configurazione, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![bastione scheda](./media/bastion-connect-vm-rdp/bastion.png)
1. Nella scheda Bastion immettere il nome utente e la password per la macchina virtuale, quindi fare clic su **Connetti**. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   ![Connessione RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastione](bastion-faq.md)
