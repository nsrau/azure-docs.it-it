<!--author=SharS last changed: 9/17/15-->

#### Per installare l'aggiornamento 1.2 da Windows PowerShell per StorSimple

1. Attenersi alla seguente procedura per scaricare l'aggiornamento del software.

    1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Se si visita il sito per la prima volta, verrà richiesto di installare Microsoft Update Catalog. Fare clic su **Installa**.
    
        ![Installare il catalogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Verrà visualizzata una schermata che consente di eseguire ricerche nel catalogo. Immettere **3063418** nella casella di ricerca, quindi fare clic su **Cerca**.

        ![Cercare nel catalogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Verrà visualizzato il bundle **StorSimple Update 1.2 Appliance Update**. Fare clic su **Aggiungi**. L'aggiornamento verrà aggiunto al carrello.

        ![Aggiornare il bundle](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Fare clic su **Visualizza carrello**.
 
        ![Visualizzare il carrello](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Fare clic su **Download**. Specificare o **sfogliare** il percorso locale in cui si desidera salvare il file scaricato. L'aggiornamento verrà scaricato nella cartella del **bundle StorSimple Update 1.2 Appliance Update** (KB3063418) per il percorso selezionato. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.
    
	Questa procedura descrive come installare l'aggiornamento del dispositivo software come aggiornamento rapido, gli aggiornamenti del firmware del disco dal server di Microsoft Update, e il driver LSI e gli aggiornamenti di Windows dal portale di gestione. È tuttavia possibile scegliere di installare aggiornamenti firmware del software, del driver e del disco come gli aggiornamenti rapidi. Sarà quindi necessario scaricare l'aggiornamento di StorSimple 1.2 SAS Controller (KB3043005) e aggiornamento StorSimple 1.2 Disk Firmware (KB3063416) e copiarli nella stessa cartella condivisa. Per installare aggiornamenti firmware del disco come aggiornamenti rapidi, seguire le istruzioni in [installazione degli aggiornamenti rapidi in modalità di manutenzione tramite Windows PowerShell per StorSimple](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple).
    
	> [AZURE.NOTE]L'aggiornamento rapido deve essere accessibile da entrambi i controller per rilevare eventuali messaggi di errore potenziali dal controller peer.
            
2. Per installare l'aggiornamento del software, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

3. Al prompt dei comandi, premere **Invio**.

4. Selezionare l'**opzione 1** per eseguire l'accesso completo al dispositivo.

5. Per installare il pacchetto di aggiornamento, al prompt dei comandi, digitare:

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
		

	> [AZURE.NOTE]In alcuni casi, i cmdlet mostrano`False` quando l'aggiornamento è ancora in corso. Per assicurarsi che l'aggiornamento rapido è stato completato, attendere alcuni minuti, eseguire nuovamente il comando e verificare che `RunInProgress` sia `False`. In caso affermativo, l'aggiornamento rapido è stato completato.
	
8. Dopo aver installato gli aggiornamenti del software, verificare le versioni del software del sistema. Digitare il seguente comando:

    `Get-HcsSystem`

    Dovrebbero essere visualizzate le seguenti versioni:

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433 
    
	Se i numeri di versione non vengono modificati dopo aver applicato l'aggiornamento, significa che non è stato possibile applicare l'aggiornamento rapido. Se si verifica ciò, contattare [il supporto tecnico di Microsoft](storsimple-contact-microsoft-support.md) per assistenza.
    
9. A questo punto si installeranno gli aggiornamenti del firmware del disco che sono problematici e che richiedono circa 30-45 minuti. È possibile scegliere di installare tali aggiornamenti in una finestra di manutenzione pianificata tramite la connessione alla console seriale del dispositivo. Per istallare gli aggiornamenti del firmware del disco, seguire le istruzioni in [Installare gli aggiornamenti in modalità manutenzione tramite Windows PowerShell per StorSimple](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

10. Dopo la corretta istallazione degli aggiornamenti del firmware del disco e dopo che il dispositivo ha terminato la modalità manutenzione, tornare al portale di gestione. Gli aggiornamenti in modalità manutenzione non vengono aggiornati nel portale fino a quando non sono trascorse 24 ore. Potrebbe essere necessario attendere prima di applicare gli aggiornamenti non problematici rimanenti dal portale di gestione.

11. Quando si è pronti ad applicare gli aggiornamenti, passare alla pagina **Manutenzione** e nella parte inferiore della pagina, fare clic su **Analisi aggiornamenti**. Si riceverà una notifica che indica che sono disponibili aggiornamenti, tra cui quelli del driver e di Windows. Fare clic su **Installa aggiornamenti **per avviare il processo di installazione. Attendere che tutti gli aggiornamenti vengano installati correttamente.





 
 

<!---HONumber=Sept15_HO4-->