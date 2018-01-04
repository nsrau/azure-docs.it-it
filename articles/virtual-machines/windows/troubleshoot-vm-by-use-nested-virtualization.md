---
title: Risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure | Microsoft Docs
description: Come risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: b50d139f62ef773fb5b9bb2638e40f8154e80979
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Risolvere i problemi relativi a una macchina virtuale di Azure usando la virtualizzazione annidata in Azure

Questo articolo illustra come creare un ambiente di virtualizzazione annidata in Microsoft Azure in modo da poter montare il disco di una macchina virtuale con problemi nell'host Hyper-V (macchina virtuale di ripristino) per procedere alla risoluzione dei problemi.

## <a name="prerequisite"></a>Prerequisito

Per montare la macchina virtuale con problemi, la macchina virtuale di ripristino deve soddisfare i prerequisiti seguenti:

-   Deve trovarsi nella stessa posizione della macchina virtuale con problemi.

-   Deve trovarsi nello stesso gruppo di risorse della macchina virtuale con problemi.

-   Deve usare lo stesso tipo di account di archiviazione (Standard o Premium) della macchina virtuale con problemi.

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>Passaggio 1: Creare una macchina virtuale di ripristino e installare il ruolo Hyper-V

1.  Creare una nuova macchina virtuale di ripristino:

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

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>Passaggio 2: Creare la macchina virtuale con problemi sul server Hyper-V della macchina virtuale di ripristino

1.  Prendere nota del nome del disco nella macchina virtuale con problemi e quindi eliminare quest'ultima. Assicurarsi di mantenere collegati tutti i dischi. 

2.  Collegare il disco del sistema operativo della macchina virtuale con problemi come disco dati della macchina virtuale di ripristino.

    1.  Dopo aver eliminato la macchina virtuale con problemi, passare alla macchina virtuale di ripristino.

    2.  Selezionare **Dischi** e quindi **Aggiungi disco dati**.

    3.  Selezionare il disco della macchina virtuale con problemi e quindi scegliere **Salva**.

3.  Dopo aver collegato il disco, stabilire una connessione Desktop remoto con la macchina virtuale di ripristino.

4.  Aprire Gestione disco (diskmgmt.msc). Verificare che il disco della macchina virtuale con problemi sia impostato su **Offline**.

5.  Aprire la console di gestione di Hyper-V: in **Server Manager** selezionare il **ruolo Hyper-V**. Fare clic con il pulsante destro del mouse sul server e quindi scegliere **Console di gestione di Hyper-V**.

6.  Nella console di gestione di Hyper-V fare clic con il pulsante destro del mouse sulla macchina virtuale di ripristino e quindi scegliere **Nuovo** > **Macchina virtuale** > **Avanti**.

7.  Digitare un nome per la macchina virtuale e quindi scegliere **Avanti**.

8.  Selezionare **Generazione 1**.

9.  Impostare almeno 1024 MB per la memoria di avvio.

10. Se applicabile, selezionare il commutatore di rete Hyper-V che è stato creato, altrimenti passare alla pagina successiva.

11. Selezionare **Connetti un disco rigido virtuale successivamente**.

    ![immagine relativa all'opzione Connetti un disco rigido virtuale successivamente](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Dopo aver completato la creazione della macchina virtuale, scegliere **Fine**.

13. Fare clic con il pulsante destro del mouse sulla macchina virtuale creata e quindi scegliere **Impostazioni**.

14. Selezionare **Controller IDE 0**, quindi **Disco rigido** e infine fare clic su **Aggiungi**.

    ![immagine relativa all'aggiunta del nuovo disco rigido](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. In **Disco rigido fisico** selezionare il disco della macchina virtuale con problemi che è stato collegato alla macchina virtuale di Azure. Se nell'elenco non sono presenti dischi, controllare se il disco è impostato su offline usando Gestione disco.

    ![immagine relativa al montaggio del disco](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Selezionare **Apply** (Applica) e quindi **OK**.

18. Fare doppio clic sulla macchina virtuale e quindi avviarla.

19. È ora possibile usare la macchina virtuale come macchina virtuale locale e quindi eseguire le procedure di risoluzione dei problemi necessarie.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Passaggio 3: Creare nuovamente la macchina virtuale di Azure in Azure

1.  Dopo aver ripristinato la macchina virtuale online, arrestare la macchina virtuale nella console di gestione di Hyper-V.

2.  Passare al [portale di Azure](https://portal.azure.com), selezionare la macchina virtuale di ripristino, scegliere Dischi e quindi copiare il nome del disco, che verrà usato nel passaggio successivo. Scollegare il disco rigido dalla macchina virtuale di ripristino.

3.  Passare a **Tutte le risorse**, cercare il nome del disco e quindi selezionare il disco.

     ![immagine relativa alla ricerca del disco](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Fare clic su **Crea macchina virtuale**.

     ![immagine relativa alla creazione della macchina virtuale dal disco](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Per creare la macchina virtuale dal disco è anche possibile usare Azure PowerShell. Per altre informazioni, vedere [Creare la nuova macchina virtuale da un disco esistente usando PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Passaggi successivi

Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md).
