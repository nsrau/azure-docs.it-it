---
title: Valutare la migrazione delle VM Hyper-V ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come valutare le macchine virtuali Hyper-V locali per la migrazione ad Azure tramite Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 57d91f14b8f3a9f58373cbd43561a03a8546fd8f
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514493"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Valutare le VM Hyper-V con Valutazione server di Azure Migrate

Questo articolo descrive come valutare le VM Hyper-V locali con lo strumento [Azure Migrate: Valutazione server](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Questa esercitazione è la seconda di una serie che illustra come valutare le VM Hyper-V ed eseguirne la migrazione ad Azure. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un progetto di Azure Migrate.
> * Configurare e registrare un'appliance di Azure Migrate.
> * Avviare l'individuazione continua di macchine virtuali locali.
> * Raggruppare le VM individuate e valutare il gruppo.
> * Esaminare la valutazione.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli sulle procedure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- [Completare](tutorial-prepare-hyper-v.md) la prima esercitazione di questa serie. In caso contrario, le istruzioni di questa esercitazione non funzioneranno.
- Ecco le operazioni che dovrebbero essere state completate nella prima esercitazione:
    - [Preparare Azure](tutorial-prepare-hyper-v.md#prepare-azure) per l'uso con Azure Migrate.
    - [Preparare gli host Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-assessment) e la valutazione delle macchine virtuali.
    - [Verificare](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) i requisiti per la distribuzione dell'appliance di Azure Migrate per la valutazione di Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Configurare un progetto di Azure Migrate

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. Nei risultati della ricerca selezionare **Azure Migrate**.
3. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server**.

    ![Individuare e valutare i server](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. In **Attività iniziali** fare clic su **Aggiungi strumenti**.
5. Nella scheda **Progetto di migrazione**  selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

    - L'area del progetto viene usata solo per archiviare i metadati raccolti dalle macchine virtuali locali.
    - Quando si esegue la migrazione delle VM, è possibile selezionare un'area di destinazione di Azure diversa. Tutte le aree di Azure sono supportate per la destinazione della migrazione.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Fare clic su **Avanti**.
8. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
10. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**.
11. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate


Azure Migrate: Valutazione server si basa su un'appliance leggera di Azure Migrate. L'appliance esegue l'individuazione delle macchine virtuali e ne invia i metadati e i dati sulle prestazioni ad Azure Migrate. L'appliance può essere configurata in diversi modi.

- Per configurare l'appliance in una macchina virtuale Hyper-V, è possibile usare un disco rigido virtuale Hyper-V scaricato. Questo è il metodo usato in questa esercitazione.
- Configurare l'appliance in una macchina virtuale Hyper-Vo in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale usando il disco rigido virtuale o se si usa Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **1: Generare la chiave del progetto Azure Migrate** specificare un nome per l'appliance Azure Migrate che verrà configurata per l'individuazione delle macchine virtuali Hyper-V. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-vhd"></a>Scaricare il disco rigido virtuale

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file VHD e fare clic su **Scarica**. 

   ![Selezioni per Individua macchine virtuali](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selezioni per Genera chiave](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.

2. Eseguire il comando di PowerShell seguente per generare il codice hash per il file ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:

        **Scenario** | **Scaricare** | **SHA256**
        --- | --- | ---
        Hyper-V (10,4 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Per Azure per enti pubblici:

        **Scenario*** | **Scaricare** | **SHA256**
        --- | --- | ---
        Hyper-V (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  0769c5f8df1e8c1ce4f685296f9ee18e1ca63e4a111d9aa4e6982e069df430d7


### <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare la VM.

1. Dopo aver scaricato il file VHD compresso nell'host Hyper-V in cui verrà inserita la macchina virtuale dell'appliance, estrarre il file.
    - Nel percorso di estrazione il file viene decompresso in una cartella denominata **AzureMigrateAppliance_VersionNumber**.
    - Questa cartella contiene una sottocartella, anch'essa denominata **AzureMigrateAppliance_VersionNumber**.
    - Questa sottocartella contiene altre tre sottocartelle: **Snapshots**, **Virtual Hard Disks** e **Virtual Machines**.

2. Aprire la console di gestione di Hyper-V. In **Azioni** fare clic su **Importa macchina virtuale**.

    ![Distribuire il disco rigido virtuale](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Nella procedura guidata Importa macchina virtuale selezionare **Prima di iniziare**, quindi fare clic su **Avanti**.
3. In **Individua cartella** selezionare la cartella **Virtual Machines**. Quindi fare clic su **Next**.
1. In **Selezione macchina virtuale** fare clic su **Avanti**.
2. In **Scegliere il tipo di importazione** fare clic su **Copia macchina virtuale (crea nuovo ID univoco)** . Quindi fare clic su **Next**.
3. In **Selezione destinazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
4. In **Cartelle di archiviazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
5. In **Scegli la rete**  specificare il commutatore virtuale che verrà usato dalla VM. È necessario che il commutatore abbia connettività Internet per inviare dati ad Azure. Leggere [informazioni](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) sulla creazione di uno switch virtuale.
6. In **Riepilogo** rivedere le impostazioni. Fare clic su **Fine**.
7. Nella console di gestione di Hyper-V selezionare **Macchine virtuali** e avviare la VM.


## <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script PowerShell](deploy-appliance-script.md) invece del disco rigido virtuale scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console di gestione di Hyper selezionare **Macchine virtuali**, fare clic con il pulsante destro del mouse sulla VM e scegliere **Connetti**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
      - Fare clic su **Set up proxy** (Configura proxy) e specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
      - Se il proxy richiede l'autenticazione, specificare le credenziali.
      - È supportato solo il proxy HTTP.
      - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di VM, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Install updates** (Installa aggiornamenti): Valutazione server di Azure Migrate verifica che nell'appliance siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi dell'appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nell'appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Valutazione server > Individua > Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](tutorial-prepare-hyper-v.md#prepare-azure) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegare le credenziali per i dischi rigidi virtuali SMB

Se i dischi rigidi virtuali sono in esecuzione in SMB, è necessario abilitare la delega delle credenziali dall'appliance agli host Hyper-V. A questo scopo, abilitare ogni host in modo che funga da delegato per l'appliance. Se le esercitazioni sono state seguite nell'ordine, questa operazione dovrebbe essere eseguita nell'esercitazione precedente, durante la preparazione di Hyper-V per la valutazione e la migrazione. Sarà stato configurato CredSSP per gli host [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials) oppure [eseguendo un apposito script](tutorial-prepare-hyper-v.md#run-the-script).

Eseguire l'abilitazione nell'appliance nel modo seguente:

#### <a name="option-1"></a>Opzione 1

Nell'appliance VM eseguire questo comando. HyperVHost1/HyperVHost2 sono nomi host di esempio.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Esempio: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opzione 2

In alternativa, eseguire questa operazione nell'editor Criteri di gruppo locali dell'appliance:

1. In **Criteri del computer locale** > **Configurazione computer** fare clic su **Modelli amministrativi** > **Sistema** > **Delega di credenziali**.
2. Fare doppio clic su **Consenti delega credenziali nuove** e selezionare **Abilitata**.
3. In **Opzioni** fare clic su **Mostra** e aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.
4. A questo punto, in **Delega di credenziali** fare doppio clic su **Consenti delega credenziali nuove solo con autenticazione server NTLM**. Anche in questo caso, aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dall'appliance agli host o cluster Hyper-V e avviare l'individuazione delle macchine virtuali.

1. In **Passaggio 1: Specificare le credenziali dell'host Hyper-V**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **Nome utente** e **Password** per l'host/cluster Hyper-V che verrà usato dall'appliance per individuare le macchine virtuali. Fare clic su **Save**.
1. Se si vogliono aggiungere più credenziali contemporaneamente, fare clic su **Aggiungi altre** per salvare e aggiungere altre credenziali. Sono supportate più credenziali per l'individuazione di macchine virtuali Hyper-V.
1. In **Passaggio 2: Specificare i dettagli per host/cluster Hyper-V** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** dell'host/cluster Hyper-V e il nome descrittivo per le credenziali per la connessione all'host/cluster.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli dell'host/cluster Hyper-V tramite **Importa CSV**.

    ![Selezioni per l'aggiunta dell'origine di individuazione](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se si sceglie **Add single item** (Aggiungi singolo elemento) è necessario specificare un nome descrittivo per le credenziali e **Indirizzo IP/FQDN** dell'host/cluster Hyper-V, quindi fare clic su **Salva**.
    - Se si sceglie **Add multiple items** (Aggiungi più elementi) _selezionato per impostazione predefinita_, è possibile aggiungere più record contemporaneamente specificando **Indirizzo IP/FQDN** dell'host/cluster Hyper-V con il nome descrittivo per le credenziali nella casella di testo. Fare clic su **Verifica** per controllare i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** è possibile scaricare un file modello CSV, quindi compilare il file con **Indirizzo IP/FQDN** dell'host/cluster Hyper-V e il nome descrittivo per le credenziali. Importare quindi il file nell'appliance, **verificare** i record nel file e fare clic su **Salva**.

1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione agli host/cluster Hyper-V aggiunti e visualizzerà lo **Stato di convalida** nella tabella per ogni host/cluster.
    - Per gli host o i cluster convalidati correttamente, è possibile visualizzare altri dettagli facendo clic sul relativo indirizzo IP/FQDN.
    - Se la convalida non riesce per un host, esaminare l'errore facendo clic su **Convalida non riuscita** nella colonna Stato della tabella. Risolvere il problema e ripetere la convalida.
    - Per rimuovere gli host o i cluster, fare clic su **Elimina**.
    - Non è possibile rimuovere un host specifico da un cluster. È possibile rimuovere solo l'intero cluster.
    - È possibile aggiungere un cluster, anche in caso di problemi con host specifici al suo interno.
1. È possibile **riconvalidare** la connettività agli host/server in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione** per avviare l'individuazione delle macchine virtuali dagli host/cluster convalidati correttamente. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni host/server nella tabella.

Viene avviata l'individuazione. Per la visualizzazione dei metadati dei server individuati nel portale di Azure sono necessari circa 2 minuti per ogni host.

### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Dopo l'individuazione, è possibile verificare se le VM vengono visualizzate nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.

## <a name="set-up-an-assessment"></a>Configurare una valutazione

È possibile eseguire due tipi di valutazioni con Valutazione server di Azure Migrate.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni VM consigliate**: in base alle dimensioni delle VM locali<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.



### <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. In **Server** > **Azure Migrate: Valutazione server** fare clic su **Valuta**.

    ![Valutare](./media/tutorial-assess-hyper-v/assess.png)

3. In **Valuta server** specificare un nome per la valutazione.
4. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Proprietà valutazione](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie una o più VM per la valutazione.
4. In **Aggiungere le macchine virtuali al gruppo** selezionare le VM da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    ![Creare una valutazione](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.


## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.


### <a name="view-an-assessment"></a>Visualizzare una valutazione

1. In **Obiettivi della migrazione** >  **Server** > **Azure Migrate: Valutazione server** fare clic su **Valutazioni**.
2. In **Valutazioni** fare clic su una valutazione per aprirla.

    ![Riepilogo della valutazione](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se le VM sono pronte per la migrazione ad Azure.
2. Verificare lo stato delle VM:
    - **Idonea per Azure**: Azure Migrate consiglia le dimensioni e le stime dei costi per le VM nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

2. Fare clic su uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle VM ed eseguire il drill-down per visualizzare i dettagli delle VM, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Questa visualizzazione mostra il costo stimato di calcolo e archiviazione associato all'esecuzione delle macchine virtuali in Azure.

1. Esaminare i costi mensili di calcolo e archiviazione. I costi sono aggregati per tutte le VM nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una VM e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione delle VM locali come VM IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensile. Questa visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli relativi a VM specifiche.


### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità.

![Classificazione di attendibilità](./media/tutorial-assess-hyper-v/confidence-rating.png)

- La classificazione è compresa tra 1 stella (minima) e 5 stelle (massima).
- La classificazione di attendibilità aiuta a stimare l'affidabilità delle indicazioni relative alle dimensioni fornite dalla valutazione.
- La classificazione di attendibilità è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.

Di seguito sono elencate le classificazioni di attendibilità per una valutazione.

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

[Altre informazioni](best-practices-assessment.md#best-practices-for-confidence-ratings) sulle procedure consigliate per le classificazioni di attendibilità.





## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * È stata configurata un'appliance di Azure Migrate
> * È stata creata ed esaminata una valutazione

Continuare con le terza esercitazione della serie per informazioni su come eseguire la migrazione di VM Hyper-V ad Azure con lo strumento Migrazione server di Azure Migrate.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di VM Hyper-V](./tutorial-migrate-hyper-v.md)
