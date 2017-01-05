---
title: Modificare la password amministratore del dispositivo dell&quot;array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: In questo articolo viene descritto come usare il portale di Azure o l&quot;interfaccia utente Web dell&quot;array virtuale StorSimple per modificare la password amministratore del dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: fd73672f97b4c16e49b2fad5e53042764f5793ca
ms.openlocfilehash: 4645ec88f804908916f7cf9b090376753c089119

---
# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Modificare la password amministratore del dispositivo dell'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Quando si usa l'interfaccia di Windows PowerShell per accedere all'array virtuale StorSimple, è necessario immettere la password amministratore del dispositivo. Quando il dispositivo StorSimple viene sottoposto a provisioning e avviato per la prima volta, la password predefinita è *Password1*. Per la sicurezza dei dati, la password predefinita scade al primo accesso e viene richiesto di modificare la password.

È anche possibile usare l'interfaccia utente Web locale o il portale di Azure per modificare la password amministratore del dispositivo in qualsiasi momento a seguito della distribuzione del dispositivo nell'ambiente di produzione. Tutte queste procedure vengono descritte in questo articolo.

 ![pannello Dispositivi](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Usare il portale di Azure per modificare la password

Eseguire la procedura seguente per modificare la password amministratore del dispositivo tramite il portale di Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Per modificare la password amministratore del dispositivo tramite il portale di Azure

1. Nella pagina di destinazione del servizio selezionare il servizio, fare doppio clic sul nome del servizio e nella sezione **Gestione** fare clic su **Dispositivi**. Verrà visualizzato il pannello **Dispositivi** in cui vengono elencati tutti i dispositivi dell'array virtuale StorSimple.

2. Nel pannello **Dispositivi** fare doppio clic sul dispositivo che richiede una modifica della password.

3. Nel pannello **Impostazioni** del dispositivo fare clic su **Sicurezza**.

4. Nel pannello **Impostazioni di sicurezza** eseguire le operazioni seguenti:
   
   1. Scorrere verso il basso fino alla sezione **Password amministratore del dispositivo**. Specificare una password amministratore contenente dagli 8 ai 15 caratteri.
   2. Confermare la password.
   3. Fare clic su **Salva** nella parte superiore del pannello.

La password amministratore del dispositivo è ora aggiornata. È possibile usare questa password modificata per accedere al dispositivo in locale.

![Pannello impostazioni di sicurezza](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Usare l'interfaccia utente Web locale per modificare la password

Eseguire i passaggi seguenti per modificare la password amministratore del dispositivo tramite l'interfaccia utente Web locale.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Per modificare la password amministratore del dispositivo tramite l'interfaccia utente Web locale

1. Nell'interfaccia utente Web locale fare clic su **Manutenzione** > **Modifica password** per il dispositivo.
   
    ![cambiare password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Immettere la **Password corrente**.
3. Fornire una **Nuova password**. La password deve essere di almeno 8 caratteri. Deve contenere 3 dei seguenti 4 elementi: lettere maiuscole, minuscole, numeri e caratteri speciali.
   
    Notare che la password non può essere uguale alle ultime 24 password.
4. Immettere nuovamente la password per confermarla.
   
    ![cambiare password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Nella parte inferiore della pagina, fare clic su **Applica**. La nuova password è ora applicata. Se la modifica della password non è eseguita correttamente, viene visualizzato l'errore seguente:
   
    ![errore password](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Dopo aver aggiornato correttamente la password, se ne riceve notifica. È quindi possibile usare la password modificata per accedere al dispositivo in locale.


## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO4-->


