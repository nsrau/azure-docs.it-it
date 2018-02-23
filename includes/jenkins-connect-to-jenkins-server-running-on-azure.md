1. Nel browser passare alla macchina virtuale Jenkins. Poiché il dashboard di Jenkins non è accessibile tramite il protocollo HTTP non protetto, viene visualizzato un messaggio che indica che è necessario usare SSH per effettuare il tunneling nella macchina virtuale.

    ![Jenkins fornisce informazioni che descrivono come usare SSH per connettersi alla macchina virtuale Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Aprire una finestra Bash o del terminale con accesso a SSH.

1. Immettere il comando `ssh` seguente, sostituendo i segnaposto **&lt;username>** e **&lt;domain>** con i valori specificati durante la creazione della macchina virtuale Jenkins:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Seguire i prompt `ssh` per accedere alla macchina virtuale Jenkins.

1. Immettere il comando seguente per recuperare la password iniziale per sbloccare Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Copiare negli Appunti la password visualizzata nella finestra Bash o del terminale.

1. Nel browser passare a `http://localhost:8080`.

1. Incollare la password recuperata prima nel campo **Administrative password** (Password amministrativa) e selezionare **Continue** (Continua).

    ![Jenkins archivia una password iniziale che dovrà essere usata per sbloccare la macchina virtuale Jenkins la prima volta che ci si connette.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Selezionare **Install suggested plugins** (Installa plug-in consigliati).

    ![Jenkins permette di installare facilmente una raccolta di plug-in consigliati al momento dell'accesso iniziale](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Nella pagina **Create First Admin User** (Crea primo utente amministratore) creare l'utente amministratore e la password per il dashboard di Jenkins e quindi selezionare **Save and finish** (Salva e completa).

1. Nella pagina **Jenkins is ready!** (Jenkins è pronto!) selezionare **Start using Jenkins** (Inizia a usare Jenkins).

    ![Quando Jenkins è installato e configurato per l'accesso, è possibile iniziare a usarlo per creare e compilare processi.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)