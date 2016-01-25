<!--author=alkohli last changed: 01/12/15-->

#### Scaricare gli aggiornamenti rapidi

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).

2. Se si visita il sito per la prima volta, verrà richiesto di installare Microsoft Update Catalog. Fare clic su **Installa**.
    
   	![Installare il catalogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Verrà visualizzata una schermata che consente di eseguire ricerche nel catalogo. Immettere **3121901** nella casella di ricerca, quindi fare clic su **Ricerca**.

    ![Cercare nel catalogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. **Cumulative Software Bundle Update 2.0 for StorSimple 8000 Series** viene visualizzato. Fare clic su **Aggiungi**. L'aggiornamento verrà aggiunto al carrello.

5. Fare clic su **Visualizza carrello**.
 
6. Fare clic su **Download**. Specificare o **sfogliare** il percorso locale in cui si desidera salvare il file scaricato. L'aggiornamento viene scaricato in una cartella con lo stesso nome dell'aggiornamento nella posizione selezionata. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.
       
	> [AZURE.NOTE]
	> 
	> - È inoltre necessario scaricare l'**aggiornamento del driver LSI** (SAS Controller Update 2.0 for StorSimple 8000 Series - KB3121900), l'**aggiornamento Storport** (Hotfix for Windows Server 2012 R2 x64 Edition - KB3080728), l'**aggiornamento Spaceport** (Hotfix for Windows Server 2012 R2 x64 Edition - KB3090322) e l'**aggiornamento del firmware del disco** (Cumulative Disk Firmware Update 2.0 for StorSimple 8000 Series - KB3121899), quindi copiarli nella stessa cartella condivisa.
	> - L'aggiornamento rapido deve essere accessibile da entrambi i controller per rilevare eventuali messaggi di errore potenziali dal controller peer.

#### Per installare e verificare gli aggiornamenti rapidi in modalità normale

Eseguire i passaggi seguenti per installare e verificare gli aggiornamenti rapidi normali.

1. Per installare gli aggiornamenti rapidi, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Al prompt dei comandi, premere **Invio**.

4. Selezionare l'**opzione 1** per eseguire l'accesso completo al dispositivo.

5. Per installare l'aggiornamento rapido, al prompt dei comandi, digitare:

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
 
6. Digitare **Y** quando viene richiesto di confermare l'installazione dell'hotfix.

7. Monitorare l'aggiornamento utilizzando il cmdlet `Get-HcsUpdateStatus`.

    Il seguente output di esempio indica che l'aggiornamento è in corso. Il `RunInprogress` sarà `True` quando l'aggiornamento è in corso.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Il seguente output di esempio indica che l'aggiornamento è stato completato. Il `RunInProgress` sarà `False` quando l'aggiornamento è stato completato.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]In alcuni casi, i cmdlet mostrano`False` quando l'aggiornamento è ancora in corso. Per assicurarsi che l'aggiornamento rapido è stato completato, attendere alcuni minuti, eseguire nuovamente il comando e verificare che `RunInProgress` sia `False`. In caso affermativo, l'aggiornamento rapido è stato completato.
	
8. Al termine dell'aggiornamento del software, ripetere i passaggi da 3 a 5 per installare e monitorare l'agente SaaS e l'agente MDS con `CisMdsAgentUpdateBundle.exe`. Assicurarsi che `HcsMdsSoftwareUpdate.exe` sia installato prima di `CisMdsAgentUpdateBundle.exe`.

9. Verificare le versioni del software di sistema. Digitare:

    `Get-HcsSystem`

    Dovrebbero essere visualizzate le seguenti versioni:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13 
    
	Se i numeri di versione non vengono modificati dopo aver applicato l'aggiornamento, significa che non è stato possibile applicare l'aggiornamento rapido. Se si verifica ciò, contattare [il supporto tecnico di Microsoft](storsimple-contact-microsoft-support.md) per assistenza.
    
9. Ripetere i passaggi da 3 a 5 per installare e monitorare il resto degli aggiornamenti rapidi normali.

	- Il driver LSI con il pacchetto `HcsLsiUpdate.exe` (KB3121900).
	- La correzione Storport con il pacchetto `Storport-KB3080728-x64.msu` (KB3080728).
	- La correzione Spaceport con il pacchetto `spaceport-KB3090322-x64.msu` (KB3090322).

#### Per installare e verificare l'aggiornamento rapido in modalità di manutenzione

Usare il pacchetto `DiskFirmwarePackage.exe` (KB3121899) per installare gli aggiornamenti firmware del disco. Si tratta di aggiornamenti problematici che richiedono circa 30 minuti per il completamento. È possibile scegliere di installare tali aggiornamenti in una finestra di manutenzione pianificata tramite la connessione alla console seriale del dispositivo. Per installare gli aggiornamenti firmware del disco, seguire le istruzioni seguenti.

1. Attivare la modalità di manutenzione per il dispositivo. Notare che non si deve utilizzare Windows PowerShell in remoto quando ci si connette a un dispositivo in modalità di manutenzione. È necessario eseguire questo cmdlet nel controller del dispositivo quando si è connessi tramite console seriale del dispositivo. Digitare:
		
	`Enter-HcsMaintenanceMode`

	Di seguito è riportato un output di esempio.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Entrambi i controller vengono riavviati. Dopo aver completato il riavvio, entrambi i controller sono in modalità di manutenzione.

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
 
2.  Una volta completata l'installazione, il controller in cui è stato installato l'aggiornamento rapido in modalità di manutenzione viene riavviato. Accedere all'opzione 1 con accesso completo e verificare la versione del firmware del disco. Digitare:
	
	`Get-HcsFirmwareVersion`
  
	Le versioni del firmware del disco previste sono:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Di seguito è riportato un output di esempio.


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
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

	 Eseguire il comando `Get-HcsFirmwareVersion` sul secondo controller per verificare che la versione del software sia stata aggiornata. È quindi possibile chiudere la modalità di manutenzione. Digitare il comando seguente per ogni controller del dispositivo:

    `Exit-HcsMaintenanceMode`
     
1. I controller vengono riavviati quando si esce dalla modalità di manutenzione. Dopo la corretta istallazione degli aggiornamenti del firmware del disco e dopo che il dispositivo ha terminato la modalità manutenzione, tornare al portale di Azure classico. Gli aggiornamenti in modalità manutenzione non vengono aggiornati nel portale fino a quando non sono trascorse 24 ore.






 
 

<!---HONumber=AcomDC_0114_2016-->