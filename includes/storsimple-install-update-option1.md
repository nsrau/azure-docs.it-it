#### Per installare l'aggiornamento 1 da Windows PowerShell per StorSimple

1. Attenersi alla seguente procedura per scaricare l'aggiornamento del software.

    1. Avviare Internet Explorer e accedere al sito [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Se si visita il sito per la prima volta, verrà richiesto di installare Microsoft Update Catalog. Fare clic su **Installa**.
    
        ![Installare il catalogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Verrà visualizzata una schermata che consente di eseguire ricerche nel catalogo. Immettere **3063418** nella casella di ricerca, quindi fare clic su **Cerca**.

        ![Cercare nel catalogo](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Verrà visualizzato il bundle **StorSimple Update 1.0 Appliance Update**. Fare clic su **Aggiungi**. L'aggiornamento verrà aggiunto al carrello.

        ![Aggiornare il bundle](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Fare clic su **Visualizza carrello**.
 
        ![Visualizzare il carrello](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Fare clic su **Download**. Specificare o selezionare il percorso locale in cui si desidera salvare il file scaricato. L'aggiornamento (all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) verrà scaricato nella cartella del bundle StorSimple Update 1.0 Appliance Update (KB3063418) per il percorso selezionato. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.
        
2. Per installare l'aggiornamento del software, accedere all'interfaccia di Windows PowerShell dalla console seriale del dispositivo StorSimple. Attenersi alle istruzioni riportate in [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-serial-console).

3. Al prompt dei comandi, digitare Invio:

4. Selezionare l'**opzione 1** per eseguire l'accesso completo al dispositivo.

5. Per installare il pacchetto di aggiornamento, al prompt dei comandi, digitare:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Il parametro di credenziale viene utilizzato soltanto se si accede a una condivisione autenticata.

    Di seguito è riportato un output di esempio.

        ````
        Controller0>Start-HcsHotfix -Path \10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Digitare **Y** quando viene richiesto di confermare l'installazione dell'hotfix.

7. Monitorare l'aggiornamento tramite il cmdlet Get-HcsUpdateStatus.

    Il seguente output di esempio indica che l'aggiornamento è in corso.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Il seguente output di esempio indica che l'aggiornamento è stato completato.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. Una volta completato l'aggiornamento del software, accedere alla pagina Manutenzione nel portale di gestione. Verificare la disponibilità di aggiornamenti. Si noterà che sono disponibili più aggiornamenti.

9. Fare clic su **Installa aggiornamenti** per applicare tutti gli aggiornamenti del software disponibili nel portale.

10. Dopo aver installato gli aggiornamenti del software, verificare le versioni del driver, del firmware e del software del sistema. Digitare il seguente comando:

    `Get-HcsSystem`

    Dovrebbero essere visualizzate le seguenti versioni:

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. Per verificare che il firmware sia stato aggiornato correttamente, digitare:

    `Start-HcsFirmwareCheck`

    Lo stato del firmware dovrebbe corrispondere a **Aggiornato**.

<!---HONumber=62-->