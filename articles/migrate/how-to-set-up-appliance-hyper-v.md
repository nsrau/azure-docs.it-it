---
title: Configurare un'appliance Azure Migrate per Hyper-V
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione e la migrazione di macchine virtuali Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538291"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurare un'appliance per le VM Hyper-V

Seguire questo articolo per configurare l'appliance Azure Migrate per la valutazione delle VM Hyper-V con lo strumento di [valutazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Il [Azure migrate Appliance](migrate-appliance.md) è un'appliance semplice usata da Azure migrate: valutazione/migrazione del server per individuare le macchine virtuali Hyper-V locali e inviare i dati relativi alle prestazioni e ai metadati delle VM in Azure.

È possibile distribuire l'appliance usando due metodi:

- Configurare in una macchina virtuale Hyper-V usando un disco rigido virtuale scaricato. Questo è il metodo descritto in questo articolo.
- Configurare l'appliance in una macchina virtuale Hyper-Vo in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale con un disco rigido virtuale o se si è in Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Distribuzione Appliance (VHD)

Per configurare l'appliance usando un modello VHD:

- Scaricare un disco rigido virtuale Hyper-V compresso dal portale di Azure.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="download-the-vhd"></a>Scaricare il disco rigido virtuale

Scaricare il modello di disco rigido virtuale compresso per l'appliance.

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. Fare clic su **Scarica** per scaricare i file VHD.

    ![Scaricare la VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il disco rigido virtuale
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Per la versione appliance 2.19.11.12, l'hash generato deve corrispondere a queste [Impostazioni](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security).




## <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare la VM.

1. Estrarre il file VHD compresso in una cartella nell'host Hyper-V che ospiterà l'appliance VM. Vengono estratte tre cartelle.
2. Aprire la console di gestione di Hyper-V. In **Azioni** fare clic su **Importa macchina virtuale**.

    ![Distribuire il disco rigido virtuale](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Nella procedura guidata Importa macchina virtuale selezionare **Prima di iniziare**, quindi fare clic su **Avanti**.
3. In **Individua cartella**specificare la cartella contenente il disco rigido virtuale estratto. Quindi fare clic su **Next**.
1. In **Selezione macchina virtuale** fare clic su **Avanti**.
2. In **Scegliere il tipo di importazione** fare clic su **Copia macchina virtuale (crea nuovo ID univoco)** . Quindi fare clic su **Next**.
3. In **Selezione destinazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
4. In **Cartelle di archiviazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
5. In **Scegli la rete**  specificare il commutatore virtuale che verrà usato dalla VM. È necessario che il commutatore abbia connettività Internet per inviare dati ad Azure.
6. In **Riepilogo** rivedere le impostazioni. Fare clic su **Fine**.
7. Nella console di gestione di Hyper-V selezionare **Macchine virtuali** e avviare la VM.


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta. Se si distribuisce l'appliance usando uno script anziché un disco rigido virtuale, i primi due passaggi della procedura non sono applicabili.

1. Nella console di gestione di Hyper selezionare **Macchine virtuali**, fare clic con il pulsante destro del mouse sulla VM e scegliere **Connetti**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **License** (Licenza): Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
        - Fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di VM, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Install updates** (Installa aggiornamenti): Valutazione server di Azure Migrate verifica che nell'appliance siano installati gli aggiornamenti più recenti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Fare clic su **Log in** (Accedi). Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
2. Nella nuova scheda accedere con le credenziali di Azure.
    - Accedere con il nome utente e la password.
    - L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web.
4. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate. Quindi selezionare il progetto.
5. Specificare un nome per l'appliance. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
6. Fare clic su **Register**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegare le credenziali per i dischi rigidi virtuali SMB

Se i dischi rigidi virtuali sono in esecuzione in SMB, è necessario abilitare la delega delle credenziali dall'appliance agli host Hyper-V. Per eseguire questa operazione dal dispositivo:

1. Nell'appliance VM eseguire questo comando. HyperVHost1/HyperVHost2 sono nomi host di esempio.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. In alternativa, eseguire questa operazione nell'editor Criteri di gruppo locali dell'appliance:
    - In **Criteri del computer locale** > **Configurazione computer** fare clic su **Modelli amministrativi** > **Sistema** > **Delega di credenziali**.
    - Fare doppio clic su **Consenti delega credenziali nuove** e selezionare **Abilitata**.
    - In **Opzioni** fare clic su **Mostra** e aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.
    - In **Delega di credenziali** fare doppio clic su **Consenti delega credenziali nuove solo con autenticazione server NTLM**. Anche in questo caso, aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dall'appliance agli host o cluster Hyper-V e avviare l'individuazione delle macchine virtuali.

1. In **Nome utente** e **Password** specificare le credenziali dell'account che verranno usate dall'appliance per individuare le macchine virtuali. Specificare un nome descrittivo per le credenziali e fare clic su **Salva dettagli**.
2. Fare clic su **Aggiungi host** e specificare i dettagli degli host o cluster Hyper-V.
3. Fare clic su **Convalida**. Dopo la convalida, viene visualizzato il numero di VM che è possibile individuare in ogni host o cluster.
    - Se la convalida non riesce per un host, esaminare l'errore passando il puntatore del mouse sull'icona nella colonna **Stato**. Risolvere i problemi e ripetere la convalida.
    - Per rimuovere host o cluster, selezionare **Elimina**.
    - Non è possibile rimuovere un host specifico da un cluster. È possibile rimuovere solo l'intero cluster.
    - È possibile aggiungere un cluster, anche in caso di problemi con host specifici al suo interno.
4. Dopo la convalida fare clic su **Salva e avvia individuazione** per avviare il processo di individuazione.

Viene avviata l'individuazione. Per visualizzare i metadati delle VM individuate nel portale di Azure, sono necessari circa 15 minuti.

## <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Dopo l'individuazione, è possibile verificare se le VM vengono visualizzate nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.


## <a name="next-steps"></a>Passaggi successivi

Prova [Hyper-V Assessment](tutorial-assess-hyper-v.md) con Azure migrate server assessment.
