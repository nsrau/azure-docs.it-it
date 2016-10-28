<!--author=alkohli last changed: 05/19/16-->

#### Scaricare gli hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si usa Microsoft Update Catalog nel computer per la prima volta, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog. ![Installare il catalogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Nella casella di ricerca di Microsoft Update Catalog, immettere il numero della Knowledge Base (KB) dell'hotfix da scaricare, ad esempio **3179904**, quindi fare clic su **Ricerca**.

    Viene visualizzato l'elenco degli hotfix, ad esempio, **Aggiornamento cumulativo del pacchetto software 2.2 per StorSimple serie 8000**.

    ![Cercare nel catalogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Fare clic su **Aggiungi**. L'aggiornamento viene aggiunto al carrello.

5. Cercare gli eventuali hotfix aggiuntivi elencati nella tabella precedente (**3103616**, **3146621**) e aggiungerli al carrello.

5. Fare clic su **Visualizza carrello**.

6. Fare clic su **Download**. Specificare o **selezionare** il percorso locale in cui salvare i file scaricati. Gli aggiornamenti vengono scaricati nel percorso specificato e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

>   [AZURE.NOTE]
Gli aggiornamenti rapidi devono essere accessibili da entrambi i controller per rilevare eventuali messaggi di errore potenziali dal controller peer.

#### Per installare e verificare gli hotfix in modalità normale

Per installare e verificare gli aggiornamenti rapidi in modalità normale, seguire questa procedura. Se sono già stati installati tramite il portale di Azure, passare direttamente a [installare e verificare gli hotfix in modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes).

1. Per installare gli hotfix, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Al prompt dei comandi, premere **Invio**.

4. Selezionare l'**opzione 1** per eseguire l'accesso completo al dispositivo. È consigliabile installare innanzitutto l'hotfix sul controller passivo.

5. Per installare l'hotfix, al prompt dei comandi, digitare:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Utilizzare l’IP anziché il DNS nel percorso condivisione nel comando precedente. Il parametro di credenziale viene utilizzato soltanto se si accede a una condivisione autenticata.

	È consigliabile utilizzare il parametro delle credenziali per accedere alle condivisioni. Anche le condivisioni aperte a "tutti" non sono in genere aperte agli utenti non autenticati.

	Specificare la password quando richiesto.

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

6. Digitare **Y** quando viene richiesto di confermare l'installazione dell'hotfix.

	> [AZURE.IMPORTANT] Se si installa l'aggiornamento 2.2, installare solo il file binario preceduto da "all-hcsmdssoftwareudpate". Non installare gli elementi di configurazione e l'aggiornamento dell'agente MDS preceduti da all-cismdsagentupdatebundle. In caso contrario, verrà generato un errore.

7. Monitorare l'aggiornamento utilizzando il cmdlet `Get-HcsUpdateStatus`. L'aggiornamento verrà innanzitutto completato sul controller passivo. Dopo aver aggiornato il controller passivo, si verificherà un failover e l'aggiornamento verrà quindi applicato all'altro controller. L'aggiornamento è completato quando entrambi i controller vengono aggiornati.

    Il seguente output di esempio indica che l'aggiornamento è in corso. Il `RunInprogress` sarà `True` quando l'aggiornamento è in corso.

        ````
        Controller0>Get-HcsUpdateStatus
		RunInprogress       : True
		LastHotfixTimestamp :
		LastUpdateTimestamp : 5/5/2016 2:04:02 AM
		Controller0Events   :
		Controller1Events   :

        ````

     Il seguente output di esempio indica che l'aggiornamento è stato completato. Il `RunInProgress` sarà `False` quando l'aggiornamento è stato completato.

        ````
        Controller0>Get-HcsUpdateStatus
		RunInprogress       : False
		LastHotfixTimestamp : 5/17/2016 9:15:55 AM
		LastUpdateTimestamp : 5/17/2016 9:06:07 AM
		Controller0Events   :
		Controller1Events   :


        ````

	> [AZURE.NOTE] In alcuni casi, i cmdlet mostrano`False` quando l'aggiornamento è ancora in corso. Per assicurarsi che l'aggiornamento rapido è stato completato, attendere alcuni minuti, eseguire nuovamente il comando e verificare che `RunInProgress` sia `False`. In caso affermativo, l'aggiornamento rapido è stato completato.

8. Dopo aver installato gli aggiornamenti del software, verificare le versioni del software del sistema. Digitare:

    `Get-HcsSystem`

    Dovrebbero essere visualizzate le seguenti versioni:

    - `HcsSoftwareVersion: 6.3.9600.17708`
    - `CisAgentVersion: 1.0.9299.0`
    - `MdsAgentVersion: 30.0.4698.16`

	Se i numeri di versione non vengono modificati dopo aver applicato l'aggiornamento, significa che non è stato possibile applicare l'aggiornamento rapido. Se si verifica ciò, contattare [il supporto tecnico di Microsoft](storsimple-contact-microsoft-support.md) per assistenza.
	
	> [AZURE.IMPORTANT] È necessario riavviare il controller attivo tramite il cmdlet `Restart-HcsController` prima di applicare gli altri aggiornamenti.

9. Ripetere i passaggi da 3 a 5 per installare il resto degli aggiornamenti rapidi in modalità normale.

	- L'aggiornamento di iSCSI KB3146621
	
	- L'aggiornamento WMI KB3103616
	

10. Ignorare questo passaggio se si aggiorna da Update 2. Se si sta aggiornando una versione precedente a Update 2, è inoltre necessario scaricare:


	- Il driver LSI KB3121900

	- L'aggiornamento di Spaceport KB3090322
	
	- L'aggiornamento di Storport KB3080728

#### Per installare e verificare gli aggiornamenti rapidi in modalità di manutenzione

Usare KB3121899 per installare gli aggiornamenti del firmware del disco. Si tratta di aggiornamenti problematici che richiedono circa 30 minuti per il completamento. È possibile scegliere di installare tali aggiornamenti in una finestra di manutenzione pianificata tramite la connessione alla console seriale del dispositivo.

Se il firmware del disco è già aggiornato, non è necessario installare questi aggiornamenti. Eseguire il cmdlet `Get-HcsUpdateAvailability` dalla console seriale del dispositivo per verificare se sono disponibili aggiornamenti e se questi comportano o meno interruzioni del servizio e vanno quindi installati, rispettivamente, in modalità di manutenzione o in modalità normale.

Per installare gli aggiornamenti del firmware del disco, seguire le istruzioni riportate sotto.

1. Attivare la modalità di manutenzione per il dispositivo. Notare che non si deve utilizzare Windows PowerShell in remoto quando ci si connette a un dispositivo in modalità di manutenzione. Eseguire questo cmdlet nel controller del dispositivo quando si è connessi tramite console seriale del dispositivo. Digitare:

	`Enter-HcsMaintenanceMode`

	Di seguito è riportato un output di esempio.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76673
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

3. Per installare l'aggiornamento firmware del disco, digitare:

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	Di seguito è riportato un output di esempio.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Monitorare l'avanzamento dell'installazione con il comando `Get-HcsUpdateStatus`. L'aggiornamento è completo quando `RunInProgress` diventa `False`.

2.  Al termine dell'installazione, il controller in cui è stato installato l'aggiornamento rapido in modalità di manutenzione viene riavviato. Accedere all'opzione 1 con accesso completo e verificare la versione del firmware del disco. Digitare:

	`Get-HcsFirmwareVersion`

	Le versioni del firmware del disco previste sono:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Di seguito è riportato un output di esempio.

        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17705
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------

    	Controller1>Get-HcsFirmwareVersion

    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C

    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 Eseguire il comando `Get-HcsFirmwareVersion` sul secondo controller per verificare che la versione del software sia stata aggiornata. È quindi possibile chiudere la modalità di manutenzione. A tale scopo, digitare il comando seguente per ogni controller del dispositivo:

    `Exit-HcsMaintenanceMode`

1. I controller si riavviano quando si esce dalla modalità di manutenzione. Dopo la corretta istallazione degli aggiornamenti del firmware del disco e dopo che il dispositivo ha terminato la modalità manutenzione, tornare al portale di Azure classico. Sul portale potrebbe non essere visualizzata l’installazione degli aggiornamenti di modalità manutenzione per 24 ore.

<!---HONumber=AcomDC_0803_2016-->