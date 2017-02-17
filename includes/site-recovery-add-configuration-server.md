1. Eseguire il file di installazione per l'Installazione unificata.
2. In **Prima di iniziare** selezionare l'opzione **Installare il server di configurazione e il server di elaborazione**.
    ![Prima di iniziare](./media/site-recovery-add-configuration-server/combined-wiz1.png)
3. In **Licenza software di terze parti** fare clic su **Accetto** per scaricare e installare MySQL.

    ![Software di terze parti](./media/site-recovery-add-configuration-server/combined-wiz105.PNG)
4. In **Registrazione** selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

    ![Registrazione](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet.

   * Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti con le impostazioni proxy esistenti**.
   * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente senza un proxy**.
   * Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connetti con le impostazioni proxy personalizzate**.

     * Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
     * Se si usa un proxy, è necessario che gli URL descritti nei [prerequisiti](#configuration-server-prerequisites) siano già consentiti.

     ![Firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

    ![Prerequisiti](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. In **MySQL Configuration** (Configurazione MySQL) creare le credenziali per l'accesso all'istanza del server MySQL che viene installata.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. In caso affermativo, il programma di installazione verifica quindi se è installato PowerCLI 6.0.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)
9. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. È possibile selezionare un'unità con almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.

    ![Percorso di installazione](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. In **Network Selection** (Selezione rete) specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione deve inviare e ricevere i dati di replica. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Oltre alla porta 9443, viene aperta anche la porta 443, che viene usata da un server Web per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

    ![Selezione rete](./media/site-recovery-add-configuration-server/combined-wiz9.png)

 11. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro.

    ![Riepilogo](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Al termine della registrazione il server viene visualizzato nel pannello **Impostazioni** > **Server** nell'insieme di credenziali.


<!--HONumber=Feb17_HO2-->


