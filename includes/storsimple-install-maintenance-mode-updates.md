<!--author=SharS last changed: 9/17/15-->

#### Per installare gli aggiornamenti in modalità di manutenzione tramite Windows PowerShell per StorSimple

1. Se non è già stato fatto, accedere alla console seriale del dispositivo e selezionare l'opzione 1, **Accedi con accesso completo**. 

2. Digitare la password. La password predefinita è **Password1**.

3. Al prompt dei comandi digitare:

     `Get-HcsUpdateAvailability`
    
4. Verrà notificato se sono disponibili aggiornamenti e se gli aggiornamenti sono problematici o meno. Per applicare aggiornamenti problematici, è necessario attivare la modalità di manutenzione per il dispositivo. Per istruzioni, vedere [Passaggio 2: Attivare la modalità di manutenzione](storsimple-update-device.md#step2).

5. Quando il dispositivo è in modalità di manutenzione, avviare il prompt dei comandi e digitare: `Start-HcsUpdate`

6. Verrà richiesto di confermare. Dopo la conferma, gli aggiornamenti verranno installati nel controller a cui si è effettuato l’accesso. Una volta installati gli aggiornamenti, il controller verrà riavviato.

7. Monitorare lo stato degli aggiornamenti. Digitare:

	`Get-HcsUpdateStatus`
	
	Se `RunInProgress` è `True`, l'aggiornamento è ancora in corso. Se `RunInProgress` è `False`, indica che l'aggiornamento è stato completato.

7. Quando l'aggiornamento è installato nel controller corrente ed è stato riavviato, connettersi all'altro controller ed eseguire i passaggi da 1 a 6.

8. Dopo l'aggiornamento di entrambi i controller, uscire dalla modalità di manutenzione. Per istruzioni, vedere [Passaggio 4: Uscire dalla modalità di manutenzione](storsimple-update-device.md#step4).

<!---HONumber=Oct15_HO3-->