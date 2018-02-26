1. Nel browser aprire l'[immagine di Azure Marketplace per Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Selezionare **SCARICA ADESSO**.

    ![Selezionare SCARICA ADESSO per avviare il processo di installazione per l'immagine del Marketplace per Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Dopo aver esaminato le informazioni sui prezzi e sulle condizioni, selezionare **Continua**.

    ![Informazioni su prezzi e condizioni dell'immagine del Marketplace per Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Selezionare **Crea** per configurare il server Jenkins nel portale di Azure. 

    ![Installare l'immagine del Marketplace per Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Nella scheda **Informazioni di base** specificare i valori seguenti:

    - **Nome**: immettere `Jenkins`.
    - **Utente**: immettere il nome utente da usare per l'accesso alla macchina virtuale su cui è in esecuzione Jenkins.
    - **Tipo di autenticazione**: selezionare **Password**.
    - **Password**: immettere la password da usare per l'accesso alla macchina virtuale su cui è in esecuzione Jenkins.
    - **Conferma password**: immettere di nuovo la password da usare per l'accesso alla macchina virtuale su cui è in esecuzione Jenkins.
    - **Jenkins release type** (Tipo di versione Jenkins): selezionare **LTS**.
    - **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui si vuole installare Jenkins.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse che funga da contenitore logico per la raccolta di risorse che costituiscono l'installazione di Jenkins.
    - **Località**: selezionare **Stati Uniti orientali**.

    ![Immettere le informazioni di autenticazione e sul gruppo di risorse per Jenkins nella scheda Informazioni di base.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Selezionare **OK** per passare alla scheda **Impostazioni**. 

1. Nella scheda **Impostazioni** specificare i valori seguenti:

    - **Dimensioni**: selezionare l'opzione appropriata per le dimensioni per la macchina virtuale Jenkins.
    - **Tipo di disco della macchina virtuale**: specificare HDD (unità disco rigido) o SSD (unità SSD) per indicare il tipo di disco di archiviazione consentito per la macchina virtuale Jenkins.
    - **Indirizzo IP pubblico**: per impostazione predefinita, per il nome dell'indirizzo IP viene usato il nome di Jenkins specificato nella pagina precedente, preceduto dal suffisso -IP. È possibile selezionare l'opzione per modificare il valore predefinito.
    - **Etichetta del nome di dominio**: specificare il valore per l'URL completo della macchina virtuale Jenkins.

    ![Immettere le impostazioni della macchina virtuale per Jenkins nella scheda Impostazioni.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Selezionare **OK** per passare alla scheda **Riepilogo**.

1. Quando viene visualizzata la scheda **Riepilogo**, vengono convalidate le informazioni immesse. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **OK**. 

    ![La scheda Riepilogo visualizza e convalida le opzioni selezionate.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Quando viene visualizzata la scheda **Crea**, selezionare **Crea** per creare la macchina virtuale Jenkins. Quando il server è pronto, viene visualizzata una notifica nel portale di Azure.

    ![Notifica che indica che Jenkins è pronto.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)