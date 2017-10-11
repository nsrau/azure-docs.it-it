---
title: Creare il ticket di supporto o il case per StorSimple serie 8000 | Microsoft Docs
description: Informazioni su come registrare una richiesta di supporto e avviare una sessione di supporto nel dispositivo StorSimple serie 8000.
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
ms.date: 07/25/2017
ms.author: alkohli;
ms.openlocfilehash: 4b5a14237ce79100f980b2186b2c3c887abaa296
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="contact-microsoft-support"></a>Contattare il supporto tecnico Microsoft

Gestione dispositivi StorSimple offre la possibilità di **registrare una nuova richiesta di supporto** all'interno del pannello di riepilogo servizio. Se si verificano problemi con la soluzione StorSimple, è possibile creare una richiesta di servizio per il supporto tecnico. In una sessione online con il supporto tecnico, è necessario anche avviare una sessione di supporto nel dispositivo StorSimple. In questo articolo viene descritto:

* Come creare una richiesta di supporto
* Come gestire una richiesta di supporto relativa al ciclo di vita all'interno del portale.
* Come avviare una sessione di supporto nell'interfaccia Windows PowerShell del dispositivo StorSimple.

Esaminare [Informazioni e contratti di servizio di supporto tecnico della serie 8000 di StorSimple](https://msdn.microsoft.com/library/mt433077.aspx) prima di creare una richiesta di supporto.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

In base al [piano di supporto](https://azure.microsoft.com/support/plans/) è possibile creare ticket di supporto per un problema sul dispositivo StorSimple direttamente dal pannello di riepilogo del servizio Gestione dispositivi StorSimple. Per creare una richiesta di supporto, attenersi alla procedura seguente.

#### <a name="to-create-a-support-request"></a>Per creare una richiesta di supporto

1. Passare al servizio Gestione dispositivi StorSimple. Nelle impostazioni del pannello di riepilogo servizio passare alla sezione **SUPPORTO E RISOLUZIONE DEI PROBLEMI** e quindi fare clic su **Nuova richiesta di supporto**.
     
    ![Contattare il supporto tecnico Microsoft tramite il nuovo portale](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Nel pannello **Nuova richiesta di supporto** selezionare **Informazioni di base**. Nel pannello **Informazioni di base** attenersi alla procedura seguente:
   1. Dall'elenco a discesa **Tipo di problema** selezionare **Tecnico**.
   2. Vengono scelti automaticamente i valori correnti di **sottoscrizione**, tipo di **servizio** e **risorsa** (servizio Gestione dispositivi StorSimple). 
   3. Scegliere un **piano di supporto** dall'elenco a discesa se alla sottoscrizione sono associati più piani. È necessario un piano di supporto a pagamento per poter abilitare il supporto tecnico.
   4. Fare clic su **Avanti**.

       ![Contattare il supporto tecnico Microsoft tramite il nuovo portale](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Nel pannello **Nuova richiesta di supporto** selezionare **Passaggio 2 - Problema**. Nel pannello **Problema** attenersi alla procedura seguente:
    
    1. Scegliere il livello di **Gravità**.
    2. Specificare se il problema è correlato al dispositivo o al servizio Gestione dispositivi StorSimple.
    3. Scegliere una **Categoria** per il problema e fornire maggiori **Dettagli**.
    4. Specificare la data e l'ora di inizio del problema.
    5. In **Caricamento file**fare clic sull'icona della cartella per selezionare il pacchetto di supporto.
    6. Selezionare **Condividi informazioni diagnostica**.
    7. Fare clic su **Avanti**.

       ![Contattare il supporto tecnico Microsoft tramite il nuovo portale](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Nel pannello **Nuova richiesta di assistenza** fare clic su **Passaggio 3 - Informazioni di contatto**. Nel pannello **Informazioni di contatto**, attenersi alla procedura seguente:

    1. In **Opzioni contatti**, fornire il metodo di contatto preferito (telefono o posta elettronica) e la lingua. Il tempo di risposta viene selezionato automaticamente in base al piano di sottoscrizione.
    2. In Informazioni di contatto specificare nome, posta elettronica, contatto facoltativo, paese. Selezionare la casella di controllo **Salva modifiche di contatto per le richieste di supporto future** .
    3. Fare clic su **Crea**.
   
        ![Contattare il supporto tecnico Microsoft tramite il nuovo portale](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Il supporto Microsoft userà queste informazioni per contattare l'utente per altre informazioni, la diagnostica e la risoluzione.
Dopo avere inviato la richiesta, si verrà presto contattati da un tecnico del supporto per procedere con la richiesta.

## <a name="manage-a-support-request"></a>Gestire una richiesta di supporto

Dopo la creazione del ticket di supporto, sarà possibile gestire il ciclo di vita del ticket dal portale.

#### <a name="to-manage-your-support-requests"></a>Per gestire le richieste di supporto

1. Per visualizzare la pagina Guida e supporto, passare a **Sfoglia -> Guida e supporto**.

    ![Gestire le richieste di supporto](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Nel pannello **Guida e supporto** viene visualizzato un elenco tabulare con tutte le richieste di supporto.

    ![Gestire le richieste di supporto](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selezionare e fare clic su una richiesta di supporto. È possibile visualizzare lo stato e i dettagli della richiesta. Fare clic su **+ Nuovo messaggio** se si vuole seguire la richiesta.

    ![Gestire le richieste di supporto](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Avviare una sessione di supporto in Windows PowerShell per StorSimple

Per risolvere eventuali problemi che potrebbero verificarsi con il dispositivo StorSimple, sarà necessario contattare il team del supporto tecnico Microsoft. Il supporto tecnico Microsoft potrebbe utilizzare una sessione di supporto per l'accesso al dispositivo.

Per avviare una sessione di supporto, attenersi alla procedura seguente.

#### <a name="to-start-a-support-session"></a>Per avviare una sessione di supporto

1. Accedere al dispositivo direttamente tramite la console seriale o tramite una sessione telnet da un computer remoto. A tale scopo, attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Nella sessione che viene aperta, premere **Invio** per visualizzare un prompt dei comandi.
3. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.
4. Al prompt dei comandi, digitare la seguente password:
   
    `Password1`
5. Al prompt dei comandi, digitare il seguente comando:
   
    `Enable-HcsSupportAccess`
6. Verrà visualizzata una stringa crittografata. Copiare la stringa in un editor di testo, ad esempio Blocco note.
7. Salvare la stringa e inviarla in un messaggio di posta elettronica al supporto tecnico Microsoft.

> [!IMPORTANT]
> È possibile disabilitare l'accesso del supporto eseguendo `Disable-HcsSupportAccess`. Il dispositivo StorSimple inoltre tenterà di disabilitare l'accesso del supporto dopo 8 ore dall'avvio della sessione. È consigliabile modificare le credenziali del dispositivo StorSimple dopo l'avvio di una sessione di supporto.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [diagnosticare e risolvere i problemi relativi al dispositivo virtuale StorSimple serie 8000](storsimple-troubleshoot-deployment.md)
