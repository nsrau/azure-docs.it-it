<properties 
   pageTitle="Contattare il supporto tecnico Microsoft| Microsoft Azure"
   description="Informazioni su come creare una richiesta di supporto e avviare una sessione di supporto nel dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/06/2015"
   ms.author="alkohli" />

# Contattare il supporto tecnico Microsoft

Se si verificano problemi con la soluzione Microsoft Azure StorSimple, è possibile creare una richiesta di servizio per il supporto tecnico. In una sessione online con il supporto tecnico, è necessario anche avviare una sessione di supporto nel dispositivo StorSimple. In questo articolo viene descritto:

- Come creare una richiesta di supporto
- Come avviare una sessione di supporto nell'interfaccia Windows PowerShell del dispositivo StorSimple.

Esaminare [Informazioni e contratti di servizio di supporto tecnico della serie 8000 di StorSimple](https://msdn.microsoft.com/library/mt433077.aspx) prima di creare una richiesta di supporto.

## Creare una richiesta di supporto

Per creare una richiesta di supporto, attenersi alla procedura seguente.

#### Per creare una richiesta di supporto

1. Una richiesta di supporto può essere creata tramite il [portale di gestione](http://manage.windowsazure.com/). Nel [portale di gestione](http://manage.windowsazure.com/) fare clic sul **Nome account** e quindi fare clic su **Contatta il supporto Microsoft**.

	![Contattare il supporto tecnico Microsoft tramite il portale di gestione](./media/storsimple-contact-microsoft-support/IC777286.png)

2. Nella finestra di dialogo **Contatta il supporto Microsoft**:

	1. Nell'elenco a discesa, selezionare la **sottoscrizione** di destinazione associata al servizio StorSimple Manager. Specificare il **Tipo di supporto** come **Tecnico**. È necessario un piano di supporto a pagamento per poter abilitare il supporto tecnico.

	2. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-contact-microsoft-support/IC740895.png) per **Crea ticket**.

3. Nella finestra **Supporto Microsoft**, dall’elenco a discesa **Prodotto** scegliere **StorSimple**.

	![Contattare il supporto tecnico Microsoft - Prodotto](./media/storsimple-contact-microsoft-support/IC777288.png)

4. Seguire le istruzioni sullo schermo per classificare la richiesta in modo corretto e fornire una descrizione chiara e specifica del problema.

Dopo avere inviato la richiesta, si verrà presto contattati da un tecnico del supporto per procedere con la richiesta.

## Avviare una sessione di supporto in Windows PowerShell per StorSimple

Per risolvere eventuali problemi che potrebbero verificarsi con il dispositivo StorSimple, sarà necessario contattare il team del supporto tecnico Microsoft. Il supporto tecnico Microsoft potrebbe utilizzare una sessione di supporto per l'accesso al dispositivo.

Per avviare una sessione di supporto, attenersi alla procedura seguente.

#### Per avviare una sessione di supporto

1. Accedere al dispositivo direttamente tramite la console seriale o tramite una sessione telnet da un computer remoto. A tale scopo, attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Nella sessione che viene aperta, premere **Invio** per visualizzare un prompt dei comandi.

3. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.

4. Al prompt dei comandi, digitare la seguente password:

	`Password1`

5. Al prompt dei comandi, digitare il seguente comando:

	`Enable-HcsSupportAccess`

6. Verrà visualizzata una stringa crittografata. Copiare la stringa in un editor di testo, ad esempio Blocco note.

7. Salvare la stringa e inviarla in un messaggio di posta elettronica al supporto tecnico Microsoft.

> [AZURE.IMPORTANT]È possibile disabilitare l'accesso del supporto eseguendo `Disable-HcsSupportAccess`. Il dispositivo StorSimple inoltre tenterà di disabilitare l'accesso del supporto dopo 8 ore dall'avvio della sessione. È consigliabile modificare le credenziali del dispositivo StorSimple dopo l'avvio di una sessione di supporto.

<!---HONumber=Oct15_HO3-->