<!--author=SharS last changed: 02/22/2016-->

### <a name="to-configure-and-register-the-device"></a>Per configurare e registrare il dispositivo
1. Accedere all'interfaccia di Windows PowerShell sulla console seriale del dispositivo StorSimple. Per istruzioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) . **Assicurarsi di seguire la procedura esattamente o non si sarà in grado di accedere alla console.**
2. Nella sessione che viene aperta, premere INVIO una volta per visualizzare un prompt dei comandi.
3. Verrà richiesto di scegliere la lingua che si desidera impostare per il dispositivo. Specificare la lingua, quindi premere INVIO.
   
    ![StorSimple configurare e registrare il dispositivo 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. Nel menu della console seriale che viene visualizzato, scegliere l'opzione 1 per eseguire l’accesso completo.
   
    ![StorSimple registrare il dispositivo 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Completare i passaggi seguenti per configurare le impostazioni di rete minime richieste per il dispositivo.
   
   > [!IMPORTANT]
   > È necessario eseguire questa procedura sul controller attivo del dispositivo. Nel messaggio dell’intestazione del menu della console seriale è indicato lo stato del controller. Se non si è connessi al controller attivo, disconnettersi e quindi connettersi al controller attivo.
   > 
   > 
   
   1. Al prompt dei comandi, digitare la password. La password predefinita è **Password1**.
   2. Digitare il seguente comando:
      
        `Invoke-HcsSetupWizard`
   3. Verrà visualizzata una procedura guidata per configurare le impostazioni di rete per il dispositivo. Fornire le informazioni seguenti:
      
      * Indirizzo IP per l'interfaccia di rete DATA 0
      * Subnet mask
      * Gateway
      * Indirizzo IP per il server DNS primario
      * Indirizzo IP per il server NTP primario
      
      > [!NOTE]
      > Potrebbe essere necessario attendere alcuni minuti affinché la subnet mask e le impostazioni DNS vengano applicate.
      > 
      > 
   4. Facoltativamente, configurare il server proxy Web.
      
      > [!IMPORTANT]
      > Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md).
      > 
      > 
6. Premere Ctrl + C per uscire dalla configurazione guidata.
7. Installare gli aggiornamenti come segue:
   
   1. Usare il cmdlet seguente per impostare gli indirizzi IP in entrambi i controller:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. Al prompt dei comandi eseguire `Get-HcsUpdateAvailability`. L’utente dovrebbe ricevere una notifica che sono disponibili aggiornamenti.
   3. Eseguire `Start-HcsUpdate`. È possibile eseguire questo comando su qualsiasi nodo. Gli aggiornamenti verranno applicati nel primo controller, il controller eseguirà il failover e quindi gli aggiornamenti verranno applicati nell’altro controller.
      
      Per controllare l'avanzamento dell’aggiornamento, eseguire `Get-HcsUpdateStatus`.    
      
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
      
      ```
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ```
      
      Per applicare tutti gli aggiornamenti, inclusi gli aggiornamenti di Windows, potrebbero essere necessarie fino a 11 ore.
8. Eseguire il cmdlet seguente per scegliere il dispositivo nel portale di Microsoft Azure per enti pubblici (per impostazione predefinita viene scelto il portale di Azure classico pubblico). Entrambi i controller verranno riavviati. È consigliabile usare due sessioni PuTTY per connettersi contemporaneamente a entrambi i controller, in modo da visualizzare quando ciascun controller viene riavviato.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Verrà visualizzato un messaggio di conferma. Accettare il valore predefinito (**Y**).
9. Eseguire il cmdlet seguente per riprendere la configurazione:
   
    `Invoke-HcsSetupWizard`
   
    ![Riprendere la configurazione guidata](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
   Quando si riprende la configurazione, la versione della procedura guidata sarà Aggiornamento 2.
10. Accettare le impostazioni di rete. Dopo aver accettato ciascuna impostazione, verrà visualizzato un messaggio di convalida.
11. Per motivi di sicurezza la password di amministratore del dispositivo scade dopo la prima sessione e sarà necessario modificarla ora. Quando richiesto, fornire una password di amministratore del dispositivo. Una password di amministratore dispositivo valida deve avere una lunghezza compresa tra gli 8 e i 15 caratteri. La password deve contenere tre dei seguenti tipi di caratteri: minuscole, maiuscole, numeri e caratteri speciali.
    
    <br/>![StorSimple registrare il dispositivo 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Il passaggio finale dell'installazione guidata registra il dispositivo con il servizio StorSimple Manager. A tale scopo, è necessario il codice di registrazione del servizio ottenuto in [Passaggio 2: Ottenere la chiave di registrazione del servizio](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Dopo aver fornito il codice di registrazione, potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo venga registrato.
    
    > [!NOTE]
    > È possibile premere Ctrl + C in qualsiasi momento per uscire dall'installazione guidata. Se sono state immesse tutte le impostazioni di rete (indirizzo IP per Data 0, Subnet mask e Gateway), le voci verranno conservate.
    > 
    > 
    
    ![Registrazione di StorSimple in corso](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Dopo la registrazione del dispositivo, verrà visualizzato un codice di crittografia dei dati di servizio. Copiare questo codice e salvarlo in un luogo sicuro. **Il codice verrà richiesto con il codice di registrazione del servizio allo scopo di registrare altri dispositivi con il servizio StorSimple Manager.** Per ulteriori informazioni sul codice, consultare [Sicurezza di StorSimple](../articles/storsimple/storsimple-security.md) .
    
    ![StorSimple registrare il dispositivo 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Per copiare il testo dalla finestra della console seriale, è sufficiente selezionarlo. È quindi possibile incollarlo negli Appunti o in qualsiasi editor di testo.
    > 
    > NON usare CTRL+C per copiare la chiave DEK del servizio. Se si usa CTRL+C l'installazione guidata verrà chiusa. Di conseguenza, la password di amministratore del dispositivo non verrà modificata e verrà ripristinata la password predefinita del dispositivo.
    > 
    > 
14. Uscire dalla console seriale.
15. Tornare al portale per enti pubblici di Azure e completare la seguente procedura:
    
    1. Fare doppio clic sul servizio StorSimple Manager per accedere alla pagina **Avvio rapido** .
    2. Fare clic su **Visualizza dispositivi connessi**.
    3. Nella pagina **Dispositivi** , verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Online**.
       
        ![Pagina Dispositivi StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png)
       
        Se lo stato del dispositivo è **Offline**, attendere qualche minuto che il dispositivo torni in linea.
       
        Se il dispositivo è ancora offline dopo alcuni minuti, è necessario assicurarsi che la rete firewall sia stata configurata come descritto nei [requisiti di rete per il dispositivo StorSimple](../articles/storsimple/storsimple-system-requirements.md).
       
        Verificare che la porta 9354 sia aperta per le comunicazioni in uscita, perché viene usata dal bus di servizio per le comunicazioni da servizio a dispositivo di StorSimple Manager.



<!--HONumber=Nov16_HO3-->


