<properties 
   pageTitle="Contattare il supporto tecnico Microsoft| Microsoft Azure"
   description="Informazioni su come creare una richiesta di supporto e avviare una sessione di supporto nel dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Contattare il supporto tecnico Microsoft

Se si verificano problemi con la soluzione Microsoft Azure StorSimple, è possibile creare una richiesta di servizio per il supporto tecnico. In una sessione online con il supporto tecnico, è necessario anche avviare una sessione di supporto nel dispositivo StorSimple. In questo articolo viene illustrato il processo di creazione di una richiesta di supporto e l'avvio di una sessione di supporto nell'interfaccia di Windows PowerShell del dispositivo StorSimple.

## Creare una richiesta di supporto

Per creare una richiesta di supporto, attenersi alla procedura seguente.

#### Per creare una richiesta di supporto

1. Una richiesta di supporto può essere creata tramite il [portale di gestione](http://manage.windowsazure.com/). Nel [portale di gestione](http://manage.windowsazure.com/) fare clic sul **Nome account** e quindi fare clic su **Contatta il supporto Microsoft**.

	![Contattare il supporto tecnico Microsoft tramite il portale di gestione](./media/storsimple-contact-microsoft-support/IC777286.png)

1. Nella finestra di dialogo **Contatta il supporto Microsoft**:


	- Nell'elenco a discesa, selezionare la **sottoscrizione** di destinazione associata al servizio StorSimple Manager. Specificare il **Tipo di supporto** come **Tecnico**. È necessario un piano di supporto a pagamento per poter abilitare il supporto tecnico.

	1. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-contact-microsoft-support/IC740895.png) per **Crea ticket**.

1. Nella finestra **Supporto Microsoft**, scegliere **StorSimple** nell'elenco a discesa **Prodotto**.

	![Contattare il supporto tecnico Microsoft - Prodotto](./media/storsimple-contact-microsoft-support/IC777288.png)

1. Seguire le istruzioni sullo schermo per classificare la richiesta in modo corretto e fornire una descrizione chiara e specifica del problema.

Dopo avere inviato la richiesta, si verrà presto contattati da un tecnico del supporto per procedere con la richiesta.

## Avviare una sessione di supporto in Windows PowerShell per StorSimple

Per risolvere eventuali problemi che potrebbero verificarsi con il dispositivo StorSimple, sarà necessario contattare il team del supporto tecnico Microsoft. Il supporto tecnico Microsoft potrebbe utilizzare una sessione di supporto per l'accesso al dispositivo.

Per avviare una sessione di supporto, attenersi alla procedura seguente.

#### Per avviare una sessione di supporto

1. Accedere al dispositivo direttamente tramite la console seriale o tramite una sessione telnet da un computer remoto. A tale scopo, attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

1. Nella sessione che viene aperta, premere **INVIO** per visualizzare un prompt dei comandi.

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**.

1. Al prompt dei comandi, digitare la seguente password:

	`Password1`

1. Al prompt dei comandi, digitare il seguente comando:

	`Enable-HcsSupportAccess`

1. Verrà visualizzata una stringa crittografata. Copiare la stringa in un editor di testo, ad esempio Blocco note.

1. Salvare la stringa e inviarla in un messaggio di posta elettronica al supporto tecnico Microsoft.

> [AZURE.IMPORTANT]È possibile disabilitare l'accesso del supporto eseguendo `Disable-HcsSupportAccess`. Il dispositivo StorSimple inoltre tenterà di disabilitare l'accesso del supporto dopo 8 ore dall'avvio della sessione. È consigliabile modificare le credenziali del dispositivo StorSimple dopo l'avvio di una sessione di supporto.

<!---HONumber=August15_HO6-->