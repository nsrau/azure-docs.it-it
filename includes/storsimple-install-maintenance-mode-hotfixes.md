
#### Per installare gli aggiornamenti rapidi in modalità di manutenzione tramite Windows PowerShell per StorSimple

> [AZURE.IMPORTANT]In modalità di manutenzione, è necessario applicare l’aggiornamento rapido prima in un controller e quindi nell’altro controller.

1. Attivare la modalità di manutenzione per il dispositivo. Vedere [Attivare la modalità di manutenzione](#enter-maintenance-mode) del dispositivo per visualizzare istruzioni su come attivare la modalità di manutenzione.

2. Per applicare l’aggiornamento rapido, digitare:

     `Start-HcsHotfix`

3. Quando richiesto, specificare il percorso della cartella condivisa di rete che contiene i file dell'aggiornamento rapido.

4. Verrà richiesto di confermare. Digitare **Y** per procedere con l'installazione dell'aggiornamento rapido.

5. Dopo aver applicato l'aggiornamento rapido in un controller, accedere all’altro controller. Applicare l'aggiornamento rapido come è stato fatto per il precedente controller.

6. Dopo aver applicato gli aggiornamenti rapidi, uscire dalla modalità di manutenzione. Vedere [Uscire dalla modalità di manutenzione](#exit-maintenance-mode) per visualizzare istruzioni.

<!---HONumber=July15_HO4-->