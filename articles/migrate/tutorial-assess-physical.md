---
title: Valutare i server fisici per la migrazione ad Azure con Valutazione server di Azure Migrate
description: Questo articolo descrive come valutare i server fisici locali per la migrazione ad Azure usando Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 21ffc425ff7d04bbb1bc0c3a550133ae5374b1e9
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950103"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Valutare i server fisici con Valutazione server di Azure Migrate

Questo articolo descrive come valutare i server fisici locali con lo strumento Valutazione server di Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

Questa esercitazione è la seconda di una serie che illustra come valutare i server fisici ed eseguirne la migrazione ad Azure. In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Configurare un progetto di Azure Migrate.
> * Configurare un'appliance Azure Migrate da eseguire in locale per valutare i server fisici.
> * Avviare l'individuazione continua di server fisici locali. L'appliance invia ad Azure i dati sulla configurazione e le prestazioni dei server individuati.
> * Raggruppare i server individuati e valutare il gruppo di server.
> * Esaminare la valutazione.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli sulle procedure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- [Completare](tutorial-prepare-physical.md) la prima esercitazione di questa serie. In caso contrario, le istruzioni di questa esercitazione non funzioneranno.
- Ecco le operazioni che dovrebbero essere state completate nella prima esercitazione:
    - [Configurare le autorizzazioni di Azure](tutorial-prepare-physical.md) per Azure Migrate.
    - [Preparare i server fisici](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) per la valutazione. È necessario verificare i requisiti dell'appliance. È necessario anche un account configurato per l'individuazione di server fisici. Le porte necessarie devono essere disponibili e occorre essere a conoscenza degli URL necessari per l'accesso ad Azure.




## <a name="set-up-an-azure-migrate-project"></a>Configurare un progetto di Azure Migrate

Per configurare un nuovo progetto di Azure Migrate, seguire questa procedura.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server**.

    ![Individuare e valutare i server](./media/tutorial-assess-physical/assess-migrate.png)

4. In **Attività iniziali** fare clic su **Aggiungi strumenti**.
5. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.  
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

    - L'area geografica del progetto viene usata solo per archiviare i metadati raccolti dai server locali.
    - Per la migrazione è possibile selezionare qualsiasi area di destinazione.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Fare clic su **Avanti**.
8. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

    ![Creare un progetto di Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
10. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**.
11. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.


## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate

Azure Migrate: Valutazione server esegue un'appliance leggera.

- L'appliance esegue l'individuazione dei server fisici e ne invia i metadati e i dati sulle prestazioni allo strumento Valutazione server di Azure Migrate.
- Per configurare l'appliance occorre:
    - Scaricare un file compresso con lo script del programma di installazione di Azure Migrate dal portale di Azure.
    - Estrarre il contenuto del file compresso. Avviare la console PowerShell con privilegi amministrativi.
    - Eseguire lo script di PowerShell per avviare l'applicazione Web dell'appliance.
    - Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.
- È possibile configurare più appliance per un singolo progetto di Azure Migrate. In tutte le appliance è possibile individuare un numero qualsiasi di server fisici. Una singola appliance può individuare un massimo di 1000 server.

### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Fisico o di altro tipo (AWS, GCP, Xen e così via)** .
3. In **1: Generare la chiave del progetto Azure Migrate** specificare un nome per l'appliance Azure Migrate che verrà configurata per l'individuazione dei server fisici o virtuali. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-installer-script"></a>Scaricare lo script del programma di installazione

In **2: Scaricare l'appliance di Azure Migrate** fare clic su **Scarica**.

   ![Selezioni per Individua macchine virtuali](./media/tutorial-assess-physical/servers-discover.png)


   ![Selezioni per Genera chiave](./media/tutorial-assess-physical/generate-key-physical.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Esempio di utilizzo per il cloud per enti pubblici: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Verificare le versioni più recenti dell'appliance e i valori hash:
    - Per il cloud pubblico:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140334) | 6de88e229c2b4836d16ce03fdfac93b5c27274945577bd8a359d598cf3777b24

    - Per Azure per enti pubblici:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140338) | 5a4715a9d10e81a5f38192b7d1c6ac0919ae3998afbf2e933c99bafae6bef80e

### <a name="run-the-azure-migrate-installer-script"></a>Eseguire lo script del programma di installazione di Azure Migrate

Lo script del programma di installazione esegue le operazioni seguenti:

- Installa gli agenti e un'applicazione Web per l'individuazione e la valutazione dei server fisici.
- Installa i ruoli di Windows, tra cui il Servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo di IIS riscrivibile. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM) con i dettagli delle impostazioni permanenti per Azure Migrate.
- Crea i seguenti file nei relativi percorsi:
    - **File di configurazione**: %Programdata%\Microsoft Azure\Config
    - **File di log**: %Programdata%\Microsoft Azure\Logs

Eseguire lo script nel modo seguente:

1. Estrarre il file ZIP in una cartella nel server che ospiterà l'appliance.  Assicurarsi di non eseguire lo script in un computer in un'appliance di Azure Migrate esistente.
2. Avviare PowerShell nello stesso server con privilegi amministrativi (elevati).
3. Modificare la directory di PowerShell nella cartella in cui è stato estratto il contenuto del file compresso scaricato.
4. Eseguire lo script denominato **AzureMigrateInstaller.ps1** eseguendo il comando seguente:

    - Per il cloud pubblico: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Per Azure per enti pubblici: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Lo script avvierà l'applicazione Web dell'appliance al termine dell'operazione.

In caso di problemi, è possibile accedere ai log degli script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log per la risoluzione dei problemi.

### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Aprire un browser in un computer in grado di connettersi all'appliance, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop facendo clic sul relativo collegamento.
2. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che il server abbia accesso a Internet. Se il server usa un proxy:
        - Fare clic su **Set up proxy** (Configura proxy) e specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
        - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di server, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Install updates** (Installa aggiornamenti): Valutazione server di Azure Migrate verifica che nell'appliance siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi dell'appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nell'appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Valutazione server > Individua > Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](tutorial-prepare-physical.md) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

A questo punto, connettersi dall'appliance ai server fisici da individuare e avviare l'individuazione.

1. In **Passaggio 1: Specificare le credenziali per l'individuazione di server fisici o virtuali Windows e Linux**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **Nome utente** e **Password** per un server Windows o Linux. Fare clic su **Save**.
1. Se si vogliono aggiungere più credenziali contemporaneamente, fare clic su **Aggiungi altre** per salvare e aggiungere altre credenziali. Sono supportate più credenziali per l'individuazione di server fisici.
1. In **Passaggio 2: Specificare i dettagli del server fisico o virtuale** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali per la connessione al server.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli del server tramite **Importa CSV**.

    ![Selezioni per l'aggiunta dell'origine di individuazione](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se si sceglie **Add single item** (Aggiungi singolo elemento) è possibile scegliere il tipo di sistema operativo, specificare un nome descrittivo per le credenziali, aggiungere **Indirizzo IP/FQDN** del server e fare clic su **Salva**.
    - Se si sceglie **Add multiple items** (Aggiungi più elementi), è possibile aggiungere più record contemporaneamente specificando **Indirizzo IP/FQDN** del server con il nome descrittivo per le credenziali nella casella di testo. Fare clic su **Verifica** per controllare i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** _(selezionato per impostazione predefinita)_ , è possibile scaricare un file modello CSV, quindi compilare il file con **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali. Importare quindi il file nell'appliance, **verificare** i record nel file e fare clic su **Salva**.

1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione ai server aggiunti e visualizzerà lo **Stato di convalida** nella tabella per ogni server.
    - Se la convalida non riesce per un server, esaminare l'errore facendo clic su **Convalida non riuscita** nella colonna Stato della tabella. Risolvere il problema e ripetere la convalida.
    - Per rimuovere un server, fare clic su **Elimina**.
1. È possibile **riconvalidare** la connettività ai server in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione** per avviare l'individuazione dei server convalidati correttamente. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni server nella tabella.


Viene avviata l'individuazione. Per la visualizzazione dei metadati del server individuato nel portale di Azure sono necessari circa 2 minuti per ogni server.

### <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Dopo l'individuazione è possibile verificare che i server siano visualizzati nel portale di Azure.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.

## <a name="set-up-an-assessment"></a>Configurare una valutazione

È possibile creare due tipi di valutazioni con Azure Migrate: Valutazione server.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni VM consigliate**: in base alle dimensioni del server locale<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.


### <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. Nella scheda **Server**, nel riquadro **Azure Migrate: Valutazione server**, fare clic su **Valuta**.

    ![Valutare](./media/tutorial-assess-physical/assess.png)

2. In **Valuta server** specificare un nome per la valutazione.
3. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.

    ![Proprietà valutazione](./media/tutorial-assess-physical/view-all.png)

3. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie uno o più server per la valutazione.
4. In **Aggiungere le macchine virtuali al gruppo** selezionare i server da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

    ![Creare una valutazione](./media/tutorial-assess-physical/assessment-create.png)

6. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.



## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se i server sono idonei per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione dei server in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

### <a name="view-an-assessment"></a>Visualizzare una valutazione

1. In **Obiettivi della migrazione** >  **Server** fare clic su **Valutazioni** in **Azure Migrate: Valutazione server**.
2. In **Valutazioni** fare clic su una valutazione per aprirla.

    ![Riepilogo della valutazione](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se i server sono pronti per la migrazione ad Azure.
2. Verificare lo stato:
    - **Idonea per Azure**: Azure Migrate consiglia le dimensioni e le stime dei costi per le VM nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

2. Fare clic su uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità dei server ed eseguire il drill-down per visualizzare i dettagli dei server, incluse le impostazioni di calcolo, archiviazione e rete.



### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Questa visualizzazione mostra il costo stimato di calcolo e archiviazione associato all'esecuzione delle macchine virtuali in Azure.

1. Esaminare i costi mensili di calcolo e archiviazione. I costi sono aggregati per tutti i server nel gruppo valutato.

    - Le stime dei costi sono basate sulle dimensioni consigliate per una VM e sui relativi dischi e proprietà.
    - Vengono visualizzati i costi mensili stimati per il calcolo e l'archiviazione.
    - La stima dei costi è relativa all'esecuzione dei server locali come VM IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

2. È possibile esaminare le stime dei costi di archiviazione mensile. Questa visualizzazione mostra i costi di archiviazione aggregati per il gruppo valutato, suddivisi in base ai diversi tipi di dischi di archiviazione.
3. È possibile eseguire il drill-down per visualizzare i dettagli relativi a server specifici.


### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Quando si eseguono valutazioni basate sulle prestazioni, alla valutazione viene assegnata una classificazione di attendibilità.

![Classificazione di attendibilità](./media/tutorial-assess-physical/confidence-rating.png)

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

Continuare con le terza esercitazione della serie per imparare a eseguire la migrazione di server fisici ad Azure con Azure Migrate: Server Migration.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di server fisici](./tutorial-migrate-physical-virtual-machines.md)
