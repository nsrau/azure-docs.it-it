<!--author=SharS last changed: 9/17/15-->

#### Per installare gli aggiornamenti rapidi in modalità di manutenzione tramite Windows PowerShell per StorSimple
> [!IMPORTANT]
> In modalità di manutenzione, è necessario applicare l’aggiornamento rapido prima in un controller e quindi nell’altro controller.
> 
> 

1. Attivare la modalità di manutenzione per il dispositivo. Per visualizzare istruzioni su come attivare la modalità di manutenzione, vedere [Passaggio 2: Attivare la modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step2).
2. Per applicare l’aggiornamento rapido, digitare:
   
     `Start-HcsHotfix`
3. Quando richiesto, specificare il percorso della cartella condivisa di rete che contiene i file dell'aggiornamento rapido.
4. Verrà richiesto di confermare. Digitare **Y** per procedere con l'installazione dell'aggiornamento rapido.
5. Dopo aver applicato l'aggiornamento rapido in un controller, accedere all’altro controller. Applicare l'aggiornamento rapido come è stato fatto per il precedente controller.
6. Dopo aver applicato gli aggiornamenti rapidi, uscire dalla modalità di manutenzione. Per istruzioni, vedere [Passaggio 4: Uscire dalla modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step4).

<!---HONumber=Oct15_HO3-->