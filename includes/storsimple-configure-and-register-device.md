
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

8. (Facoltativo) configurare il server proxy Web. Sebbene la configurazione del proxy Web sia facoltativa, **tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui**. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](https://msdn.microsoft.com/library/azure/dn764937.aspx).
 

      >[AZURE.NOTE]È possibile premere Ctrl + C in qualsiasi momento per uscire dall'installazione guidata. Qualsiasi impostazione applicata prima di emettere questo comando verrà conservata.

9. Per motivi di sicurezza la password di amministratore del dispositivo scade dopo la prima sessione e sarà necessario modificarla per le sessioni successive. Quando richiesto, fornire una password di amministratore del dispositivo. Una password di amministratore dispositivo valida deve avere una lunghezza compresa tra gli 8 e i 15 caratteri. La password deve contenere una combinazione di caratteri minuscoli, caratteri maiuscoli, numeri e caratteri speciali.

10. Anche la password di Gestione snapshot StorSimple viene impostata qui. È possibile utilizzare questa password quando si autentica un dispositivo con l'host Windows che esegue Gestione snapshot StorSimple. Quando richiesto, fornire una password con un numero di caratteri compreso tra 14 e 15. La password deve contenere una combinazione di tre dei seguenti tipi di caratteri: minuscole, maiuscole, numeri e caratteri speciali.

    ![StorSimple registrare il dispositivo 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)

    È possibile reimpostare la password di Gestione Snapshot StorSimple dall'interfaccia del servizio StorSimple Manager.

11. Il passaggio finale dell'installazione guidata registra il dispositivo con il servizio StorSimple Manager. A tale scopo, è necessario il codice di registrazione del servizio ottenuto nel passaggio 2. Dopo aver fornito il codice di registrazione, potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo venga registrato.

12. Dopo la registrazione del dispositivo, verrà visualizzato un codice di crittografia dei dati di servizio. Copiare questo codice e salvarlo in un luogo sicuro. **Il codice verrà richiesto con il codice di registrazione del servizio allo scopo di registrare altri dispositivi con il servizio StorSimple Manager.** Per ulteriori informazioni sul codice, consultare [Sicurezza di StorSimple](../articles/storsimple/storsimple-security.md).

     ![StorSimple registrare il dispositivo 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)

     Per copiare il testo dalla finestra della console seriale, è sufficiente selezionarlo. È quindi possibile incollarlo negli Appunti o in qualsiasi editor di testo. NON utilizzare Ctrl + C per copiare la chiave DEK del servizio. Se si utilizza Ctrl + C l'installazione guidata verrà chiusa. Di conseguenza, la password di amministratore del dispositivo e la password di Gestione snapshot StorSimple non verranno modificate e verranno ripristinate le password predefinite del dispositivo.

13. Uscire dalla console seriale.

14. Tornare al portale di gestione e completare la seguente procedura:
  1. Fare doppio clic sul servizio StorSimple Manager per accedere alla pagina **Avvio rapido**.
  2. Fare clic su **Visualizza dispositivi connessi**.
  3. Nella pagina **Dispositivi**, verificare che il dispositivo sia connesso correttamente al servizio controllando lo stato. Lo stato del dispositivo deve essere **Online**.
   
    ![Pagina Dispositivi StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png)
  
      >[AZURE.NOTE]Se lo stato del dispositivo è **Offline**, attendere qualche minuto che il dispositivo torni in linea.

<!---HONumber=July15_HO2-->