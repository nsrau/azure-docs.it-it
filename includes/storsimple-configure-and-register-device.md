<!--author=alkohli last changed: 12/01/15-->


#### Per configurare e registrare il dispositivo

1. Accedere all'interfaccia di Windows PowerShell sulla console seriale del dispositivo StorSimple. Per istruzioni, vedere [Utilizzare PuTTY per connettersi alla console seriale del dispositivo](#use-putty-to-connect-to-the-device-serial-console). **Assicurarsi di seguire la procedura esattamente o non si sarà in grado di accedere alla console.**

2. Nella sessione che viene aperta, premere INVIO una volta per visualizzare un prompt dei comandi.

3. Verrà richiesto di scegliere la lingua che si desidera impostare per il dispositivo. Specificare la lingua, quindi premere INVIO.

    ![StorSimple configurare e registrare il dispositivo 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)

4. Nel menu della console seriale che viene visualizzato, scegliere l'opzione 1 per eseguire l’accesso completo.

    ![StorSimple registrare il dispositivo 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
  
     Completare i passaggi da 5 a 12 per configurare le impostazioni di rete minime richieste per il dispositivo. **È necessario eseguire questa procedura sul controller attivo del dispositivo.** Nel messaggio dell’intestazione del menu della console seriale è indicato lo stato del controller. Se non si è connessi al controller attivo, disconnettersi e quindi connettersi al controller attivo.

5. Al prompt dei comandi, digitare la password. La password predefinita è **Password1**.

6. Digitare il seguente comando:

     `Invoke-HcsSetupWizard`

7. Verrà visualizzata una procedura guidata per configurare le impostazioni di rete per il dispositivo. Fornire le informazioni seguenti:
   - Indirizzo IP per l'interfaccia di rete DATA 0
   - Subnet mask
   - Gateway
   - Indirizzo IP per il server DNS primario
   - Indirizzo IP per il server NTP primario
   
      >[AZURE.NOTE]Potrebbe essere necessario attendere alcuni minuti affinché la subnet mask e le impostazioni DNS vengano applicate. Se viene visualizzato un messaggio di errore di dispositivo non pronto, controllare la connessione di rete fisica nell'interfaccia di rete DATA 0 del controller attivo.

8. (Facoltativo) configurare il server proxy Web. Sebbene la configurazione del proxy Web sia facoltativa, **tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui**. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). Se si verificano problemi durante questo passaggio, fare riferimento alle linee guida per la risoluzione dei problemi di [Errori durante la configurazione del proxy web](storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).
 

      >[AZURE.NOTE]È possibile premere Ctrl + C in qualsiasi momento per uscire dall'installazione guidata. Qualsiasi impostazione applicata prima di emettere questo comando verrà conservata.

9. Per motivi di sicurezza la password di amministratore del dispositivo scade dopo la prima sessione e sarà necessario modificarla per le sessioni successive. Quando richiesto, fornire una password di amministratore del dispositivo. Una password di amministratore dispositivo valida deve avere una lunghezza compresa tra gli 8 e i 15 caratteri. La password deve contenere una combinazione di caratteri minuscoli, caratteri maiuscoli, numeri e caratteri speciali.

10. Anche la password di Gestione snapshot StorSimple viene impostata qui. È possibile utilizzare questa password quando si autentica un dispositivo con l'host Windows che esegue Gestione snapshot StorSimple. Quando richiesto, fornire una password con un numero di caratteri compreso tra 14 e 15. La password deve contenere una combinazione di tre dei seguenti tipi di caratteri: minuscole, maiuscole, numeri e caratteri speciali.

    ![StorSimple registrare il dispositivo 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)

    È possibile reimpostare la password di Gestione Snapshot StorSimple dall'interfaccia del servizio StorSimple Manager. Per passaggi dettagliati, vedere[Modificare le password StorSimple utilizzando il servizio Gestione StorSimple](storsimple-change-passwords.md).

	Se si verificano problemi durante questo passaggio, fare riferimento alle linee guida per la risoluzione dei problemi di[Errori relativi alle password](storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).

11. Il passaggio finale dell'installazione guidata registra il dispositivo con il servizio StorSimple Manager. A tale scopo, è necessario il codice di registrazione del servizio ottenuto nel passaggio 2. Dopo aver fornito il codice di registrazione, potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo venga registrato.

	Per risolvere gli eventuali errori di registrazione dispositivo possibili, fare riferimento a[Errori durante la registrazione del dispositivo](storsimple-troubleshoot-deployment.md#errors-during-device-registration). Per la risoluzione dei problemi dettagliata, è inoltre possibile fare riferimento a[Esempio dettagliato di risoluzione dei problemi](storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).

12. Dopo la registrazione del dispositivo, verrà visualizzato un codice di crittografia dei dati di servizio. Copiare questo codice e salvarlo in un luogo sicuro.
	
	> [AZURE.WARNING]Il codice verrà richiesto con il codice di registrazione del servizio allo scopo di registrare altri dispositivi con il servizio StorSimple Manager. Per ulteriori informazioni sul codice, consultare [Sicurezza di StorSimple](../articles/storsimple/storsimple-security.md).

     ![StorSimple registrare il dispositivo 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)

     Per copiare il testo dalla finestra della console seriale, è sufficiente selezionarlo. È quindi possibile incollarlo negli Appunti o in qualsiasi editor di testo. NON usare CTRL+C per copiare la chiave DEK del servizio. Se si utilizza Ctrl + C l'installazione guidata verrà chiusa. Di conseguenza, la password di amministratore del dispositivo e la password di Gestione snapshot StorSimple non verranno modificate e verranno ripristinate le password predefinite del dispositivo.

13. Uscire dalla console seriale.

14. Tornare al portale di Azure classico e completare la procedura seguente:
  1. Fare doppio clic sul servizio StorSimple Manager per accedere alla pagina **Avvio rapido**.
  2. Fare clic su **Visualizza dispositivi connessi**.
  3. Nella pagina **Dispositivi**, verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Online**. Se lo stato del dispositivo è **Offline**, attendere qualche minuto che il dispositivo torni in linea.
   
    ![Pagina Dispositivi StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png)
  
      >[AZURE.IMPORTANT]Quando il dispositivo è online, collegare i cavi di rete che sono stati scollegati all'inizio di questo passaggio.

Dopo che il dispositivo è registrato correttamente ma non è in linea, è possibile eseguire `Test-HcsmConnection -Verbose`per garantire che la connettività di rete sia integra. Per informazioni dettagliate sull'utilizzo di questo cmdlet, vedere[Riferimenti cmdlet per Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Video disponibile](./media/storsimple-configure-and-register-device/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple, fare clic [qui](http://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

<!---HONumber=AcomDC_1203_2015-->