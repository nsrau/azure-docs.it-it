## <a name="download-install-and-register-the-azure-backup-agent"></a>Scaricare, installare e registrare l'agente backup di Azure
Dopo aver creato l'insieme di credenziali di Servizi di ripristino, installare l'agente di Backup in ogni computer Windows (Windows Server, client Windows, server System Center Data Protection Manager o computer server di Backup di Azure) usato per il backup dei dati in Azure.

1. Aprire la sottoscrizione nel [portale di Azure](https://ms.portal.azure.com/).
2. Nel menu a sinistra selezionare **Tutti i servizi** e digitare **Servizi di ripristino** nell'elenco dei servizi. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![aprire l'insieme di credenziali dei servizi di ripristino](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. Nella pagina Avvio rapido fare clic sull'opzione **Per Windows Server, System Center Data Protection Manager o client Windows** in **Scarica agente**. Fare clic su **Salva** per copiarlo nel computer locale.
   
    ![Salva agente](./media/backup-install-agent/agent.png)
4. Dopo aver installato l'agente, fare doppio clic su MARSAgentInstaller.exe per avviare l'installazione dell'agente Backup di Azure. Scegliere la cartella di installazione e la cartella Scratch necessarie per l'agente. La posizione specificata per la cache deve includere almeno al 5% dei dati di backup come spazio disponibile.
5. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.
6. Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non è già disponibile).
7. Dopo aver installato l'agente, fare clic sul pulsante **Procedi alla registrazione** per continuare con il flusso di lavoro.
   
   ![Register ](./media/backup-install-agent/register.png)
8. Nella schermata di credenziali dell'insieme di credenziali selezionare il file di credenziali dell'insieme di credenziali scaricato.
   
    ![Credenziali di insieme](./media/backup-install-agent/vc.png)
   
    Il file delle credenziali di insieme è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.
   
    Assicurarsi che l'applicazione di installazione possa accedere al file di credenziali dell'insieme di credenziali. Se si verificano errori relativi all'accesso, copiare il file di credenziali dell'insieme di credenziali in un percorso temporaneo nel computer locale e ripetere l'operazione.
   
    Se si verifica un errore di credenziali dell'insieme di credenziali non valido, ad esempio "È stato specificato un insieme di credenziali non valido", il file di credenziali dell'insieme di credenziali è danneggiato o non include le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore in genere si verifica quando l'utente fa clic sull'opzione **Scarica credenziali dell'insieme di credenziali** nel portale di Azure in rapida successione. In questo caso è valido solo il secondo file di credenziali dell'insieme di credenziali.
9. Nella schermata **impostazione crittografia** , è possibile generare una passphrase o fornire una passphrase (almeno 16 caratteri). Ricordarsi di salvare la passphrase in un luogo sicuro.
   
    ![Crittografia](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.
   > 
   > 
10. Dopo aver selezionato il pulsante **Fine** , il computer sarà registrato nell'insieme di credenziali e sarà possibile avviare il backup in Microsoft Azure.
11. È possibile modificare le impostazioni specificate durante la registrazione del flusso di lavoro facendo clic sull'opzione **Modifica proprietà** nello snap-in di MMC di Backup di Microsoft Azure.
    
    ![Modifica proprietà](./media/backup-install-agent/change.png)
    
    In alternativa, quando si usa Data Protection Manager, è possibile modificare le impostazioni specificate durante il flusso di lavoro di registrazione facendo clic sull'opzione **Configura** selezionando **Online** nella scheda **Gestione**.
    
    ![Configurare il Backup di Azure](./media/backup-install-agent/configure.png)

