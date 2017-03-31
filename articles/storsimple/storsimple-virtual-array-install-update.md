---
title: Installare aggiornamenti su un array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: Viene illustrato come usare l&quot;interfaccia utente Web dell&quot;array virtuale StorSimple per applicare aggiornamenti tramite il portale e gli hotfix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 978dde5857344704ce14e2dd090895f1fe541863
ms.openlocfilehash: c2d081604c0ca01f47c3ff2aab7477859d280963
ms.lasthandoff: 03/01/2017

---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Installare aggiornamenti nell'array virtuale StorSimple - Portale di Azure

## <a name="overview"></a>Panoramica

Questo articolo descrive i passaggi necessari per installare aggiornamenti sull'array virtuale StorSimple mediante l'interfaccia utente Web locale e il portale di Azure. È necessario applicare aggiornamenti software o hotfix per mantenere StorSimple Virtual Array sempre aggiornato. 

Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Dato che l'array virtuale StorSimple è un dispositivo a nodo singolo, gli eventuali I/O in corso vengono interrotti e il dispositivo registra un periodo di inattività. 

Prima di applicare un aggiornamento, si consiglia di portare offline i volumi o le condivisioni, prima nell'host e poi nel dispositivo. Questa operazione consente di eliminare qualsiasi rischio di danneggiamento dei dati.

> [!IMPORTANT]
> Se si esegue l'aggiornamento 0.1 o una versione del software GA, è necessario usare il metodo hotfix tramite l'interfaccia utente Web locale per installare l'aggiornamento 0.3. Se si esegue l'aggiornamento 0.2, si consiglia di installare gli aggiornamenti tramite il portale di Azure classico.
 

## <a name="use-the-local-web-ui"></a>Usare l'interfaccia utente Web locale

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

* Scaricare l'aggiornamento o l'hotfix
* Installare l'aggiornamento o l'hotfix

### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Per scaricare l'aggiornamento o l'hotfix

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa Microsoft Update Catalog nel computer per la prima volta, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero dell'hotfix da scaricare riportato nella Knowledge Base (KB). Digitare **3182061** per l'aggiornamento 0.3, quindi fare clic su **Cerca**.
   
    Viene visualizzato l'elenco degli hotfix, tra cui l' **aggiornamento 0.3 per l'array virtuale StorSimple**.
   
    ![Cercare nel catalogo](./media/storsimple-virtual-array-install-update/download1.png)

4. Fare clic su **Aggiungi**. L'aggiornamento viene aggiunto al carrello.

5. Fare clic su **Visualizza carrello**.

6. Fare clic su **Download**. Specificare o **selezionare** il percorso locale in cui salvare i file scaricati. Gli aggiornamenti vengono scaricati nel percorso specificato e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

7. Aprire la cartella copiata e si dovrebbe vedere un file di pacchetto autonomo Microsoft Update `WindowsTH-KB3011067-x64`. Si usa questo file per installare l'hotfix o aggiornamento.

### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete. 

Usare questo metodo per installare gli aggiornamenti in un dispositivo che esegue GA o versioni del software con l'aggiornamento 0.1. Questa procedura di aggiornamento richiede un massimo di 2 minuti per il completamento. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Per installare l'aggiornamento o l'hotfix

1. Nell'interfaccia utente Web locale, accedere a **Manutenzione** > **Aggiornamento software**.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update/update1m.png)

2. In **Percorso del file di aggiornamento**, immettere il nome del file dell'aggiornamento o dell'hotfix. È possibile anche cercare il file di installazione dell'aggiornamento o dell'hotfix, se posizionato in una condivisione di rete. Fare clic su **Apply**.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update/update2m.png)

3. Verrà visualizzato un avviso. Dato che si tratta di un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento il dispositivo si riavvia con un conseguente periodo di inattività. Fare clic sull'icona del segno di spunta
   
   ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update/update3m.png)

4. L'aggiornamento si avvia. Dopo l'aggiornamento il dispositivo si riavvia in automatico. In questo periodo di tempo l'interfaccia utente locale non è accessibile.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update/update5m.png)

5. Al termine del riavvio si viene indirizzati alla pagina **di accesso** . Per verificare se il software del dispositivo è stato aggiornato, nell'interfaccia utente Web locale passare a **Manutenzione** > **Aggiornamento software**. Dovrebbe essere visualizzata la versione del software **10.0.0.0.0.10288.0** per l'aggiornamento 0.3.
   
   > [!NOTE]
   > Le versioni del software vengono riportate in modo leggermente diverso nell'interfaccia utente Web locale e nel portale di Azure. Ad esempio, l'interfaccia utente Web locale riporta **10.0.0.0.0.10288**, mentre il portale di Azure riporta **10.0.10288.0** per la stessa versione.
   
    ![aggiornamento dispositivo](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Se si esegue l'aggiornamento 0.2, si consiglia di installare gli aggiornamenti tramite il portale di Azure. La procedura del portale richiede all'utente di analizzare, scaricare e installare gli aggiornamenti. Questa procedura di aggiornamento richiede circa 7 minuti per il completamento. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Al termine dell'installazione, quando lo stato del processo è 100%, passare al servizio Gestione dispositivi StorSimple. Selezionare **Dispositivi** e quindi selezionare e fare clic sul dispositivo che si vuole aggiornare nell'elenco di dispositivi connessi al servizio. Nel pannello **Impostazioni** andare alla sezione **Gestisci** e selezionare **Aggiornamenti del dispositivo**. La versione del software visualizzata dovrebbe essere **10.0.10288.0**.


## <a name="next-steps"></a>Passaggi successivi

Scoprire di più su come [amministrazione StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).


