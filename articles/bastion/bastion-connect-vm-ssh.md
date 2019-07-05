---
title: Connettersi a una VM Linux usando Azure Bastion | Microsoft Docs
description: In questo articolo, informazioni su come connettersi alla macchina virtuale Linux usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477769"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Connettersi tramite SSH a una macchina virtuale Linux usando Azure Bastion (anteprima)

Questo articolo illustra i passaggi a in modo sicuro e senza problemi SSH per le VM Linux in una rete virtuale di Azure. Puoi connetterti a una macchina virtuale direttamente dal portale di Azure. Quando usi Azure Bastion, le macchine virtuali non richiedono un client, un agente o software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

È possibile usare Azure Bastion per connettersi a una macchina virtuale Linux tramite SSH. È possibile usare sia il nome utente/password e chiavi SSH per l'autenticazione. È possibile connettersi alla macchina virtuale con le chiavi SSH usando:

* Una chiave privata che si immette manualmente
* Un file che contiene le informazioni sulla chiave private

La chiave privata SSH deve essere in un formato che inizia con `"-----BEGIN RSA PRIVATE KEY-----"` e termina con `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi che è stato impostato in un host di Azure Bastion per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host di Azure Bastion](bastion-create-host-portal.md). Una volta che il servizio Bastion è stato effettuato il provisioning e distribuito nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. In questa versione di anteprima quando si usa Bastion per connettersi, si presuppone che si usa il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle VM Linux.

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

## <a name="username"></a>Connessione: Usando nome utente e password


1.  Uso [questo collegamento](https://aka.ms/BastionHost) per aprire la pagina del portale di anteprima di Azure Bastion. Passare alla macchina virtuale che si desidera connettersi, quindi fare clic su **Connect**. La macchina virtuale deve essere una macchina virtuale Linux quando si usa una connessione SSH.
1. Dopo aver scelto di Connect, viene visualizzata una barra sul lato che contiene tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se si non esegue il provisioning di Bastion per la rete virtuale, vedere [Bastion configurare](bastion-create-host-portal.md). Se non viene visualizzata **Bastion** elencate, non è stato aperto il portale di anteprima. Aprire il portale usando [questo collegamento](https://aka.ms/BastionHost).

      ![Connessione macchina virtuale](./media/bastion-connect-vm-ssh/bastion.png)

1. Immettere il nome utente e la password per SSH alla macchina virtuale.
1. Fare clic su **Connect** pulsante dopo l'immissione della chiave.

## <a name="privatekey"></a>Connessione: Immettere manualmente una chiave privata

1.  Uso [questo collegamento](https://aka.ms/BastionHost) per aprire la pagina del portale di anteprima di Azure Bastion. Passare alla macchina virtuale che si desidera connettersi, quindi fare clic su **Connect**. La macchina virtuale deve essere una macchina virtuale Linux quando si usa una connessione SSH.
1. Dopo aver scelto di Connect, viene visualizzata una barra sul lato che contiene tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se si non esegue il provisioning di Bastion per la rete virtuale, vedere [Bastion configurare](bastion-create-host-portal.md). Se non viene visualizzata **Bastion** elencate, non è stato aperto il portale di anteprima. Aprire il portale usando [questo collegamento](https://aka.ms/BastionHost).

      ![Connessione macchina virtuale](./media/bastion-connect-vm-ssh/bastion.png)

1. Immettere il nome utente e selezionare **chiave privata SSH**.
1. Immettere la chiave privata nell'area di testo **chiave privata SSH** (o incollarlo direttamente).
1. Fare clic su **Connect** pulsante dopo l'immissione della chiave.

## <a name="ssh"></a>Connessione: Usando un file di chiave privata

1.  Uso [questo collegamento](https://aka.ms/BastionHost) per aprire la pagina del portale di anteprima di Azure Bastion. Passare alla macchina virtuale che si desidera connettersi, quindi fare clic su **Connect**. La macchina virtuale deve essere una macchina virtuale Linux quando si usa una connessione SSH.

    ![Connessione macchina virtuale](./media/bastion-connect-vm-ssh/connect.png)

1. Dopo aver scelto di Connect, viene visualizzata una barra sul lato che contiene tre schede: RDP, SSH e Bastion. Se è stato effettuato il provisioning di Bastion per la rete virtuale, la scheda Bastion è attiva per impostazione predefinita. Se si non esegue il provisioning di Bastion per la rete virtuale, vedere [Bastion configurare](bastion-create-host-portal.md). Se non viene visualizzata **Bastion** elencate, non è stato aperto il portale di anteprima. Aprire il portale usando [questo collegamento](https://aka.ms/BastionHost).

    ![Connessione macchina virtuale](./media/bastion-connect-vm-ssh/bastion.png)

1. Immettere il nome utente e selezionare **chiave privata SSH dal File locale**.
1. Scegliere il **esplorare** (l'icona della cartella nel file locale).
1. Cercare il file, quindi fare clic su **aperto**.
1. Fare clic su **Connect** per connettersi alla macchina virtuale. Quando fa clic su Connetti, SSH alla macchina virtuale verrà aprire direttamente nel portale di Azure. Questa connessione è su HTML5 usando la porta 443 nel servizio Bastion tramite l'indirizzo IP privato della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Lettura di [Bastion domande frequenti](bastion-faq.md)
