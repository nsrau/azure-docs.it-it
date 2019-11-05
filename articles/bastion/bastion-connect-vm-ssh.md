---
title: Connettersi a una VM Linux con Azure Bastion | Microsoft Docs
description: Questo articolo illustra come connettersi a una macchina virtuale Linux usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494471"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Connettersi tramite SSH a una macchina virtuale Linux con Azure Bastion

Questo articolo illustra come accedere in modo sicuro e trasparente alle macchine virtuali Linux in una rete virtuale di Azure. Puoi connetterti a una macchina virtuale direttamente dal portale di Azure. Quando usi Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

È possibile usare Azure Bastion per connettersi a una macchina virtuale Linux tramite SSH. Per l'autenticazione, è possibile usare sia il nome utente che la password e le chiavi SSH. È possibile connettersi alla macchina virtuale con chiavi SSH usando uno dei seguenti:

* Chiave privata da immettere manualmente
* Un file che contiene le informazioni sulla chiave privata

La chiave privata SSH deve avere un formato che inizia con `"-----BEGIN RSA PRIVATE KEY-----"` e termina con `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host Bastion di Azure](bastion-create-host-portal.md). Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. Quando si usa Bastion per connettersi, si presuppone che si usi il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle macchine virtuali Linux.

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

## <a name="username"></a>Connetti: uso del nome utente e della password

1.   Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si desidera connettersi, quindi fare clic su **Connetti**. Quando si usa una connessione SSH, la VM deve essere una macchina virtuale Linux.
1. Dopo aver fatto clic su Connetti, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Connessione VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e la password per SSH nella macchina virtuale.
1. Dopo l'immissione della chiave, fare clic sul pulsante **Connetti** .

## <a name="privatekey"></a>Connetti: immettere manualmente una chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si desidera connettersi, quindi fare clic su **Connetti**. Quando si usa una connessione SSH, la VM deve essere una macchina virtuale Linux.
1. Dopo aver fatto clic su Connetti, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Connessione VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **chiave privata SSH**.
1. Immettere la chiave privata nella **chiave privata SSH** dell'area di testo (o incollarla direttamente).
1. Dopo l'immissione della chiave, fare clic sul pulsante **Connetti** .

## <a name="ssh"></a>Connetti: uso di un file di chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si desidera connettersi, quindi fare clic su **Connetti**. Quando si usa una connessione SSH, la VM deve essere una macchina virtuale Linux.

   ![Connessione VM](./media/bastion-connect-vm-ssh/connect.png)
1. Dopo aver fatto clic su Connetti, viene visualizzata una barra laterale con tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](bastion-create-host-portal.md).

   ![Connessione VM](./media/bastion-connect-vm-ssh/bastion.png)
1. Immettere il nome utente e selezionare **chiave privata SSH da file locale**.
1. Fare clic sul pulsante **Sfoglia** (l'icona della cartella nel file locale).
1. Individuare il file, quindi fare clic su **Apri**.
1. Fare clic su **Connetti** per connettersi alla macchina virtuale. Quando si fa clic su Connetti, la connessione SSH a questa macchina virtuale verrà aperta direttamente nella portale di Azure. Questa connessione avviene tramite HTML5 usando la porta 443 nel servizio Bastion sull'IP privato della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastion](bastion-faq.md)