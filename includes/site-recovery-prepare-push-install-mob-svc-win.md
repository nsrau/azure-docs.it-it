### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparare un'installazione push in un computer Windows

1. Verificare che sia presente la connettività di rete tra il computer Windows e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere diritti di amministratore (locale o di dominio). Usare questo account solo per l'installazione push e per gli aggiornamenti dell'agente.

   > [!NOTE]
   > Se non si usa un account di dominio, disabilitare il Controllo dell'accesso utente remoto nel computer locale. Per disabilitare il Controllo dell'accesso utente remoto, nella chiave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System del Registro di sistema aggiungere un nuovo valore DWORD: **LocalAccountTokenFilterPolicy**. Impostare il valore su **1**. Per eseguire questa operazione in un prompt dei comandi, eseguire il comando seguente:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows (WMI)**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Aggiungere l'account creato in CSPSConfigtool.
    1.  Accedere al server di configurazione.
    2.  Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
    3.  Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    4.  Aggiungere l'account che è stato creato.
    5.  Immettere le credenziali usate quando si abilita la replica per un computer.
