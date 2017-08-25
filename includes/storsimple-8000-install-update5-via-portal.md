<!--author=alkohli last changed: 08/04/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Per installare un aggiornamento dal portale di Azure

1. Nella pagina del servizio StorSimple, selezionare il proprio dispositivo.

    ![Selezionare il dispositivo](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Andare a **Impostazioni del dispositivo** > **Aggiornamenti del dispositivo**.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Se sono disponibili nuovi aggiornamenti, viene visualizzata una notifica. In alternativa, nel pannello **Aggiornamenti del dispositivo** fare clic su **Verifica la disponibilità di aggiornamenti**. Viene creato un processo per verificare la disponibilità di aggiornamenti. Al termine del processo si riceve una notifica.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Si consiglia di leggere le note sulla versione prima di installare un aggiornamento nel dispositivo. Per applicare gli aggiornamenti, fare clic su **Installa aggiornamenti**. Nel pannello **Confermare gli aggiornamenti regolari** rivedere i prerequisiti da completare prima di applicare gli aggiornamenti. Selezionare la casella di controllo per indicare che si è pronti per aggiornare il dispositivo e quindi fare clic su **Installa**.

    ![Fare clic su Aggiornamenti del dispositivo](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Viene avviato un set di controlli dei prerequisiti. I controlli includono quanto segue:
   
   * **Controlli di integrità del controller** per verificare che entrambi i controller dei dispositivi siano integri e online.
   * **Controlli di integrità del componente hardware** per verificare che tutti i componenti hardware del dispositivo StorSimple siano integri.
   * **Controlla DATA 0** per verificare che DATA 0 è attivato sul dispositivo. Se questa interfaccia non è abilitata, è necessario abilitarla e riprovare.

    L'aggiornamento viene scaricato e installato solo se tutti i controlli vengono completati correttamente. Quando i controlli sono in corso si riceve una notifica. Se le verifiche preliminari hanno esito negativo, verranno indicate le cause dell'errore. Risolvere tali problemi e quindi provare a eseguire di nuovo l'operazione. Se non è possibile risolvere tali problemi in autonomia, contattare il supporto tecnico Microsoft.

7. Dopo aver completato tutte le verifiche preliminari, viene creato un processo di aggiornamento. Dopo la creazione di tale processo si riceve una notifica.
   
    ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    A questo punto, l'aggiornamento viene applicato al dispositivo.

9. Per completare l'aggiornamento possono essere necessarie alcune ore. Selezionare il processo di aggiornamento e fare clic su **Dettagli** per visualizzare i dettagli del processo in qualsiasi momento.

    ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update5-via-portal/update8.png)

     È anche possibile monitorare lo stato del processo di aggiornamento da **Impostazioni del dispositivo > Processi**. Nel pannello **Processi** è possibile visualizzare lo stato dell'aggiornamento.

     ![Creazione del processo di aggiornamento](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Al termine del processo, passare a **Impostazioni del dispositivo > Aggiornamenti del dispositivo**. La versione del software ora risulterà aggiornata.

