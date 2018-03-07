### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparare un'installazione push in un computer Windows

1. Verificare che tra il computer Windows e il server di elaborazione sia attiva la connettività di rete.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere diritti di amministratore, locale o di dominio. Usarlo solo per l'installazione push e per gli aggiornamenti dell'agente.

   > [!NOTE]
   > Se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale. Per disabilitare il controllo Accesso utente remoto, nella chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System del Registro di sistema aggiungere un nuovo valore DWORD: **LocalAccountTokenFilterPolicy**. Impostare il valore su **1**. Per eseguire questa operazione da un prompt dei comandi, usare il comando seguente:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
3. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows (WMI)**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

4. Aggiungere l'account creato in CSPSConfigtool. A tale scopo, seguire questa procedura:

    a. Accedere al server di configurazione.

    b. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.

    c. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

    d. Aggiungere l'account che è stato creato.

    e. Immettere le credenziali usate quando si abilita la replica per un computer.
