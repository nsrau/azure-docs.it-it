1. Avvio di UnifiedSetup.exe di Azure Site Recovery
2. In **Prima di iniziare** selezionare **Add additional process servers to scale out deployment** (Aggiungere server di elaborazione per aumentare le istanze di distribuzione).

  ![Aggiungere il server di elaborazione](./media/site-recovery-add-process-server/ps-page-1.png)

3. In **Configuration Server Details** (Dettagli del server di configurazione) specificare l'indirizzo IP del server di configurazione e la passphrase.

  ![Aggiungere il server di elaborazione 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. In **Impostazioni Internet** specificare in che modo il provider in esecuzione nel server di configurazione si connette ad Azure Site Recovery tramite Internet.

  ![Aggiungere il server di elaborazione 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Per connettersi al proxy attualmente configurato nel computer, selezionare **Connetti con le impostazioni proxy esistenti**.
   * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente senza un proxy**.
   * Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connect with custom proxy settings** (Connetti con le impostazioni proxy personalizzate).

     * Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
     * Se si usa un proxy, è necessario che l'accesso agli URL del servizio sia già consentito.

5. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo per assicurarsi che sia possibile eseguire l'installazione. Se viene visualizzato un avviso relativo al **Global time sync check** (Controllo della sincronizzazione ora globale), verificare che l'ora del clock di sistema, nelle impostazioni di **Data e ora**, corrisponda al fuso orario.

     ![Aggiungere il server di elaborazione 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. In **Dettagli ambiente** specificare se si vuole eseguire la replica di VM VMware. In caso affermativo, il programma di installazione verifica quindi se è installato PowerCLI 6.0.

     ![Aggiungere il server di elaborazione 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. L'unità selezionata deve avere almeno 5 GB di spazio su disco disponibile, ma è consigliabile usare un'unità cache con almeno 600 GB di spazio disponibile.
     ![Aggiungere il server di elaborazione 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. In **Selezione rete** specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server di configurazione deve inviare e ricevere i dati di replica. La porta 9443 è la porta predefinita per l'invio e la ricezione del traffico di replica, ma è possibile modificare il numero di porta in base ai requisiti dell'ambiente. Oltre alla porta 9443, viene aperta anche la porta 443, che viene usata da un server Web per orchestrare le operazioni di replica. Non usare la porta 443 per inviare o ricevere traffico di replica.

     ![Aggiungere il server di elaborazione 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. Esaminare le informazioni nella pagina **Riepilogo** e fare clic su **Installa**. Al termine dell'installazione verrà generata una passphrase. Sarà necessaria quando si abilita la replica, è quindi consigliabile copiarla e conservarla in un luogo sicuro.

     ![Aggiungere il server di elaborazione 7](./media/site-recovery-add-process-server/ps-page-8.png)
