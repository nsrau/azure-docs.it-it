---
title: Installare l'aggiornamento 0.6 nell'array virtuale StorSimple | Microsoft Docs
description: Descrive come usare l'interfaccia utente Web dell'array virtuale StorSimple per applicare aggiornamenti tramite il portale di Azure e gli hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installare l'aggiornamento 0.6 nell'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Questo articolo descrive i passaggi necessari per installare l'aggiornamento 0.6 nell'array virtuale StorSimple tramite l'interfaccia utente Web locale e il portale di Azure. È necessario applicare aggiornamenti software o aggiornamenti rapidi per mantenere l'array virtuale StorSimple sempre aggiornato.

Prima di applicare un aggiornamento, si consiglia di portare offline i volumi o le condivisioni, prima nell'host e poi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati. Quando i volumi o le condivisioni sono offline, è consigliabile eseguire anche un backup manuale del dispositivo.

> [!IMPORTANT]
> - L'aggiornamento 0.6 corrisponde alla versione del software **10.0.10293.0** nel dispositivo. Per informazioni sulle novità in questo aggiornamento, vedere le [note sulla versione per l'aggiornamento 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Se si esegue l'aggiornamento 0.2 o versione successiva, è consigliabile installare gli aggiornamenti tramite il portale di Azure. Se si esegue l'aggiornamento 0.1 o una versione del software disponibile a livello generale, è necessario usare il metodo hotfix tramite l'interfaccia utente Web locale per installare l'aggiornamento 0.6.
>
> - Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, gli eventuali I/O in corso vengono interrotti e il dispositivo registra un periodo di inattività.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Se si esegue l'aggiornamento 0.2 o versione successiva, è consigliabile installare gli aggiornamenti tramite il portale di Azure. La procedura del portale richiede all'utente di analizzare, scaricare e installare gli aggiornamenti. Questa procedura di aggiornamento richiede circa 7 minuti per il completamento. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Al termine dell'installazione, passare al servizio Gestione dispositivi StorSimple. Selezionare **Dispositivi** e quindi selezionare e fare clic sul dispositivo appena aggiornato. Passare a **Impostazioni > Gestisci > Device Updates** (Aggiornamenti dispositivi). La versione del software visualizzata dovrebbe essere **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Usare l'interfaccia utente Web locale

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

* Scaricare l'aggiornamento o l'hotfix
* Installare l'aggiornamento o l'hotfix

### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Per scaricare l'aggiornamento o l'hotfix

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa Microsoft Update Catalog nel computer per la prima volta, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero dell'hotfix da scaricare riportato nella Knowledge Base (KB). Immettere **4023268** per l'aggiornamento 0.6 e quindi fare clic su **Cerca**.
   
    Verrà visualizzato l'elenco degli aggiornamenti rapidi, tra cui l'**aggiornamento 0.6 per l'array virtuale StorSimple**.
   
    ![Cercare nel catalogo](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Fare clic su **Download**.

5. Vengono visualizzati cinque file da scaricare. Scaricare ognuno dei file in una cartella. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

6. Aprire la cartella in cui si trovano i file.
    ![File nel pacchetto](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Verranno visualizzati:
    -  Un file di pacchetto autonomo Microsoft Update `WindowsTH-KB3011067-x64`. Questo file viene usato per aggiornare il software del dispositivo.
    - Un file di pacchetto dell'agente di monitoraggio Geneva `GenevaMonitoringAgentPackageInstaller`. Questo file viene usato per aggiornare l'agente del servizio di monitoraggio e diagnostica (MDS). Fare doppio clic sul file con estensione cab. Viene visualizzato un file con estensione _MSI_. Selezionare il file, fare clic con il pulsante destro del mouse e quindi scegliere **Estrai**. Usare il file con estensione _MSI_ per aggiornare l'agente.

        ![Estrarre il file di aggiornamento dell'agente del servizio di monitoraggio e diagnostica (MDS)](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Non è necessario aggiornare l'agente MDS, se si esegue l'aggiornamento 0.5 di StorSimple Update (0.0.10293.0).

    - Tre file che contengono gli aggiornamenti critici della sicurezza di Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64` e `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete.

Usare questo metodo per installare gli aggiornamenti in un dispositivo che esegue GA o versioni del software con l'aggiornamento 0.1. Il completamento di questa procedura richiede circa 12 minuti. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, accedere a **Manutenzione** > **Aggiornamento software**. Prendere nota della versione del software in esecuzione. Se si esegue la versione **10.0.10290.0**, non è necessario aggiornare l'agente MDS nel passaggio 6.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. In **Percorso del file di aggiornamento**, immettere il nome del file dell'aggiornamento o dell'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Fare clic su **Apply**.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Verrà visualizzato un avviso. Visto che l'array virtuale è un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento il dispositivo si riavvia con un conseguente periodo di inattività. Fare clic sull'icona del segno di spunta
   
   ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. L'aggiornamento si avvia. Dopo l'aggiornamento il dispositivo si riavvia in automatico. In questo periodo di tempo l'interfaccia utente locale non è accessibile.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Al termine del riavvio si viene indirizzati alla pagina **di accesso** . Per verificare se il software del dispositivo è stato aggiornato, nell'interfaccia utente Web locale passare a **Manutenzione** > **Aggiornamento software**. Dovrebbe essere visualizzata la versione del software **10.0.0.0.0.10293** per l'aggiornamento 0.6.
   
   > [!NOTE]
   > Le versioni del software vengono riportate in modo leggermente diverso nell'interfaccia utente Web locale e nel portale di Azure. Ad esempio, l'interfaccia utente Web locale indica **10.0.0.0.0.10293**, mentre il portale di Azure indica **10.0.10293.0** per la stessa versione.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Ignorare questo passaggio se si eseguiva l'aggiornamento 0.5 dell'array virtuale StorSimple (**10.0.10290.0**) prima di applicare questo aggiornamento. L'utente ha preso nota della versione del software nel passaggio 1 prima di avviare l'aggiornamento. Se veniva eseguito l'aggiornamento 0.5, l'agente MDS è già aggiornato.

    Se si esegue una versione del software precedente all'aggiornamento 0.5, il passaggio successivo per l'utente consiste nell'aggiornare l'agente MDS. Nella pagina **Software Update** (Aggiornamento software), passare a **Update file path** (Aggiorna percorso file) e cercare il file `GenevaMonitoringAgentPackageInstaller.msi`. Ripetere i passaggi da 2 a 4. Dopo il riavvio dell'array virtuale, accedere all'interfaccia utente Web locale.

7. Ripetere i passaggi da 2 a 4 per installare le correzioni della sicurezza di Windows usando i file `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64` e `windows8.1-kb4019213-x64`. L'array virtuale viene riavviato dopo ogni installazione ed è necessario accedere all'interfaccia utente Web locale.

## <a name="next-steps"></a>Passaggi successivi

Scoprire di più su come [amministrazione StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

