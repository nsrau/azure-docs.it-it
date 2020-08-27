---
title: Configurare un'appliance Azure Migrate per Hyper-V
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione e la migrazione di macchine virtuali Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 21d88c4a2b2095fe677fe479bd7320f7a494db9e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929933"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configurare un'appliance per le VM Hyper-V

Seguire questo articolo per configurare l'appliance Azure Migrate per la valutazione delle VM Hyper-V con lo strumento di [valutazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Il [Azure migrate Appliance](migrate-appliance.md)  è un'appliance semplice usata da Azure migrate: valutazione/migrazione del server per individuare le macchine virtuali Hyper-V locali e inviare i dati relativi alle prestazioni e ai metadati delle VM in Azure.

È possibile distribuire l'appliance usando due metodi:

- Configurare in una macchina virtuale Hyper-V usando un disco rigido virtuale scaricato. Questo è il metodo descritto in questo articolo.
- Configurare l'appliance in una macchina virtuale Hyper-Vo in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale con un disco rigido virtuale o se si è in Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Distribuzione Appliance (VHD)

Per configurare l'appliance usando un modello VHD:

- Specificare un nome per il dispositivo e generare una chiave del progetto Azure Migrate nel portale.
- Scaricare un disco rigido virtuale Hyper-V compresso dal portale di Azure.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare il dispositivo per la prima volta e registrarlo con il progetto Azure Migrate usando la chiave del progetto Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Genera la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **1: generare Azure migrate chiave del progetto**, specificare un nome per il dispositivo Azure migrate che verrà configurato per l'individuazione delle macchine virtuali Hyper-V. il nome deve essere alfanumerico con un massimo di 14 caratteri.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Discover machines durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto Azure migrate** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la relativa configurazione.

### <a name="download-the-vhd"></a>Scaricare il disco rigido virtuale

In **2: scaricare Azure migrate Appliance**, selezionare il. File VHD e fare clic su **download**. 

   ![Selezioni per le macchine virtuali di individuazione](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selezioni per genera chiave](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il disco rigido virtuale
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  Per la versione appliance 2.19.11.12, l'hash generato deve corrispondere a queste [Impostazioni](./tutorial-assess-hyper-v.md#verify-security).




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

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script PowerShell](deploy-appliance-script.md) invece del disco rigido virtuale scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console di gestione di Hyper selezionare **Macchine virtuali**, fare clic con il pulsante destro del mouse sulla VM e scegliere **Connetti**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
1. Accettare le **condizioni di licenza**e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
      - Fare clic su **Configura proxy** per e specificare l'indirizzo del proxy, nel formato http://ProxyIPAddress o nella http://ProxyFQDN) porta di ascolto.
      - Se il proxy richiede l'autenticazione, specificare le credenziali.
      - È supportato solo il proxy HTTP.
      - Se sono stati aggiunti dettagli del proxy o è stato disabilitato il proxy e/o l'autenticazione, fare clic su **Salva** per attivare nuovamente il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di VM, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Installare gli aggiornamenti**: Azure migrate server Assessment verifica che nel dispositivo siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave di progetto Azure migrate** copiata dal portale. Se non si dispone della chiave, passare a **Server Assessment> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se non viene visualizzato, assicurarsi di aver disattivato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione dispone delle [autorizzazioni](tutorial-prepare-hyper-v.md#prepare-azure) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Dopo aver registrato correttamente il dispositivo, è possibile visualizzare i dettagli di registrazione facendo clic su **Visualizza dettagli**.



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

1. In **passaggio 1: fornire le credenziali dell'host Hyper-v**fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per un host o cluster Hyper-v che l'appliance userà per individuare le macchine virtuali. Fare clic su **Save**.
1. Se si desidera aggiungere più credenziali contemporaneamente, fare clic su **Aggiungi altro** per salvare e aggiungere altre credenziali. Per l'individuazione di macchine virtuali Hyper-V sono supportate più credenziali.
1. In **Step 2: provide Hyper-v host/cluster Details**fare clic su **Add Discovery Source** per specificare l'host Hyper-v/ **indirizzo IP del cluster/FQDN** e il nome descrittivo per le credenziali per la connessione all'host o al cluster.
1. È possibile **aggiungere un singolo elemento** alla volta o **aggiungere più elementi** in un'unica posizione. È disponibile anche un'opzione per fornire i dettagli dell'host/cluster Hyper-V tramite l' **importazione di CSV**.

    ![Selezioni per l'aggiunta dell'origine di individuazione](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se si sceglie **Aggiungi singolo elemento**, è necessario specificare un nome descrittivo per le credenziali e l'host Hyper-V/ **indirizzo IP del cluster/FQDN** e fare clic su **Salva**.
    - Se si sceglie **Aggiungi più elementi** _(selezionato per impostazione predefinita)_, è possibile aggiungere più record contemporaneamente specificando l' **indirizzo IP/FQDN** del cluster o dell'host Hyper-V con il nome descrittivo per le credenziali nella casella di testo. **Verificare** i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV**, è possibile scaricare un file di modello CSV, popolare il file con l'host Hyper-V/ **indirizzo IP del cluster/FQDN** e il nome descrittivo per le credenziali. Si importa quindi il file nell'appliance, si **verificano** i record nel file e si fa clic su **Save (Salva**).

1. Quando si fa clic su Salva, il dispositivo tenterà di convalidare la connessione agli host/cluster Hyper-V aggiunti e visualizzerà lo **stato di convalida** nella tabella per ogni host/cluster.
    - Per gli host o i cluster convalidati correttamente, è possibile visualizzare altri dettagli facendo clic sul relativo indirizzo IP/FQDN.
    - Se la convalida non riesce per un host, esaminare l'errore facendo clic su **convalida non riuscita** nella colonna stato della tabella. Risolvere il problema e convalidare di nuovo.
    - Per rimuovere gli host o i cluster, fare clic su **Elimina**.
    - Non è possibile rimuovere un host specifico da un cluster. È possibile rimuovere solo l'intero cluster.
    - È possibile aggiungere un cluster, anche in caso di problemi con host specifici al suo interno.
1. È possibile **rivalidare** la connettività a host/cluster in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione per avviare**l'individuazione delle VM dagli host o dai cluster convalidati correttamente. Dopo che l'individuazione è stata avviata correttamente, è possibile controllare lo stato di individuazione rispetto a ogni host/cluster nella tabella.

Viene avviata l'individuazione. Sono necessari circa 2 minuti per host per visualizzare i metadati dei server individuati nella portale di Azure.

## <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Dopo l'individuazione, è possibile verificare se le VM vengono visualizzate nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.


## <a name="next-steps"></a>Passaggi successivi

Prova [Hyper-V Assessment](tutorial-assess-hyper-v.md) con Azure migrate server assessment.
