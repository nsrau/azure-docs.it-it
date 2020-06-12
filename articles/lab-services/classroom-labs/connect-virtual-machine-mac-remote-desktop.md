---
title: Come connettersi a una macchina virtuale di Azure Lab Services da un Mac | Microsoft Docs
description: Informazioni su come connettersi da un Mac a una macchina virtuale in Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704366"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Connettersi a una macchina virtuale con Remote Desktop Protocol in un Mac
Questa sezione illustra come uno studente può connettersi a una macchina virtuale di un lab per le classi da un Mac usando Remote Desktop Protocol (RDP).

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installare Desktop remoto Microsoft in un Mac
1. Aprire l'App Store nel Mac e cercare **Desktop remoto Microsoft.**

    ![Desktop remoto Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installare la versione più recente di Desktop remoto Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Accedere alla macchina virtuale dal Mac usando RDP
1. Aprire il file **RDP** scaricato nel computer in cui è installato **Desktop remoto Microsoft**. Dovrebbe iniziare a connettersi alla macchina virtuale. 

    ![Connettersi alla macchina virtuale](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selezionare **Continua** se viene visualizzato il seguente avviso. 

    ![Avviso del certificato](../media/how-to-use-classroom-lab/certificate-error.png)
1. Verrà visualizzata la macchina virtuale. 

    > [!NOTE]
    > L'esempio seguente è relativo a una VM Linux CentOS. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come connettersi alle macchine virtuali Linux usando RDP, vedere [Usare Desktop remoto per le macchine virtuali Linux](how-to-use-remote-desktop-linux-student.md)


