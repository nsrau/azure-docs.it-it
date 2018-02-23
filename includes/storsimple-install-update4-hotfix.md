<!--author=alkohli last changed: 01/23/18-->

#### <a name="to-download-hotfixes"></a>Scaricare gli hotfix

Eseguire i passaggi seguenti per scaricare l'aggiornamento del software da Microsoft Update Catalog.

1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Se si usa Microsoft Update Catalog nel computer per la prima volta, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo Microsoft Update Catalog.

    ![Installare il catalogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Nella casella di ricerca di Microsoft Update Catalog immettere il numero della Knowledge Base (KB) relativo all'hotfix da scaricare, ad esempio **4011839**, quindi fare clic su **Cerca**.
   
    Verrà visualizzato l'elenco degli hotfix, ad esempio, **Cumulative Software Bundle Update 4.0 for StorSimple 8000 Series**(Aggiornamento cumulativo del pacchetto software 4.0 per StorSimple serie 8000).
   
    ![Cercare nel catalogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Fare clic su **Download**. Specificare o **sfogliare** il percorso locale in cui si desidera salvare il file scaricato. Fare clic sui file da scaricare nel percorso e nella cartella specificati. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.
5. Cercare gli eventuali hotfix aggiuntivi elencati nella tabella precedente (**4011841**) e scaricare i file corrispondenti nelle cartelle specifiche indicate in tale tabella.

> [!NOTE]
> Gli aggiornamenti rapidi devono essere accessibili da entrambi i controller per rilevare eventuali messaggi di errore potenziali dal controller peer.
>
> È necessario copiare gli hotfix in tre cartelle separate. Ad esempio, l'aggiornamento del software del dispositivo o dell'agente Cis/MDS può essere copiato nella cartella _FirstOrderUpdate_, tutti gli altri aggiornamenti che non comportano interruzioni del servizio possono essere copiati nella cartella _SecondOrderUpdate_ e gli aggiornamenti per la modalità di manutenzione possono essere copiati nella cartella _ThirdOrderUpdate_.

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Per installare e verificare gli hotfix in modalità normale

Per installare e verificare gli aggiornamenti rapidi in modalità normale, seguire questa procedura. Se sono già stati installati tramite il portale di Azure classico, passare direttamente a [installare e verificare gli hotfix per la modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes).

1. Per installare gli hotfix, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Seguire le istruzioni riportate in [Usare PuTTY per connettersi alla console seriale](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Al prompt dei comandi, premere **Invio**.
2. Selezionare l' **opzione 1** per eseguire l'accesso completo al dispositivo. È consigliabile installare innanzitutto l'hotfix sul controller passivo.
3. Per installare l'hotfix, al prompt dei comandi, digitare:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilizzare l’IP anziché il DNS nel percorso condivisione nel comando precedente. Il parametro di credenziale viene utilizzato soltanto se si accede a una condivisione autenticata.
   
    È consigliabile utilizzare il parametro delle credenziali per accedere alle condivisioni. Anche le condivisioni aperte a "tutti" non sono in genere aperte agli utenti non autenticati.
   
    Specificare la password quando richiesto.
   
    Di seguito è riportato un output di esempio per l'installazione degli aggiornamenti di primo livello. Per l'aggiornamento di primo livello, è necessario fare riferimento al file specifico.
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. Digitare **Y** quando viene richiesto di confermare l'installazione dell'hotfix.
5. Monitorare l'aggiornamento utilizzando il cmdlet `Get-HcsUpdateStatus` . L'aggiornamento verrà innanzitutto completato sul controller passivo. Dopo aver aggiornato il controller passivo, si verificherà un failover e l'aggiornamento verrà quindi applicato all'altro controller. L'aggiornamento è completato quando entrambi i controller vengono aggiornati.
   
    Il seguente output di esempio indica che l'aggiornamento è in corso. Il `RunInprogress` sarà `True` quando l'aggiornamento è in corso.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Il seguente output di esempio indica che l'aggiornamento è stato completato. Il `RunInProgress` sarà `False` quando l'aggiornamento è stato completato.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > In alcuni casi, i cmdlet mostrano`False` quando l'aggiornamento è ancora in corso. Per assicurarsi che l'aggiornamento rapido è stato completato, attendere alcuni minuti, eseguire nuovamente il comando e verificare che `RunInProgress` sia `False`. In caso affermativo, l'aggiornamento rapido è stato completato.

6. Dopo aver installato gli aggiornamenti del software, verificare le versioni del software del sistema. Digitare: 
   
    `Get-HcsSystem`
   
    Dovrebbero essere visualizzate le seguenti versioni:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   *  `HcsSoftwareVersion: 6.3.9600.17820`
   
    Se il numero di versione non cambia dopo aver applicato l'aggiornamento, non è stato possibile applicare l'hotfix. In questo caso contattare il [Supporto tecnico Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) per assistenza.
     
    > [!IMPORTANT]
    > Prima di applicare l'aggiornamento successivo è necessario riavviare il controller attivo tramite il cmdlet `Restart-HcsController`.
     
7. Ripetere i passaggi 3-5 per installare l'agente Cis/MDS scaricato nella cartella _FirstOrderUpdate_. 
8. Ripetere i passaggi da 3 a 5 per installare gli aggiornamenti di secondo livello. **Per gli aggiornamenti di secondo livello, è possibile installare più aggiornamenti eseguendo semplicemente `Start-HcsHotfix cmdlet` e puntando alla cartella in cui si trovano gli aggiornamenti di secondo livello. Il cmdlet eseguirà tutti gli aggiornamenti disponibili nella cartella.** Se è già installato un aggiornamento, la logica di aggiornamento lo rileva e non applica l'aggiornamento. 

Dopo aver installato tutti gli hotfix, usare il cmdlet `Get-HcsSystem`. Le versioni devono essere:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Per installare e verificare gli aggiornamenti rapidi in modalità di manutenzione
Usare KB4011837 per installare gli aggiornamenti del firmware del disco. Si tratta di aggiornamenti problematici che richiedono circa 30 minuti per il completamento. È possibile scegliere di installare tali aggiornamenti in una finestra di manutenzione pianificata tramite la connessione alla console seriale del dispositivo.

Se il firmware del disco è già aggiornato, non è necessario installare questi aggiornamenti. Eseguire il cmdlet `Get-HcsUpdateAvailability` dalla console seriale del dispositivo per verificare se sono disponibili aggiornamenti e se questi comportano o meno interruzioni del servizio e vanno quindi installati, rispettivamente, in modalità di manutenzione o in modalità normale.

Per installare gli aggiornamenti del firmware del disco, seguire le istruzioni riportate sotto.

1. Attivare la modalità di manutenzione per il dispositivo. **Non è consigliabile usare Windows PowerShell in remoto quando ci si connette a un dispositivo in modalità di manutenzione. Eseguire questo cmdlet nel controller del dispositivo quando si è connessi tramite la console seriale del dispositivo.** Digitare: 
   
    `Enter-HcsMaintenanceMode`
   
    Di seguito è riportato un output di esempio.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
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
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitorare l'avanzamento dell'installazione con il comando `Get-HcsUpdateStatus` . L'aggiornamento è completo quando `RunInProgress` diventa `False`.
4. Al termine dell'installazione, il controller in cui è stato installato l'aggiornamento rapido in modalità di manutenzione viene riavviato. Accedere all'opzione 1 con accesso completo e verificare la versione del firmware del disco. Digitare: 
   
   `Get-HcsFirmwareVersion`
   
   Le versioni del firmware del disco previste sono:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   Di seguito è riportato un output di esempio.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17820
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Eseguire il comando `Get-HcsFirmwareVersion` sul secondo controller per verificare che la versione del software sia stata aggiornata. È quindi possibile chiudere la modalità di manutenzione. A tale scopo, digitare il comando seguente per ogni controller del dispositivo:
   
   `Exit-HcsMaintenanceMode`

5. I controller si riavviano quando si esce dalla modalità di manutenzione. Dopo la corretta istallazione degli aggiornamenti del firmware del disco e dopo che il dispositivo ha terminato la modalità manutenzione, tornare al portale di Azure classico. Si noti che il portale potrebbe non mostrare che sono stati installati gli aggiornamenti per la modalità di manutenzione prima di 24 ore.

