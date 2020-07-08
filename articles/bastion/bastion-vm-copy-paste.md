---
title: 'Copiare e incollare da e verso una macchina virtuale: Azure Bastion'
description: Questo articolo illustra come copiare e incollare da e verso una macchina virtuale di Azure usando Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 4e818617b70e874153e026bcf40b6dfee8a8e04e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744239"
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

   ![tavolozza degli strumenti](./media/bastion-vm-manage/left.png)

   ![Appunti](./media/bastion-vm-manage/clipboard.png)
1. Il testo copiato viene in genere visualizzato automaticamente nella tavolozza copia Bastion. Se il testo non è presente, incollare il testo nell'area di testo della tavolozza.
1. Una volta che il testo è nell'area di testo, è possibile incollarlo nella sessione remota.

   ![incollare](./media/bastion-vm-manage/local.png)

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
