---
title: 'Copiare e incollare da e verso una macchina virtuale: Azure Bastion'
description: Questo articolo illustra come copiare e incollare da e verso una macchina virtuale di Azure usando Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 6835c5e1ecbf0fc99a14754d17bb6e6380eda6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445343"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiare e incollare in una macchina virtuale: Azure Bastion

Questo articolo consente di copiare e incollare testo da e verso macchine virtuali quando si usa Azure Bastion. Prima di usare una macchina virtuale, assicurarsi di aver seguito i passaggi per [creare un host Bastion](bastion-create-host-portal.md). Connettersi quindi alla macchina virtuale che si vuole usare tramite [RDP](bastion-connect-vm-rdp.md) o [SSH](bastion-connect-vm-ssh.md).

Per i browser che supportano l'accesso all'API degli Appunti avanzati, è possibile copiare e incollare il testo tra il dispositivo locale e la sessione remota nello stesso modo in cui vengono copiati e incollati tra le applicazioni nel dispositivo locale. Per gli altri browser, è possibile usare la tavolozza degli strumenti di accesso agli Appunti Bastion.

>[!NOTE]
>Attualmente è supportata solo la copia e incolla di testo.
>

   ![Consenti appunti](./media/bastion-vm-manage/allow.png)

È supportata solo la copia e incolla di testo. Per l'operazione di copia e incolla diretta, il browser potrebbe richiedere l'accesso agli Appunti quando la sessione Bastion viene inizializzata. **Consentire** alla pagina Web di accedere agli Appunti.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copia in una sessione remota

Dopo aver eseguito la connessione alla macchina virtuale usando il [portale di Azure ](https://portal.azure.com), seguire questa procedura:

1. Copiare il testo o il contenuto dal dispositivo locale negli Appunti locali.
1. Durante la sessione remota, avviare la tavolozza degli strumenti di accesso degli Appunti Bastion selezionando le due frecce. Le frecce si trovano nel centro a sinistra della sessione.

   ![Screenshot che mostra le frecce di avvio per la tavolozza degli strumenti evidenziata sul lato sinistro della finestra.](./media/bastion-vm-manage/left.png)

   ![Screenshot Mostra gli Appunti per il testo copiato in Bastion.](./media/bastion-vm-manage/clipboard.png)
1. Il testo copiato viene in genere visualizzato automaticamente nella tavolozza copia Bastion. Se il testo non è presente, incollare il testo nell'area di testo della tavolozza.
1. Una volta che il testo è nell'area di testo, è possibile incollarlo nella sessione remota.

   ![Screenshot che mostra il pulsante copia/incolla evidenziato e una stringa di testo di esempio copiata nella sessione remota.](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Copia da una sessione remota

Dopo aver eseguito la connessione alla macchina virtuale usando il [portale di Azure ](https://portal.azure.com), seguire questa procedura:

1. Copiare il testo o il contenuto della sessione remota negli Appunti remoti (usando Ctrl + C).

   ![tavolozza degli strumenti](./media/bastion-vm-manage/remote.png)
1. Durante la sessione remota, avviare la tavolozza degli strumenti di accesso degli Appunti Bastion selezionando le due frecce. Le frecce si trovano nel centro a sinistra della sessione.

   ![Appunti](./media/bastion-vm-manage/clipboard2.png)
1. Il testo copiato viene in genere visualizzato automaticamente nella tavolozza copia Bastion. Se il testo non è presente, incollare il testo nell'area di testo della tavolozza.
1. Quando il testo è nell'area di testo, è possibile incollarlo nel dispositivo locale.

   ![incollare](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).
