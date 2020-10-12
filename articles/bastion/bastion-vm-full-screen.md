---
title: 'Azure Bastion: visualizzare la sessione della macchina virtuale: schermo intero'
description: Informazioni su come modificare la visualizzazione della macchina virtuale a schermo intero e viceversa nel browser per una connessione RDP o SSH in Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: fa795d7b066733aba54f8612bbabecf264a11c70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270292"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Passare alla visualizzazione a schermo intero per una sessione VM: Azure Bastion

Questo articolo consente di modificare la visualizzazione della macchina virtuale a schermo intero e di nuovo nel browser. Prima di usare una macchina virtuale, assicurarsi di aver seguito i passaggi per [creare un host Bastion](bastion-create-host-portal.md). Connettersi quindi alla macchina virtuale che si vuole usare tramite [RDP](bastion-connect-vm-rdp.md) o [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Avvia lo strumento appunti

Durante la sessione remota, avviare la tavolozza degli strumenti di accesso degli Appunti Bastion selezionando le due frecce situate a sinistra del centro della sessione.

![tools](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Selezione schermo intero

Selezionare il pulsante **FullScreen** per impostare la sessione su un'esperienza a schermo intero. Dopo l'attivazione, la sessione viene reinizializzata a schermo intero.

![schermo intero](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).
Informazioni su come [copiare e incollare](bastion-vm-copy-paste.md) da e verso una macchina virtuale di Azure.
