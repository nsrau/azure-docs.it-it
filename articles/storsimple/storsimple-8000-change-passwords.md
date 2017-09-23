---
title: Modificare le password di StorSimple | Microsoft Docs
description: Descrive come usare il servizio Gestione dispositivi StorSimple per modificare le password di StorSimple Snapshot Manager e di amministratore del dispositivo.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Usare il servizio Gestione dispositivi StorSimple per modificare le password di StorSimple

## <a name="overview"></a>Panoramica
L'opzione **Impostazioni del dispositivo** del portale di Azure contiene tutti i parametri del dispositivo che è possibile riconfigurare in un dispositivo StorSimple gestito da un servizio Gestione dispositivi StorSimple. In questa esercitazione viene illustrato come usare l'opzione **Sicurezza** in **Impostazioni dispositivo** per modificare la password dell'amministratore del dispositivo o di StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Configurare la password dell’amministratore del dispositivo
Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo StorSimple, è necessario immettere una password amministratore del dispositivo. Quando il primo dispositivo StorSimple è registrato con un servizio, la password predefinita per questa interfaccia è *Password1*. Per la protezione dei dati, è necessario modificare la password al termine del processo di registrazione. Non è possibile uscire dal processo di registrazione senza modificare la password. Per altre informazioni, vedere [Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La password impostata la prima volta durante la registrazione attraverso l'interfaccia di Windows PowerShell può essere modificata successivamente tramite il portale di Azure. Eseguire i passaggi seguenti per modificare la password di amministratore del dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Per configurare la password dell’amministratore del dispositivo
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**.

2. Nell'elenco tabulare dei dispositivi, selezionare e fare clic sul dispositivo di cui si vuole modificare la password.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Nel pannello **Impostazioni**, passare a **Impostazioni dispositivo > Sicurezza**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Nel pannello **Impostazioni di sicurezza**, fare clic su **Password** per modificare la password di amministratore del dispositivo.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Nel pannello **Password**, specificare una password di amministratore contenente dagli 8 ai 15 caratteri. La password deve contenere una combinazione di 3 o più lettere maiuscole, minuscole, numeri e caratteri speciali.

6. Confermare la password.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Alla richiesta di conferma fare clic su **Salva** e quindi su **Sì**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A questo punto, la password amministratore del dispositivo dovrebbe essere aggiornata. È possibile utilizzare la password modificata per accedere all'interfaccia di Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Configurare la password di StorSimple Snapshot Manager
Il software di Gestione snapshot StorSimple si trova nell'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di snapshot locali e cloud.

Quando si configura un dispositivo in StorSimple Snapshot Manager, verrà richiesto di specificare l'indirizzo IP del dispositivo e la password per autenticare il dispositivo di archiviazione.

È possibile impostare o modificare la password di StorSimple Snapshot Manager nel portale di Azure. Attenersi alla procedura seguente per modificare la password di StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Per configurare la password di StorSimple Snapshot Manager
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**.

2. Nell'elenco tabulare dei dispositivi, selezionare e fare clic sul dispositivo di cui si vuole modificare la password di StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Nel pannello **Impostazioni**, passare a **Impostazioni dispositivo > Sicurezza**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Nel pannello **le Impostazioni di protezione**, fare clic su **Password** per impostare o modificare la password di StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Nel pannello **Password** immettere una password composta da 14 o 15 caratteri. Assicurarsi che la password contenga una combinazione di 3 o più lettere maiuscole, minuscole, numeri e caratteri speciali.

6. Confermare la password.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Alla richiesta di conferma fare clic su **Salva** e quindi su **Sì**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

La password di StorSimple Snapshot Manager ora deve essere aggiornata.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla [sicurezza di StorSimple](storsimple-8000-security.md).
* [Ulteriori informazioni su come modificare la configurazione del dispositivo](storsimple-8000-modify-device-config.md).
* Altre informazioni sull'[utilizzo del servizio Gestione dispositivi StorSimple per la gestione del dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


