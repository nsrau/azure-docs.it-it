---
title: 'Copiare e incollare da e verso una macchina virtuale: Azure Bastion | Microsoft Docs'
description: Questo articolo illustra come copiare e incollare da e verso una macchina virtuale di Azure usando Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191811"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Copiare e incollare in una macchina virtuale: Bastion Azure (anteprima)

Questo articolo consente di copiare e incollare il testo da e verso le macchine virtuali quando si usa Azure Bastion. Prima di usare una macchina virtuale, assicurarsi di aver seguito i passaggi necessari per [creare un Bastion host](bastion-create-host-portal.md). Quindi, connettersi alla macchina virtuale che si vuole usare tramite uno [RDP](bastion-connect-vm-rdp.md) oppure [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Per i browser che supportano l'accesso avanzato di API degli Appunti, è possibile copiare e incollare il testo tra il dispositivo locale e la sessione remota nello stesso modo si copia e incolla tra le applicazioni nel dispositivo locale. Per altri browser, è possibile utilizzare la tavolozza di strumento Bastion accesso negli Appunti.

  ![Consenti negli Appunti](./media/bastion-vm-manage/allow.png)

È supportato solo testo Copia/Incolla. Per diretta copia e Incolla, il browser può essere richiesto per l'accesso agli Appunti quando viene inizializzata la sessione di Bastion. **Consenti** pagina web per accedere agli Appunti.

## <a name="to"></a>Copiare in una sessione remota

Dopo la connessione alla macchina virtuale usando il [portale di Azure](https://aka.ms/BastionHost) per l'anteprima Bastion:

1. Copiare il contenuto di testo/dal dispositivo locale in locale negli Appunti.
1. Durante la sessione remota, avviare la tavolozza di strumento Bastion accesso negli Appunti selezionando le due frecce. Le frecce si trovano al centro a sinistra della sessione.

    ![tavolozza degli strumenti](./media/bastion-vm-manage/left.png)

    ![Negli Appunti](./media/bastion-vm-manage/clipboard.png)

1. In genere, il testo copiato Mostra automaticamente nella tavolozza Bastion Copia Incolla. Se il testo non è presente, quindi incollare il testo nell'area di testo nella tavolozza.
1. Una volta il testo nell'area di testo, è possibile incollarlo nella sessione remota.

    ![Incolla](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Copiare da una sessione remota

Dopo la connessione alla macchina virtuale usando il [portale di Azure](https://aka.ms/BastionHost) per l'anteprima Bastion:

1. Copiare il contenuto di testo/nella sessione remota in remoto negli Appunti (usando Ctrl + C).

    ![tavolozza degli strumenti](./media/bastion-vm-manage/remote.png)

1. Durante la sessione remota, avviare la tavolozza di strumento Bastion accesso negli Appunti selezionando le due frecce. Le frecce si trovano al centro a sinistra della sessione.

    ![Negli Appunti](./media/bastion-vm-manage/clipboard2.png)

1. In genere, il testo copiato Mostra automaticamente nella tavolozza Bastion Copia Incolla. Se il testo non è presente, quindi incollare il testo nell'area di testo nella tavolozza.
1. Una volta il testo nell'area di testo, è possibile incollarlo nel dispositivo locale.

    ![Incolla](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggere il [Bastion domande frequenti su](bastion-faq.md).