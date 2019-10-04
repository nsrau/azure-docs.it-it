---
title: Modifica vista della sessione macchina virtuale a schermo intero in Azure Bastion | Microsoft Docs
description: In questo articolo, informazioni su come modificare la visualizzazione a schermo intero.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191551"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>Passare alla visualizzazione a schermo intero per una sessione di macchina virtuale: Bastion Azure (anteprima)

Questo articolo consente di modificare la visualizzazione della macchina virtuale a schermo intero e tornare nel browser. Prima di usare una macchina virtuale, assicurarsi di aver seguito i passaggi necessari per [creare un Bastion host](bastion-create-host-portal.md). Quindi, connettersi alla macchina virtuale che si vuole usare tramite uno [RDP](bastion-connect-vm-rdp.md) oppure [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="launch-the-clipboard-tool"></a>Avviare lo strumento negli Appunti

Durante la sessione remota, avviare la tavolozza di strumento Bastion accesso negli Appunti selezionando le due frecce che si trovano al centro a sinistra della sessione.

![strumenti](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Selezionare modalità schermo intero

Selezionare il **a schermo intero** pulsante per passare la sessione per un'esperienza a schermo intero. Dopo aver abilitato, la sessione verrà reinizializzato a schermo intero.

![schermo intero](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggere il [Bastion domande frequenti su](bastion-faq.md).
Informazioni su come [copia e Incolla](bastion-vm-copy-paste.md) da e verso una macchina virtuale di Azure.