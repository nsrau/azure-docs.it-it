---
title: Risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure | Microsoft Docs
description: Come risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: 4565eb86727e768ba894d701cbc5e0073c07ee01
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185517"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure

Questo articolo illustra come creare un ambiente di virtualizzazione annidata in Microsoft Azure in modo da poter montare il disco di una macchina virtuale con problemi nell'host Hyper-V (macchina virtuale di ripristino) per procedere alla risoluzione dei problemi.

## <a name="prerequisites"></a>prerequisiti

Per montare la macchina virtuale con problemi, la macchina virtuale di ripristino deve soddisfare i prerequisiti seguenti:

-   Deve trovarsi nella stessa posizione della macchina virtuale con problemi.

-   Deve trovarsi nello stesso gruppo di risorse della macchina virtuale con problemi.

-   Deve usare lo stesso tipo di account di archiviazione (Standard o Premium) della macchina virtuale con problemi.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Passaggio 1: Creare una macchina virtuale di ripristino e installare il ruolo Hyper-V

1.  Crea una nuova macchina virtuale di ripristino:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Dimensione: qualsiasi macchina virtuale della serie V3 con almeno due core che supportano la virtualizzazione annidata. Per altre informazioni, vedere [Introducing the new Dv3 and Ev3 VM sizes](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) (Introduzione alle nuove dimensioni Dv3 ed Ev3 delle macchine virtuali).

    -  Posizione, account di archiviazione e gruppo di risorse identici a quelli della macchina virtuale con problemi.

    -  Selezionare lo stesso tipo di archiviazione della macchina virtuale con problemi (Standard o Premium).

2.  Dopo aver creato la macchina virtuale di ripristino, stabilire una connessione Desktop remoto con la macchina virtuale di ripristino.

3.  In Server Manager scegliere **Gestione** > **Aggiungi ruoli e funzionalità**.

4.  Nella sezione **Tipo di installazione** selezionare **Installazione basata su ruoli o basata su funzionalità**.

5.  Nella sezione **Selezione server di destinazione** verificare che la macchina virtuale di ripristino sia selezionata.

6.  Selezionare il **ruolo Hyper-V** > **Aggiungi funzionalità**.

7.  Scegliere **Avanti** nella sezione **Funzionalità**.

8.  Se è disponibile un commutatore virtuale, selezionarlo. In caso contrario, scegliere **Avanti**.

9.  Nella sezione **Migrazione** scegliere **Avanti**.

10. Nella sezione **Archivi predefiniti** scegliere **Avanti**.

11. Selezionare la casella per riavviare automaticamente il server, se necessario.

12. Selezionare **Installa**.

13. Consentire al server di installare il ruolo Hyper-V. Questa operazione richiede alcuni minuti e il server verrà riavviato automaticamente.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Passaggio 2: Creare la macchina virtuale con problemi sul server Hyper-V della macchina virtuale di ripristino

1.  [Creare un disco snapshot](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) per il disco del sistema operativo della macchina virtuale in cui si è verificato il problema e quindi alleghi il disco snapshot alla macchina virtuale ricusare.

2.  Desktop remoto per la macchina virtuale di ripristino.

3.  Aprire Gestione disco (diskmgmt.msc). Verificare che il disco della macchina virtuale con problemi sia impostato su **Offline**.

4.  Aprire la console di gestione di Hyper-V: in **Server Manager** selezionare il **ruolo Hyper-V**. Fare clic con il pulsante destro del mouse sul server e quindi scegliere **Console di gestione di Hyper-V**.

5.  Nella console di gestione di Hyper-V fare clic con il pulsante destro del mouse sulla macchina virtuale di ripristino e quindi scegliere **Nuovo** > **Macchina virtuale** > **Avanti**.

6.  Digitare un nome per la macchina virtuale e quindi scegliere **Avanti**.

7.  Selezionare **Generazione 1**.

8.  Impostare almeno 1024 MB per la memoria di avvio.

9. Se applicabile, selezionare il commutatore di rete Hyper-V che è stato creato, altrimenti passare alla pagina successiva.

10. Selezionare **Connetti un disco rigido virtuale successivamente**.

    ![immagine relativa all'opzione Connetti un disco rigido virtuale successivamente](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Dopo aver completato la creazione della macchina virtuale, scegliere **Fine**.

12. Fare clic con il pulsante destro del mouse sulla macchina virtuale creata e quindi scegliere **Impostazioni**.

13. Selezionare **Controller IDE 0**, quindi **Disco rigido** e infine fare clic su **Aggiungi**.

    ![immagine relativa all'aggiunta del nuovo disco rigido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. In **Disco rigido fisico** selezionare il disco della macchina virtuale con problemi che è stato collegato alla macchina virtuale di Azure. Se nell'elenco non sono presenti dischi, controllare se il disco è impostato su offline usando Gestione disco.

    ![immagine relativa al montaggio del disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selezionare **Apply** (Applica) e quindi **OK**.

16. Fare doppio clic sulla macchina virtuale e quindi avviarla.

17. È ora possibile usare la macchina virtuale come macchina virtuale locale e quindi eseguire le procedure di risoluzione dei problemi necessarie.

## <a name="step-3-replace-the-os-disk-used-by-the-problem-vm"></a>Passaggio 3: sostituire il disco del sistema operativo usato dalla macchina virtuale con problemi

1.  Dopo aver ripristinato la macchina virtuale online, arrestare la macchina virtuale nella console di gestione di Hyper-V.

2.  [Smontare e scollegare il disco del sistema operativo riparato](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Sostituire il disco del sistema operativo usato dalla VM con il disco del sistema operativo riparato](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Passaggi successivi

Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md).
