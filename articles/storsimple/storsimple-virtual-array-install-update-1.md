---
title: Installare l'aggiornamento 1.0 nell'array virtuale StorSimple | Microsoft Docs
description: Descrive come usare l'interfaccia utente Web dell'array virtuale StorSimple per applicare aggiornamenti tramite il portale di Azure e gli hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installare l'aggiornamento 1.0 nell'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Questo articolo descrive i passaggi necessari per installare l'aggiornamento 1.0 nell'array virtuale StorSimple tramite l'interfaccia utente Web locale e il portale di Azure.

È necessario applicare aggiornamenti software o aggiornamenti rapidi per mantenere l'array virtuale StorSimple sempre aggiornato. Prima di applicare un aggiornamento, si consiglia di portare offline i volumi o le condivisioni, prima nell'host e poi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati. Quando i volumi o le condivisioni sono offline, è consigliabile eseguire anche un backup manuale del dispositivo.

> [!IMPORTANT]
> - L'aggiornamento 1.0 corrisponde alla versione del software **10.0.10296.0** nel dispositivo. Per informazioni sulle novità in questo aggiornamento, vedere le [note sulla versione per l'aggiornamento 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, gli eventuali I/O in corso vengono interrotti e il dispositivo registra un periodo di inattività.
>
> - L'aggiornamento 1 è disponibile nel portale di Azure solo se l'array virtuale esegue l'aggiornamento 0.6. Per gli array virtuali che eseguono una versione precedente all'aggiornamento 0.6, installare prima l'aggiornamento 0.6 e quindi installare l'aggiornamento 1.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Se si esegue l'aggiornamento 0.2 o versione successiva, è consigliabile installare gli aggiornamenti tramite il portale di Azure. La procedura del portale richiede all'utente di analizzare, scaricare e installare gli aggiornamenti. L'applicazione dell'aggiornamento tramite il portale di Azure varia in base alla versione del software in esecuzione sull'array virtuale.

 - Se l'array virtuale esegue l'aggiornamento 0.6, il portale di Azure installa direttamente l'aggiornamento 1 (10.0.10296.0) sul dispositivo. Questa procedura di aggiornamento richiede circa 7 minuti per il completamento.
 - Se l'array virtuale esegue una versione precedente all'aggiornamento 0.6, l'aggiornamento viene eseguito in due passaggi. Il portale di Azure installa prima l'aggiornamento 0.6 (10.0.10293.0) sul dispositivo. L'array virtuale viene riavviato e il portale di Azure installa quindi l'aggiornamento 1 (10.0.10296.0) sul dispositivo. Per completare questa procedura di aggiornamento sono necessari circa 15 minuti.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Al termine dell'installazione, passare al servizio Gestione dispositivi StorSimple. Selezionare **Dispositivi** e quindi selezionare e fare clic sul dispositivo appena aggiornato. Passare a **Impostazioni > Gestisci > Device Updates** (Aggiornamenti dispositivi). La versione del software visualizzata dovrebbe essere **10.0.10296.0**.

![Versione del software dopo l'aggiornamento](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Usare l'interfaccia utente Web locale

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

* Scaricare l'aggiornamento o l'hotfix
* Installare l'aggiornamento o l'hotfix

> [!IMPORTANT] 
> **Procedere con questo aggiornamento solo se si esegue l'aggiornamento 0.6 (10.0.10293.0). Se si esegue una versione precedente, [installare prima l'aggiornamento 0.6](storsimple-virtual-array-install-update-06.md) sul dispositivo e quindi applicare l'aggiornamento 1.**

### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'hotfix

Se l'array virtuale esegue l'aggiornamento 0.6, eseguire i passaggi seguenti per scaricare l'aggiornamento 1 da Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Per scaricare l'aggiornamento o l'hotfix

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa Microsoft Update Catalog nel computer per la prima volta, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero dell'hotfix da scaricare riportato nella Knowledge Base (KB). Immettere **4047203** per l'aggiornamento 1.0 e quindi fare clic su **Cerca**.
   
    Verrà visualizzato l'elenco degli aggiornamenti rapidi, tra cui l'**aggiornamento 1.0 per l'array virtuale StorSimple**.
   
    ![Cercare nel catalogo](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Fare clic su **Download**.

5. Scaricare i due file in una cartella. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

6. Aprire la cartella in cui si trovano i file.

    ![File nel pacchetto](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Vengono visualizzati due file:
    -  Un file di pacchetto autonomo Microsoft Update `WindowsTH-KB3011067-x64`. Questo file viene usato per aggiornare il software del dispositivo.
    - Un file che contiene gli aggiornamenti cumulativi per `windows8.1-kb4034681-x64` di agosto. Per altre informazioni su cosa è incluso in questo aggiornamento cumulativo, vedere [Aggiornamento cumulativo mensile di agosto](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi:

 - Di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete.
 - Che l'array virtuale esegua l'aggiornamento 0.6 (10.0.10293.0). Se si esegue una versione precedente all'aggiornamento 0.6, [installare prima l'aggiornamento 0.6](storsimple-virtual-array-install-update-06.md) e quindi installare l'aggiornamento 1.

Per completare questa procedura di aggiornamento sono necessari circa 4 minuti. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, accedere a **Manutenzione** > **Aggiornamento software**. Prendere nota della versione del software in esecuzione. **Procedere con questo aggiornamento solo se si esegue l'aggiornamento 0.6 (10.0.10293.0). Se si esegue una versione precedente, [installare prima l'aggiornamento 0.6](storsimple-virtual-array-install-update-06.md) sul dispositivo e quindi applicare l'aggiornamento 1.**
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. In **Percorso del file di aggiornamento**, immettere il nome del file dell'aggiornamento o dell'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Fare clic su **Apply**.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Verrà visualizzato un avviso. Visto che l'array virtuale è un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento il dispositivo si riavvia con un conseguente periodo di inattività. Fare clic sull'icona del segno di spunta
   
   ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. L'aggiornamento si avvia. Dopo l'aggiornamento il dispositivo si riavvia in automatico. In questo periodo di tempo l'interfaccia utente locale non è accessibile.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Al termine del riavvio si viene indirizzati alla pagina **di accesso** . Per verificare se il software del dispositivo è stato aggiornato, nell'interfaccia utente Web locale passare a **Manutenzione** > **Aggiornamento software**. Dovrebbe essere visualizzata la versione del software **10.0.0.0.0.10296** per l'aggiornamento 1.0.
   
   > [!NOTE]
   > Le versioni del software vengono riportate in modo leggermente diverso nell'interfaccia utente Web locale e nel portale di Azure. Ad esempio, l'interfaccia utente Web locale indica **10.0.0.0.0.10296**, mentre il portale di Azure indica **10.0.10296.0** per la stessa versione.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Ripetere i passaggi da 2 a 4 per installare la correzione della sicurezza di Windows usando il file `windows8.1-kb4012213-x64`. L'array virtuale viene riavviato dopo l'installazione ed è necessario accedere all'interfaccia utente Web locale.

> [!NOTE]
> Se l'aggiornamento 1 è stato applicato direttamente in un dispositivo con una versione precedente all'aggiornamento 0.6, mancheranno alcuni aggiornamenti. Contattare il supporto tecnico Microsoft per i passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

Scoprire di più su come [amministrazione StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
