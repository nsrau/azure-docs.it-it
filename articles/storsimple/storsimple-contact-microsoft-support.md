<properties 
   pageTitle="Contattare il supporto tecnico Microsoft| Microsoft Azure"
   description="Informazioni su come creare una richiesta di supporto e avviare una sessione di supporto nel dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="alkohli" />

# Contattare il supporto tecnico Microsoft

Se si verificano problemi con la soluzione Microsoft Azure StorSimple, è possibile creare una richiesta di servizio per il supporto tecnico. In una sessione online con il supporto tecnico, è necessario anche avviare una sessione di supporto nel dispositivo StorSimple. In questo articolo viene descritto:

- Come creare una richiesta di supporto
- Come avviare una sessione di supporto nell'interfaccia Windows PowerShell del dispositivo StorSimple.

Esaminare [Informazioni e contratti di servizio di supporto tecnico della serie 8000 di StorSimple](https://msdn.microsoft.com/library/mt433077.aspx) prima di creare una richiesta di supporto.

## Creare una richiesta di supporto

Per creare una richiesta di supporto, attenersi alla procedura seguente.

#### Per creare una richiesta di supporto

1. Nell'angolo superiore destro del [portale classico di Azure](https://manage.windowsazure.com/) fare clic sul nome account e quindi su **Contatta il supporto Microsoft**.

	![Contattare il supporto tecnico Microsoft tramite il portale di gestione](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Si verrà reindirizzati al nuovo portale di Azure, portal.azure.com. Fare clic sul riquadro **Nuova richiesta di supporto**.

	![Contattare il supporto tecnico Microsoft tramite il nuovo portale](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    Sul lato destro della schermata verrà visualizzato il riquadro **Nuova richiesta di supporto**.

	![Nuovo riquadro di richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Nella finestra di dialogo **Generale** eseguire le operazioni seguenti:
	1. Dall'elenco a discesa **Tipo di problema** selezionare **Tecnico**.
	2. Selezionare una **Sottoscrizione** dall'elenco a discesa.
	3. Dall'elenco a discesa **Servizio** selezionare **StorSimple**. 
	4. Selezionare un **Piano di supporto** dall'elenco a discesa. È necessario un piano di supporto a pagamento per poter abilitare il supporto tecnico.

4. Fare clic su **Avanti**. Verrà visualizzata la finestra di dialogo **Problema**.

	![Nuovo riquadro di richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza5a.png)

5. Nella finestra di dialogo **Problema** eseguire le operazioni seguenti:

    1.  Selezionare un livello di **Gravità** dall'elenco a discesa.
    2.  Selezionare un **Tipo di problema** dall'elenco a discesa.
    3.  Selezionare una **Categoria** dall'elenco a discesa. 
    4.  Nella casella **Dettagli** descrivere brevemente il problema.
    5.  Nella casella **Intervallo di tempo** indicare la data, l'ora e il fuso orario corrispondenti al momento in cui si è verificato il problema l'ultima volta.
    6.  In **Caricamento file** fare clic sull'icona della cartella per selezionare il pacchetto di supporto.
    7.  Selezionare la casella di controllo **Condividi informazioni diagnostica** .

6. Fare clic su **Avanti**. Verrà visualizzata la finestra di dialogo **Informazioni contatto**.

	![Nuovo riquadro di richiesta di supporto](./media/storsimple-contact-microsoft-support/Ibiza6a.png)

7. Immettere le informazioni di contatto e selezionare un metodo di contatto (telefono o posta elettronica).

8. Selezionare la casella di controllo **Salva modifiche di contatto per le richieste di supporto future**.

9. Fare clic su **Create**.

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

> [AZURE.IMPORTANT] È possibile disabilitare l'accesso del supporto eseguendo `Disable-HcsSupportAccess`. Il dispositivo StorSimple inoltre tenterà di disabilitare l'accesso del supporto dopo 8 ore dall'avvio della sessione. È consigliabile modificare le credenziali del dispositivo StorSimple dopo l'avvio di una sessione di supporto.

<!---HONumber=AcomDC_0615_2016-->