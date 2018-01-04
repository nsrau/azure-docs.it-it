1. Nel browser, passare alla macchina virtuale Jenkins. Poiché il dashboard di Jenkins è accessibile tramite il protocollo HTTP non protetta, viene visualizzato un messaggio che indica che è necessario utilizzare tunnel SSH nella macchina virtuale.

    ![Jenkins fornisce informazioni che descrivono come usare SSH per la connessione alla macchina virtuale Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Aprire una finestra bash o terminal, con accesso a SSH.

1. Immettere le informazioni seguenti `ssh` comando, sostituendo il  **&lt;nome utente >** e  **&lt;dominio >** segnaposto con i valori specificati durante la creazione di Jenkins macchina virtuale:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Seguire il `ssh` chiede di accedere alla macchina virtuale di Jenkins.

1. Immettere il comando seguente per recuperare la password iniziale per lo sblocco Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Copiare negli Appunti la password che viene visualizzata nella finestra bash o terminal.

1. Nel browser passare a `http://localhost:8080`.

1. Incollare la password recuperata in precedenza nel **password amministrativa** campo e selezionare **continua**.

    ![Jenkins archivia una password iniziale che è necessario utilizzare per sbloccare la macchina virtuale di Jenkins la prima volta, che è possibile connettersi.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Selezionare **installare suggeriti i plug-in**.

    ![Jenkins consente di installare facilmente una raccolta di suggeriti plug-in al momento dell'accesso iniziale](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Nel **creare primo utente Admin** pagina, creare l'utente amministrativo e una password per il dashboard di Jenkins e selezionare **salvare e di fine**.

1. Nel **Jenkins è pronto.** Selezionare **iniziare a usare Jenkins**.

    ![Una volta Jenkins è installato e configurato per l'accesso, è possibile iniziare a utilizzarlo per creare e compilare i processi.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)