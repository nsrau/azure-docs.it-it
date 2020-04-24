---
title: Connettersi a una VM Linux con Azure Bastion
description: Questo articolo illustra come connettersi a una macchina virtuale Linux usando Azure Bastion.
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
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Connettersi tramite SSH a una macchina virtuale Linux con Azure Bastion

Questo articolo illustra come accedere in modo sicuro e trasparente alle macchine virtuali Linux in una rete virtuale di Azure. È possibile connettersi a una macchina virtuale direttamente dalla portale di Azure. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o un software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

È possibile usare Azure Bastion per connettersi a una macchina virtuale Linux tramite SSH. Per l'autenticazione, è possibile usare sia il nome utente che la password e le chiavi SSH. È possibile connettersi alla macchina virtuale con chiavi SSH usando uno dei seguenti:

* Chiave privata da immettere manualmente
* Un file che contiene le informazioni sulla chiave privata

La chiave privata SSH deve essere in un formato che inizia con `"-----BEGIN RSA PRIVATE KEY-----"` e termina con `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host Bastion di Azure](bastion-create-host-portal.md). Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. 

Quando si usa Bastion per connettersi, si presuppone che si usi il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle macchine virtuali Linux. Per informazioni sulla connessione a una macchina virtuale Windows, vedere [connettersi a una VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Ruoli necessari

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

Per connettersi alla VM Linux tramite SSH, è necessario che le porte seguenti siano aperte nella macchina virtuale:

* Porta in ingresso: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Connetti: uso del nome utente e della password

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastion, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e la password per SSH nella macchina virtuale.
1. Dopo l'immissione della chiave, fare clic sul pulsante **Connetti** .

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connetti: immettere manualmente una chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastion, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **chiave privata SSH**.
1. Immettere la chiave privata nella **chiave privata SSH** dell'area di testo (o incollarla direttamente).
1. Dopo l'immissione della chiave, fare clic sul pulsante **Connetti** .

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Connetti: uso di un file di chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   ![Connessione](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Bastion, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **chiave privata SSH da file locale**.
1. Fare clic sul pulsante **Sfoglia** (l'icona della cartella nel file locale).
1. Individuare il file, quindi fare clic su **Apri**.
1. Fare clic su **Connetti** per connettersi alla macchina virtuale. Quando si fa clic su Connetti, la connessione SSH a questa macchina virtuale verrà aperta direttamente nella portale di Azure. Questa connessione avviene tramite HTML5 usando la porta 443 nel servizio Bastion sull'IP privato della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastion](bastion-faq.md)
