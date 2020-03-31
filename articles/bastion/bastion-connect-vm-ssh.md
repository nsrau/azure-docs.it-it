---
title: Connettersi a una macchina virtuale Linux usando Azure BastionConnect to a Linux VM using Azure Bastion
description: In this article, learn how to connect to Linux Virtual Machine by using Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596828"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Connettersi usando SSH a una macchina virtuale Linux usando Azure BastionConnect using SSH to a Linux virtual machine using Azure Bastion

Questo articolo illustra come sdesktopare in modo sicuro e trasparente SSH per le macchine virtuali Linux in una rete virtuale di Azure.This article shows you how to securely and seamless ly seamless SSH to your Linux VMs in an Azure virtual network. È possibile connettersi a una macchina virtuale direttamente dal portale di Azure.You can connect to a VM directly from the Azure portal. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere [Panoramica](bastion-overview.md).

È possibile usare Azure Bastion per connettersi a una macchina virtuale Linux usando SSH.You can use Azure Bastion to connect to a Linux virtual machine using SSH. È possibile utilizzare sia nome utente/password che chiavi SSH per l'autenticazione. È possibile connettersi alla macchina virtuale con chiavi SSH usando:You can connect to your VM with SSH keys by using either:

* Una chiave privata immessa manualmente
* Un file che contiene le informazioni sulla chiave privata

La chiave privata SSH deve essere `"-----BEGIN RSA PRIVATE KEY-----"` in un `"-----END RSA PRIVATE KEY-----"`formato che inizia con e termina con .

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host di Azure Bastion per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere Creare un host del Bastione di Azure.For more information, see [Create an Azure Bastion host](bastion-create-host-portal.md). Dopo aver eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. 

Quando si usa Bastion per connettersi, si presuppone che si stia usando RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle macchine virtuali Linux.When you use Bastion to connect, it assumes that you are using RDP to connect to a Windows VM, and SSH to connect to your Linux VMs. Per informazioni sulla connessione a una macchina virtuale Windows, vedere [Connettersi a una macchina virtuale - Windows.For](bastion-connect-vm-rdp.md)information about connecting to a Windows VM, see Connect to a VM - Windows .

### <a name="required-roles"></a>Ruoli obbligatori

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

Per connettersi alla macchina virtuale Linux tramite SSH, è necessario che le porte seguenti siano aperte nella macchina virtuale:In order to connect to the Linux VM via SSH, you must have the following ports open on your VM:

* Porta in ingresso: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Connetti: utilizzo di nome utente e password

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastione, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato eseguito il provisioning di Bastion per la rete virtuale, la scheda Bastione è attiva per impostazione predefinita. Se non è stato eseguito il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e la password per SSH nella macchina virtuale.
1. Fare clic sul pulsante **Connetti** dopo aver immesso il tasto.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connetti: immettere manualmente una chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastione, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato eseguito il provisioning di Bastion per la rete virtuale, la scheda Bastione è attiva per impostazione predefinita. Se non è stato eseguito il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **SSH Private Key**.
1. Immettere la chiave privata nell'area di testo **SSH chiave privata** (o incollarlo direttamente).
1. Fare clic sul pulsante **Connetti** dopo aver immesso il tasto.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Connetti: utilizzo di un file di chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastione, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato eseguito il provisioning di Bastion per la rete virtuale, la scheda Bastione è attiva per impostazione predefinita. Se non è stato eseguito il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **SSH Private Key from Local File**.
1. Fare clic sul pulsante **Sfoglia** (l'icona della cartella nel file locale).
1. Cercare il file, quindi fare clic su **Apri**.
1. Fare clic su **Connetti** per connettersi alla macchina virtuale. Dopo aver fatto clic su Connetti, SSH a questa macchina virtuale verrà aperto direttamente nel portale di Azure.Once click Connect, SSH to this virtual machine will directly open in the Azure portal. Questa connessione è su HTML5 utilizzando la porta 443 sul servizio Bastion sopra l'IP privato della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastione](bastion-faq.md)
