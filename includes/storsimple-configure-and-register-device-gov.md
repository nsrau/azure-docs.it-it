<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Per configurare e registrare il dispositivo
1. Accedere all'interfaccia di Windows PowerShell sulla console seriale del dispositivo StorSimple. Per istruzioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console) . **Assicurarsi di seguire la procedura esattamente o non si sarà in grado di accedere alla console.**
2. Nella sessione che viene aperta, premere INVIO una volta per visualizzare un prompt dei comandi. 
3. Verrà richiesto di scegliere la lingua che si desidera impostare per il dispositivo. Specificare la lingua, quindi premere INVIO. 
   
    ![StorSimple configurare e registrare il dispositivo 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. Nel menu della console seriale che viene visualizzato, scegliere l'opzione 1 per eseguire l’accesso completo. 
   
    ![StorSimple registrare il dispositivo 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
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
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


