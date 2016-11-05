---
title: Modificare la password amministratore del dispositivo virtuale StorSimple | Microsoft Docs
description: Viene descritto come usare il portale di Azure classico o l'interfaccia utente Web StorSimple Virtual Array per modificare la password amministratore del dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/17/2016
ms.author: alkohli

---
# Modificare la password amministratore del dispositivo dell'array virtuale StorSimple
## Panoramica
Quando si usa l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale StorSimple, è necessario immettere la password amministratore del dispositivo. Quando il dispositivo StorSimple viene sottoposto a provisioning e avviato per la prima volta, la password predefinita è *Password1*. Per la sicurezza dei dati, la password predefinita scade al primo accesso e viene richiesto di modificare la password.

È inoltre possibile usare l'interfaccia utente Web locale o il portale di Azure classico per modificare la password amministratore del dispositivo in qualsiasi momento a seguito della distribuzione del dispositivo nell'ambiente di produzione. Tutte queste procedure vengono descritte in questo articolo.

## Usare il portale di Azure classico per modificare la password
Eseguire i passaggi seguenti per modificare la password amministratore del dispositivo tramite il portale di Azure classico.

#### Per modificare la password amministratore del dispositivo tramite il portale di Azure classico
1. Nel portale, fare clic su**Dispositivi** > **Configurazione** per il dispositivo.
2. Scorrere verso il basso fino alla sezione **Password amministratore del dispositivo** Specificare una password amministratore contenente dagli 8 ai 15 caratteri. La password deve contenere una combinazione di lettere maiuscole, minuscole, numeri e caratteri speciali.
3. Confermare la password.
4. Fare clic su **Save** nella parte inferiore della pagina.

A questo punto, la password amministratore del dispositivo dovrebbe essere aggiornata. È possibile usare questa password modificata per accedere al dispositivo in locale.

## Usare l'interfaccia utente Web StorSimple Virtual Array per modificare la password
Eseguire i passaggi seguenti per modificare la password amministratore del dispositivo tramite l'interfaccia utente Web locale.

#### Per modificare la password amministratore del dispositivo tramite l'interfaccia utente Web locale
1. Nell'interfaccia utente Web locale, fare clic su **Manutenzione** > **Modifica password** per il dispositivo.
   
    ![cambiare password1](./media/storsimple-ova-change-device-admin-password/image40.png)
2. Immettere la **Password corrente**.
3. Fornire una **Nuova password**. La password deve essere di almeno 8 caratteri. Deve contenere 3 dei seguenti 4 elementi: lettere maiuscole, minuscole, numeri e caratteri speciali.
   
    Notare che la password non può essere uguale alle ultime 24 password.
4. Immettere nuovamente la password per confermarla.
   
    ![cambiare password2](./media/storsimple-ova-change-device-admin-password/image41.png)
5. Nella parte inferiore della pagina, fare clic su **Applica**. La nuova password viene quindi applicata. Se la modifica della password non viene eseguita correttamente, viene visualizzato l'errore seguente.
   
    ![errore password](./media/storsimple-ova-change-device-admin-password/image42.png)
   
    Dopo aver aggiornato correttamente la password, se ne riceve notifica. È quindi possibile usare la password modificata per accedere al dispositivo in locale.

## Passaggi successivi
Scoprire come [amministrare lo StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->