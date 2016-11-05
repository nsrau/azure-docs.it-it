<!--author=SharS last changed: 03/17/2016-->

#### Scaricare gli hotfix
Attenersi alla seguente procedura per scaricare l'aggiornamento del software.

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Se si utilizza per la prima volta Microsoft Update Catalog su questo computer, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo di Microsoft Update Catalog. ![Installare il catalogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero della Knowledge Base (KB) dell'aggiornamento rapido da scaricare, ad esempio **3063418**, quindi fare clic su **Cerca**.
4. Verrà visualizzato il bundle **StorSimple Update 1.2 Appliance Update**. Fare clic su **Aggiungi**. L'aggiornamento verrà aggiunto al carrello.
5. Cercare gli eventuali aggiornamenti rapidi aggiuntivi elencati nella tabella precedente (**3043005** e **3063416**) e aggiungerli al carrello.
6. Fare clic su **Visualizza carrello**.
   
    ![Visualizzare il carrello](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Fare clic su **Download**. Specificare o **sfogliare** il percorso locale in cui si desidera salvare il file scaricato. Gli aggiornamenti vengono scaricati nel percorso specificato e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

> [!NOTE]
> Gli aggiornamenti rapidi devono essere accessibili da entrambi i controller per rilevare eventuali messaggi di errore potenziali dal controller peer.
> 
> 

#### Per installare e verificare gli hotfix in modalità normale
Eseguire i passaggi seguenti per installare e verificare gli aggiornamenti rapidi in modalità normale. Se sono già stati installati tramite il portale di Azure, passare direttamente a [installare e verificare gli aggiornamenti rapidi in modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes).

1. Per installare l'aggiornamento del software, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Al prompt dei comandi, premere **Invio**.
2. Selezionare l'**opzione 1** per eseguire l'accesso completo al dispositivo.
3. Per installare il pacchetto di aggiornamento, al prompt dei comandi, digitare:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilizzare l’IP anziché il DNS nel percorso condivisione nel comando precedente. Il parametro di credenziale viene utilizzato soltanto se si accede a una condivisione autenticata.
   
    È consigliabile utilizzare il parametro delle credenziali per accedere alle condivisioni. Anche le condivisioni aperte a "tutti" non sono in genere aperte agli utenti non autenticati.
   
    Di seguito è riportato un output di esempio.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Digitare **Y** quando viene richiesto di confermare l'installazione dell'hotfix.
5. Monitorare l'aggiornamento utilizzando il cmdlet `Get-HcsUpdateStatus`.
   
    Il seguente output di esempio indica che l'aggiornamento è in corso. Il `RunInprogress` sarà `True` quando l'aggiornamento è in corso.
   
        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````
   
     Il seguente output di esempio indica che l'aggiornamento è stato completato. Il `RunInProgress` sarà `False` quando l'aggiornamento è stato completato.
   
        ````
        Controller1>Get-HcsUpdateStatus
   
        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
   
        ````
   
   > [!NOTE]
   > In alcuni casi, i cmdlet mostrano`False` quando l'aggiornamento è ancora in corso. Per assicurarsi che l'aggiornamento rapido è stato completato, attendere alcuni minuti, eseguire nuovamente il comando e verificare che `RunInProgress` sia `False`. In caso affermativo, l'aggiornamento rapido è stato completato.
   > 
   > 
6. Dopo aver installato gli aggiornamenti del software, verificare le versioni del software del sistema. Digitare il seguente comando:
   
    `Get-HcsSystem`
   
    Dovrebbero essere visualizzate le seguenti versioni:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     Se i numeri di versione non vengono modificati dopo aver applicato l'aggiornamento, significa che non è stato possibile applicare l'aggiornamento rapido. Se si verifica ciò, contattare [il supporto tecnico di Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) per assistenza.
7. Ripetere i passaggi da 3 a 5 per installare il resto degli aggiornamenti rapidi in modalità normale (KB3043005).

#### Per installare e verificare gli aggiornamenti rapidi in modalità di manutenzione
Usare KB3063416 per installare gli aggiornamenti del firmware del disco. Si tratta di aggiornamenti con interruzione del servizio che richiedono circa 30-45 minuti per il completamento. È possibile scegliere di installare tali aggiornamenti in una finestra di manutenzione pianificata tramite la connessione alla console seriale del dispositivo.

Per installare gli aggiornamenti del firmware del disco, seguire le istruzioni riportate sotto.

1. Attivare la modalità di manutenzione per il dispositivo. Notare che non si deve utilizzare Windows PowerShell in remoto quando ci si connette a un dispositivo in modalità di manutenzione. È necessario eseguire questo cmdlet nel controller del dispositivo quando si è connessi tramite console seriale del dispositivo. Digitare:
   
    `Enter-HcsMaintenanceMode`
   
    Di seguito è riportato un output di esempio.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Entrambi i controller si riavviano in modalità manutenzione.
2. Per installare l'aggiornamento firmware del disco, digitare:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Di seguito è riportato un output di esempio.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitorare l'avanzamento dell'installazione con il comando `Get-HcsUpdateStatus`. L'aggiornamento è completo quando `RunInProgress` diventa `False`.
4. Una volta completata l'installazione, il controller in cui è stato installato l'hotfix in modalità di manutenzione viene riavviato. Accedere all'opzione 1 con accesso completo e verificare la versione del firmware del disco. Digitare:
   
   `Get-HcsFirmwareVersion`
   
   Le versioni del firmware del disco previste sono:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Eseguire il comando `Get-HcsFirmwareVersion` sul secondo controller per verificare che la versione del software sia stata aggiornata. È quindi possibile chiudere la modalità di manutenzione. Digitare il comando seguente per ogni controller del dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. I controller si riavviano quando si esce dalla modalità di manutenzione. Dopo la corretta istallazione degli aggiornamenti del firmware del disco e dopo che il dispositivo ha terminato la modalità manutenzione, tornare al portale di Azure classico. Sul portale potrebbe non essere visualizzata l’installazione degli aggiornamenti di modalità manutenzione per 24 ore.

<!---HONumber=AcomDC_0323_2016-->