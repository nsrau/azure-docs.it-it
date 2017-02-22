### <a name="prepare-for-push-install-on-windows-machines"></a>Preparare l'installazione push in computer Windows

1. Verificare che sia presente la connettività di rete tra il computer Windows e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere privilegi di amministratore (locale o di dominio) e viene usato solo per l'installazione push e gli aggiornamenti dell'agente.

   > [!NOTE]
   > Se non si usa un account di dominio, è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. Per disabilitare il Controllo dell'accesso utente remoto in un computer, aggiungere un nuovo valore DWORD **LocalAccountTokenFilterPolicy** nella chiave del Registro di sistema HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System e impostarlo su 1. Il comando per eseguire tale operazione dal prompt dei comandi è questo: **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Aggiungere l'account creato in CSPSConfigtool.
    - Accedere al server di configurazione.
    - Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
    - Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
    - Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.


<!--HONumber=Jan17_HO3-->


