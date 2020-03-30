---
title: Come connettersi a una macchina virtuale di Azure Lab Services dal Mac Documenti Microsoft
description: Questo articolo
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
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503088"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Connettersi a una macchina virtuale usando RDP in un Mac
Questa sezione mostra come uno studente può connettersi a una macchina virtuale del laboratorio in classe da un Mac tramite RDP.

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
Per informazioni su come connettersi a macchine virtuali Linux tramite RDP, vedere [Usare desktop remoto per macchine virtuali LinuxTo](how-to-use-remote-desktop-linux-student.md) learn how to connect to Linux VMs using RDP, see Use remote desktop for Linux virtual machines


