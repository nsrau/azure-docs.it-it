<!--author=SharS last changed: 01/15/2016-->

#### Per installare l'aggiornamento 1.2 dal portale di Azure classico
1. Nella pagina del servizio StorSimple, selezionare il proprio dispositivo. Andare a **Dispositivi** > **Manutenzione**.
2. Nella parte inferiore della pagina, fare clic su **Cerca aggiornamenti**. Viene creato un processo che consente di cercare gli aggiornamenti disponibili. L'utente riceve una notifica al termine del processo.
3. Nella sezione **Aggiornamenti software** presente nella stessa pagina, è possibile verificare la presenza dei nuovi aggiornamenti del software. Si consiglia di leggere le note sulla versione, prima di installare l'aggiornamento 1.2 nel dispositivo.
   
    ![Installare gli aggiornamenti del software](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)
4. Nella parte inferiore della pagina, fare clic su **Installa aggiornamenti**.
5. Verrà richiesto di confermare. Fare clic su **OK**.
6. Viene visualizzata la finestra di dialogo **Installa aggiornamenti**. Assicurarsi che il dispositivo sia conforme ai controlli elencati nella finestra di dialogo. Questi passaggi sono stati completati prima dell'aggiornamento. Selezionare **I requisiti sono chiari ed p possibile aggiornare il dispositivo**. Fare clic sull'icona del segno di spunta
   
    ![Messaggio di conferma](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)
7. Verrà ora avviato un set di controlli preliminari automatici. Sono state illustrate le seguenti operazioni:
   
   * **Controlli di integrità del controller** per verificare che entrambi i controller dei dispositivi siano integri e online.
   * **Controlli di integrità del componente hardware** per verificare che tutti i componenti hardware del dispositivo StorSimple siano integri.
   * **Controlla DATA 0** per verificare che DATA 0 è attivato sul dispositivo. Se questa interfaccia non è abilitata, è necessario abilitarlo e riprovare.
   * **Controlli DATA 2 e 3 dati** per verificare che la DATA 2 e 3 dati interfacce di rete non sono abilitate. Se queste interfacce sono abilitate, sarà necessario disattivarli e quindi si tenta di aggiornare il dispositivo. Questo controllo viene eseguito solo se si sta aggiornando un dispositivo che esegue GA. Dispositivi che eseguono versioni 0,3, 0,2 o 0,1 questo controllo non è necessario.
   * **Controllo gateway** su qualsiasi dispositivo esegue una versione precedente Update 1. Questo controllo viene eseguito su tutti i dispositivi che eseguono un software con una versione precedente all’aggiornamento 1 ma non ha esito positivo su dispositivi con un gateway configurato per un'interfaccia di rete diverso da DATA 0.
     
     L’aggiornamento 1.2 verrà applicato solo se tutti i controlli di pre-aggiornamento sono stati completati correttamente. Quando sono in corso i controlli preliminari dell'aggiornamento, l'utente riceve una notifica.
     
     ![Notifica sul controllo preliminare](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)
     
     Di seguito è riportato un esempio in cui i controlli preliminari all'aggiornamento hanno esito negativo. È necessario verificare che entrambi i controller di dominio siano integri e online. Inoltre, è necessario verificare lo stato di integrità dei componenti hardware. In questo esempio, il controller 0 e il controller 1 richiedono l'attenzione dell'utente. Se non è possibile risolvere tali problemi in autonomia, contattare il supporto tecnico Microsoft.
     
       ![Controllo preliminare non riuscito](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)
     
     > [!NOTE]
     > Dopo aver applicato l’aggiornamento 1.2 nel dispositivo StorSimple, gli aggiornamenti futuri non avranno i controlli DATA 2 e DATA 3 e la verifica del Gateway. Gli altri controlli preliminari saranno ancora necessari.
     > 
     > 
8. Dopo aver completato tutti i controlli preliminari all'aggiornamento, verrà creato un processo di aggiornamento. Una volta creato tale processo, l'utente riceve una notifica.
   
    ![Creazione del processo di aggiornamento](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)
   
    A questo punto, è possibile installare l'aggiornamento nel dispositivo.
9. Per controllare l'avanzamento del processo di aggiornamento, fare clic su **Visualizza processo**. Nella pagina **Processi**, è possibile visualizzare lo stato di avanzamento dell'aggiornamento.
   
    ![Avanzamento del processo di aggiornamento](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)
10. Il completamento dell'aggiornamento potrebbe durare alcune ore. Tuttavia, è possibile visualizzare i dettagli del processo in qualsiasi momento.
    
    ![Dettagli del processo di aggiornamento](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)
11. Una volta completato l'aggiornamento, andare alla pagina **Manutenzione** e scorrere verso il basso fino a **Aggiornamenti software**.
12. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 1.2 della serie 8000 di StorSimple (6.3.9600.17584)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**.
    
    ![Pagina di manutenzione](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)
13. Si vedrà ora che sono disponibili gli aggiornamenti in modalità manutenzione. Questi aggiornamenti sono aggiornamenti problematici che comportano tempi di inattività del dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. Seguire le istruzioni in [Installare gli aggiornamenti in modalità manutenzione](../articles/storsimple/storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) per installare gli aggiornamenti tramite Windows PowerShell per StorSimple.

> [!NOTE]
> In alcuni casi, il messaggio che indica che gli aggiornamenti in modalità manutenzione sono disponibili può essere visualizzato fino a 24 ore dopo la corretta applicazione di tali aggiornamenti sul dispositivo.
> 
> 

<!---HONumber=AcomDC_0121_2016-->