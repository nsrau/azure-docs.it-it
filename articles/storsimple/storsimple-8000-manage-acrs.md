---
title: Gestire i record di controllo di accesso in StorSimple | Microsoft Docs
description: In questo articolo vengono descritti i record di controllo di accesso (ACR) che consentono di specificare quali host possono connettersi a un volume nel dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Utilizzare il servizio StorSimple Manager per gestire li record di controllo di accesso

## <a name="overview"></a>Panoramica
I record di controllo di accesso (ACR) consentono di specificare quali host possono connettersi a un volume nel dispositivo StorSimple. I record di controllo di accesso vengono impostati su un volume specifico e contengono i nomi completi iSCSI (IQN) degli host. Quando un host prova a connettersi a un volume, il dispositivo controlla il record di controllo di accesso associato a tale volume per l'IQN e, se esiste una corrispondenza, viene stabilita la connessione. I record di controllo di accesso nella sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple visualizzano tutti i record di controllo di accesso con gli IQN corrispondenti degli host.

In questa esercitazione vengono illustrate le seguenti attività comuni correlate ai record di controllo di accesso:

* Aggiungere un record di controllo di accesso
* Modificare un record di controllo di accesso
* Eliminare un record di controllo di accesso

> [!IMPORTANT]
> * Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume.
> * Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.

## <a name="get-the-iqn"></a>Ottenere il nome qualificato iSCSI

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI di un host di Windows che esegue Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Aggiungere un record di controllo di accesso
Per aggiungere record di controllo di accesso, usare la sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple. In genere, un record di controllo di accesso verrà associato a un volume.

Attenersi alla seguente procedura per aggiungere un record di controllo di accesso.

#### <a name="to-add-an-acr"></a>Per aggiungere un record di controllo di accesso

1. Nel servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.
2. Nel pannello **Record di controllo di accesso** fare clic su **+ Aggiungi record di controllo di accesso**.

    ![Clic su Aggiungi record di controllo di accesso](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Nel pannello **Aggiungi record di controllo di accesso** attenersi alla procedura seguente:

    1. Fornire un nome per il record di controllo di accesso.
    
    2. In **Nome iniziatore iSCSI**, fornire l'IQN dell'host di Windows Server.

    3. Fare clic su **Aggiungi** per creare il record di controllo di accesso.

        ![Clic su Aggiungi record di controllo di accesso](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Il record di controllo di accesso appena aggiunto verrà visualizzato nel relativo elenco tabulare.

    ![Clic su Aggiungi record di controllo di accesso](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Modificare un record di controllo di accesso
Per modificare i record di controllo di accesso, usare la sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple.

> [!NOTE]
> È consigliabile modificare solo i record di controllo di accesso che non sono attualmente in uso. Per modificare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.

Seguire questa procedura per modificare un record di controllo di accesso.

#### <a name="to-edit-an-access-control-record"></a>Per modificare un record di controllo di accesso
1.  Nel servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Nell'elenco tabulare dei record di controllo di accesso, fare clic e selezionare il record di controllo di accesso da modificare.

    ![Modificare i record di controllo di accesso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Nel pannello **Modifica record di controllo di accesso**, fornire un nome IQN diverso, corrispondente a un altro host.

    ![Modificare i record di controllo di accesso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Fare clic su **Salva**. Alla richiesta di conferma fare clic su **Sì**. 

    ![Modificare i record di controllo di accesso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Quando un record di controllo di accesso viene aggiornato l'utente riceve una notifica. Anche l'elenco tabulare viene aggiornato per riflettere le modifiche.

   
## <a name="delete-an-access-control-record"></a>Eliminare un record di controllo di accesso
Per eliminare i record di controllo di accesso, usare la sezione **Configurazione** del pannello del servizio Gestione dispositivi StorSimple.

> [!NOTE]
> È possibile eliminare solo i record di controllo di acceso che non sono attualmente in uso. Per eliminare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.

Attenersi alla procedura seguente per eliminare un record di controllo di accesso.

#### <a name="to-delete-an-access-control-record"></a>Per eliminare un record di controllo di accesso
1.  Nel servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Nell'elenco tabulare dei record di controllo di accesso, fare clic e selezionare il record di controllo di accesso da eliminare.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida e quindi selezionare **Elimina**.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando viene richiesta la conferma, esaminare le informazioni e quindi fare clic su **Elimina**.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Quando un record di controllo di accesso viene eliminato l'utente riceve una notifica. L'elenco tabulare viene aggiornato per riflettere l'eliminazione.

    ![Passare ai record di controllo di accesso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla [gestione di volumi StorSimple](storsimple-8000-manage-volumes-u2.md).
* Ulteriori informazioni sull’ [utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

