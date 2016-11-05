<!--author=alkohli last changed: 03/17/16-->

#### Per installare un aggiornamento dal portale di Azure
1. Nella pagina del servizio StorSimple, selezionare il proprio dispositivo. Andare a **Dispositivi** > **Manutenzione**.
2. Nella parte inferiore della pagina, fare clic su **Cerca aggiornamenti**. Viene creato un processo che consente di cercare gli aggiornamenti disponibili. L'utente riceve una notifica al termine del processo.
3. Nella sezione **Aggiornamenti software** presente nella stessa pagina, è possibile verificare la presenza dei nuovi aggiornamenti del software. Si consiglia di leggere le note sulla versione prima di installare un aggiornamento nel dispositivo.
4. Nella parte inferiore della pagina, fare clic su **Installa aggiornamenti**, quindi su **OK**.
5. Nella finestra di dialogo **Installa aggiornamenti**, assicurarsi di aver seguito le indicazioni, quindi selezionare **Dichiaro di avere compreso il requisito indicato sopra e di essere pronto per l'aggiornamento del dispositivo** e fare clic sul segno di spunta.
   
    ![Messaggio di conferma](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Verrà ora avviato un set di controlli dei prerequisiti. Sono state illustrate le seguenti operazioni:
   
   * **Controlli di integrità del controller** per verificare che entrambi i controller dei dispositivi siano integri e online.
   * **Controlli di integrità del componente hardware** per verificare che tutti i componenti hardware del dispositivo StorSimple siano integri.
   * **Controlla DATA 0** per verificare che DATA 0 è attivato sul dispositivo. Se questa interfaccia non è abilitata, è necessario abilitarlo e riprovare.
   * **Controlli DATA 2 e 3 dati** per verificare che la DATA 2 e 3 dati interfacce di rete non sono abilitate. Se queste interfacce sono abilitate, sarà necessario disattivarli e quindi si tenta di aggiornare il dispositivo. Questo controllo viene eseguito solo se si sta aggiornando un dispositivo che esegue GA. Dispositivi che eseguono versioni 0,3, 0,2 o 0,1 questo controllo non è necessario.
   * **Controllo gateway** su qualsiasi dispositivo esegue una versione precedente Update 1. Questo controllo viene eseguito su tutti i dispositivi che eseguono un software con una versione precedente all’aggiornamento 1 ma non ha esito positivo su dispositivi con un gateway configurato per un'interfaccia di rete diverso da DATA 0.
     
     Se tutti i controlli vengono eseguiti correttamente, viene applicato l'aggiornamento. Quando sono in corso i controlli, l'utente riceve una notifica.
     
     ![Notifica sul controllo preliminare](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     Di seguito è riportato un esempio in cui i controlli hanno esito negativo. È necessario verificare che entrambi i controller di dominio siano integri e online. Inoltre, è necessario verificare lo stato di integrità dei componenti hardware. In questo esempio, il controller 0 e il controller 1 richiedono l'attenzione dell'utente. Se non è possibile risolvere tali problemi in autonomia, contattare il supporto tecnico Microsoft.
     
       ![Controlli non riusciti](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Dopo aver completato tutti i controlli, verrà creato un processo di aggiornamento. Una volta creato tale processo, l'utente riceve una notifica.
   
    ![Creazione del processo di aggiornamento](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    A questo punto, è possibile installare l'aggiornamento nel dispositivo.
8. Per controllare l'avanzamento del processo di aggiornamento, fare clic su **Visualizza processo**. Nella pagina **Processi**, è possibile visualizzare lo stato di avanzamento dell'aggiornamento.
9. Il completamento dell'aggiornamento potrebbe durare alcune ore. Selezionare il processo di aggiornamento e fare clic su **Dettagli** per visualizzare i dettagli del processo in qualsiasi momento.
10. Una volta completato l'aggiornamento, andare alla pagina **Manutenzione** e scorrere verso il basso fino a **Aggiornamenti software**.

<!---HONumber=AcomDC_0323_2016-->