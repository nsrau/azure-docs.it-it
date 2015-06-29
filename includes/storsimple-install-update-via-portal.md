<properties 
   pageTitle="Installare l'aggiornamento 1 dal portale di gestione di Azure"
   description="Viene illustrato come utilizzare il portale di gestione per installare l'aggiornamento 1 della serie 8000 di StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/22/2015"
   ms.author="v-sharos" />

#### Per installare l'aggiornamento 1 dal portale di gestione

1. Nella pagina del servizio StorSimple, selezionare il proprio dispositivo. Andare a **Dispositivi** \> **Manutenzione**.

2. Nella parte inferiore della pagina, fare clic su **Cerca aggiornamenti**. Viene creato un processo che consente di cercare gli aggiornamenti disponibili. L'utente riceve una notifica al termine del processo.

3. Nella sezione **Aggiornamenti software** presente nella stessa pagina, è possibile verificare la presenza dei nuovi aggiornamenti del software. Si consiglia di leggere le note sulla versione, prima di installare l'aggiornamento 1.0 nel dispositivo.

    ![Installare gli aggiornamenti del software](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. Nella parte inferiore della pagina, fare clic su **Installa aggiornamenti**.

5. Verrà richiesto di confermare. Fare clic su **OK**.

6. Viene visualizzata la finestra di dialogo **Installa aggiornamenti**. Assicurarsi che il dispositivo sia conforme ai controlli elencati nella finestra di dialogo. Selezionare **I requisiti sono chiari ed p possibile aggiornare il dispositivo**. Fare clic sull'icona del segno di spunta

    ![Messaggio di conferma](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. Quando sono in corso i controlli preliminari dell'aggiornamento, l'utente riceve una notifica.
  
    ![Notifica sul controllo preliminare](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    Di seguito è riportato un esempio in cui i controlli preliminari all'aggiornamento hanno esito negativo. È necessario verificare che entrambi i controller di dominio siano integri e online. Inoltre, è necessario verificare lo stato di integrità dei componenti hardware. In questo esempio, il controller 0 e il controller 1 richiedono l'attenzione dell'utente. Se non è possibile risolvere tali problemi in autonomia, contattare il supporto tecnico Microsoft.

    ![Controllo preliminare non riuscito](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Dopo aver completato tutti i controlli preliminari all'aggiornamento, verrà creato un processo di aggiornamento. Una volta creato tale processo, l'utente riceve una notifica.
 
    ![Creazione del processo di aggiornamento](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    A questo punto, è possibile installare l'aggiornamento nel dispositivo.
 
9. Per controllare l'avanzamento del processo di aggiornamento, fare clic su **Visualizza processo**. Nella pagina Processi, è possibile visualizzare lo stato di avanzamento dell'aggiornamento.

    ![Avanzamento del processo di aggiornamento](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. Il completamento dell'aggiornamento potrebbe durare alcune ore. Tuttavia, è possibile visualizzare i dettagli del processo in qualsiasi momento.

    ![Dettagli del processo di aggiornamento](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. Una volta completato l'aggiornamento, andare alla pagina **Manutenzione** e scorrere verso il basso fino a **Aggiornamenti software**.

12. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 1.0 della serie 8000 di StorSimple \(6.3.9600.17491\)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**.

    ![Pagina di manutenzione](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=58_postMigration-->