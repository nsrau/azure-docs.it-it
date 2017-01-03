---
title: Gestire i record di controllo di accesso per l&quot;array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: In questo articolo viene descritto come gestire i record di controllo di accesso (ACR) che consentono di specificare quali host possono connettersi a un volume nell&quot;array virtuale StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5c31ac5284f3ba8fecc1e9f33838a778254658e5
ms.openlocfilehash: a0ba81faa20587d9559bf25cdc82dd6e20f42948

---
# <a name="use-the-storsimple-device-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Usare il servizio Gestione dispositivi StorSimple per gestire i record di controllo di accesso per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica

I record di controllo di accesso (ACR) consentono di specificare quali host possono connettersi a un volume nell'array virtuale StorSimple (noto anche come dispositivo virtuale locale StorSimple). I record di controllo di accesso vengono impostati su un volume specifico e contengono i nomi completi iSCSI (IQN) degli host. Quando un host prova a connettersi a un volume, il dispositivo verifica il record di controllo di accesso associato a tale volume per il nome qualificato iSCSI e, se esiste una corrispondenza, viene stabilita la connessione. Nel pannello **Record di controllo di accesso** nella sezione **Configura** del servizio Gestione dispositivi vengono visualizzati tutti i record di controllo di accesso insieme agli IQN degli host.

![Gestire record di controllo di accesso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

In questa esercitazione vengono illustrate le seguenti attività comuni correlate ai record di controllo di accesso:

* Ottenere il nome qualificato iSCSI
* Aggiungere un record di controllo di accesso
* Modificare un record di controllo di accesso
* Eliminare un record di controllo di accesso

> [!IMPORTANT]
> 
> * Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume.
> * Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.


## <a name="get-the-iqn"></a>Ottenere il nome qualificato iSCSI

Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI di un host di Windows che esegue Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Aggiungere un record di controllo di accesso

Usare il pannello **Record di controllo di accesso** nella sezione **Configurazione** del servizio Gestione dispositivi StorSimple per aggiungere record di controllo di accesso. In genere, a un volume viene associato un record di controllo di accesso.

Per informazioni sull'associazione di un record di controllo di accesso con un volume, vedere [Aggiungere un volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume.


Attenersi alla seguente procedura per aggiungere un record di controllo di accesso.

#### <a name="to-add-an-acr"></a>Per aggiungere un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.
2. Nel pannello **Record di controllo di accesso** fare clic su **Aggiungi**.
3. Nel pannello **Aggiungi record di controllo di accesso** eseguire le operazioni seguenti:
   
    1. Fornire un **Nome** per l'ACR.
    
    2. In **Nome iniziatore iSCSI**fornire il nome qualificato iSCSI dell'host di Windows. Per ottenere l'IQN dell'host di Windows Server, eseguire le operazioni seguenti:
   
    3. Avviare l'iniziatore iSCSI di Microsoft sull’host di Windows. Nella scheda **Configurazione** della finestra delle proprietà dell'iniziatore iSCSI selezionare e copiare la stringa dal campo **Nome iniziatore**.
    Incollare questa stringa nel campo **IQN** nel pannello **Aggiungi record di controllo di accesso**.
   
    6. Fare clic su **Aggiungi** per aggiungere il record di controllo di accesso.  
   
        ![Aggiungere record di controllo di accesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. L'elenco tabulare viene aggiornato per riflettere questa aggiunta.

## <a name="edit-an-acr"></a>Modificare un record di controllo di accesso

Usare il pannello **Record di controllo di accesso** nella sezione **Configurazione** del servizio Gestione dispositivi nel portale di Azure per modificare i record di controllo di accesso.

> [!NOTE]
> Non è opportuno modificare un record di controllo di accesso attualmente in uso. Per modificare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.


Seguire questa procedura per modificare un record di controllo di accesso.

#### <a name="to-edit-an-acr"></a>Per modificare un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.
2. Nell'elenco tabulare dei record di controllo di accesso nel pannello **Record di controllo di accesso** fare doppio clic sul record di controllo di accesso che si vuole modificare.
3. Nel pannello **Modifica record di controllo di accesso** eseguire le operazioni seguenti:
   
    1. Fornire il nome qualificato ISCSI per il record di controllo di accesso.
   
    2. Fare clic su **Salva** nella parte superiore del pannello per salvare il record di controllo di accesso modificato. Viene visualizzato il messaggio di conferma seguente:
   
        ![Modificare i record di controllo di accesso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Eliminare un record di controllo di accesso

Per eliminare record di controllo di accesso, usare la pagina **Configurazione** nel portale di Azure.

> [!NOTE]
> 
> * Non è opportuno eliminare un record di controllo di accesso attualmente in uso. Per eliminare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.
> * Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.


Attenersi alla procedura seguente per eliminare un record di controllo di accesso.

#### <a name="to-delete-an-access-control-record"></a>Per eliminare un record di controllo di accesso

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e nella sezione **Configurazione** fare clic su **Record di controllo di accesso**.

2. Nell'elenco tabulare dei record di controllo di accesso nel pannello **Record di controllo di accesso** fare doppio clic sul record di controllo di accesso che si vuole eliminare.

3. Nel pannello Modifica record di controllo di accesso fare clic su **Elimina**.
   
    ![Eliminare record di controllo di accesso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Quando viene richiesta la conferma, fare clic su **Elimina** per continuare con l'eliminazione. L'elenco tabulare viene aggiornato per riflettere l'eliminazione.
   
   ![Messaggio di avviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull' [aggiunta di volumi e la configurazione di record di controllo di accesso](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).




<!--HONumber=Jan17_HO1-->


