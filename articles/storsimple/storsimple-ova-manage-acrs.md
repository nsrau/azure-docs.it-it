---
title: Gestire i record di controllo di accesso per l&quot;array virtuale StorSimple | Microsoft Docs
description: In questo articolo viene descritto come gestire i record di controllo di accesso (ACR) che consentono di specificare quali host possono connettersi a un volume nell&quot;array virtuale StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 11252938-5b97-4178-8c37-f58eaa3d00b1
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f45d8e2b848e930a790e7bf38c2b09fbdaaf2fab
ms.openlocfilehash: 30b6aedd9b4b7913f6862b34582f3ae96c9eae5f
ms.lasthandoff: 03/01/2017


---
# <a name="use-storsimple-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Usare StorSimple Manager per gestire i record di controllo di accesso per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica
I record di controllo di accesso (ACR) consentono di specificare quali host possono connettersi a un volume nell'array virtuale StorSimple (noto anche come dispositivo virtuale locale StorSimple). I record di controllo di accesso vengono impostati su un volume specifico e contengono i nomi completi iSCSI (IQN) degli host. Quando un host prova a connettersi a un volume, il dispositivo verifica il record di controllo di accesso associato a tale volume per il nome qualificato iSCSI e, se esiste una corrispondenza, viene stabilita la connessione. Nella sezione dei **record di controllo di accesso** nella pagina **Configura** vengono visualizzati tutti i record di controllo di accesso insieme agli IQN degli host.

In questa esercitazione vengono illustrate le seguenti attività comuni correlate ai record di controllo di accesso:

* Ottenere il nome qualificato iSCSI
* Aggiungere un record di controllo di accesso 
* Modificare un record di controllo di accesso 
* Eliminare un record di controllo di accesso 

> [!IMPORTANT]
> * Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume. 
> * Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.
> 
> 

## <a name="get-the-iqn"></a>Ottenere il nome qualificato iSCSI
Eseguire i passaggi seguenti per ottenere il nome qualificato iSCSI di un host di Windows che esegue Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Aggiungere un record di controllo di accesso
Si utilizza la pagina **Configurazione** del servizio StorSimple Manager per aggiungere record di controllo di accesso. In genere, un record di controllo di accesso verrà associato a un volume.

Per informazioni sull'associazione di un record di controllo di accesso con un volume, vedere [Aggiungere un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Quando si assegna un record di controllo di accesso a un volume, fare attenzione che nel volume non abbiano effettuato l'accesso più di un host non cluster perché ciò potrebbe danneggiare il volume.
> 
> 

Attenersi alla seguente procedura per aggiungere un record di controllo di accesso.

#### <a name="to-add-an-acr"></a>Per aggiungere un record di controllo di accesso
1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione** .
   
    ![scheda Configurazione](./media/storsimple-ova-manage-acrs/acr1.png)
2. Nell'elenco tabulare in **Record di controllo di accesso** fornire un **nome** per il record di controllo di accesso.
3. In **Nome iniziatore iSCSI**fornire il nome qualificato iSCSI dell'host di Windows. 
4. Fare clic su **Salva** nella parte inferiore della pagina per salvare il record di controllo di accesso appena creato. Viene visualizzato il messaggio di conferma seguente:
   
    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr2.png)
5. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere questa aggiunta.

## <a name="edit-an-acr"></a>Modificare un record di controllo di accesso
Per modificare record di controllo di accesso, usare la pagina **Configurazione** nel portale di Azure classico. 

> [!NOTE]
> È possibile modificare solo i record di controllo di accesso che non sono attualmente in uso. Per modificare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.
> 
> 

Seguire questa procedura per modificare un record di controllo di accesso.

#### <a name="to-edit-an-acr"></a>Per modificare un record di controllo di accesso
1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione** .
2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera modificare.
3. Fornire un nuovo nome e/o l'IQN del record di controllo di accesso.
4. Fare clic su **Salva** nella parte inferiore della pagina per salvare il record di controllo di accesso modificato. Verrà visualizzato un messaggio di conferma. 
5. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere questa modifica.

## <a name="delete-an-access-control-record"></a>Eliminare un record di controllo di accesso
Per eliminare record di controllo di accesso, usare la pagina **Configurazione** nel portale di Azure classico. 

> [!NOTE]
> * È possibile eliminare solo i record di controllo di accesso che non sono attualmente in uso. Per eliminare un record di controllo di accesso associato a un volume attualmente in uso, è innanzitutto necessario rendere il volume offline.
> * Quando si elimina un record di controllo di accesso da un volume, assicurarsi che l'host corrispondente non acceda al volume perché l'eliminazione potrebbe comportare un'interruzione di lettura/scrittura.
> 
> 

Attenersi alla procedura seguente per eliminare un record di controllo di accesso.

#### <a name="to-delete-an-access-control-record"></a>Per eliminare un record di controllo di accesso
1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e quindi fare clic sulla scheda **Configurazione** .
2. Nell'elenco tabulare dei record di controllo di accesso, passare il mouse sul record di controllo di accesso che si desidera eliminare.
3. Nella colonna all'estrema destra del record di controllo di accesso selezionato, verrà visualizzata un'icona di eliminazione (**x**). Fare clic sull'icona **x** per eliminare il record di controllo di accesso. Viene visualizzato il messaggio di conferma seguente:
   
    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr3.png)
4. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere l'eliminazione.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull' [aggiunta di volumi e la configurazione di record di controllo di accesso](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).


