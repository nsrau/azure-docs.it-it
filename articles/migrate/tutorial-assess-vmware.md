---
title: Valutare le macchine virtuali VMware con Valutazione server di Azure Migrate
description: Questo articolo descrive come valutare le macchine virtuali VMware locali per la migrazione ad Azure tramite Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 8c2784e999e751972883b6c9ffba2485bb9fe9e1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950086"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Valutare le VM VMware con Valutazione server

Questo articolo illustra come valutare le macchine virtuali VMware locali usando lo strumento [Azure Migrate: Valutazione server](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Questa esercitazione è la seconda di una serie che illustra come valutare le macchine virtuali VMware ed eseguirne la migrazione ad Azure. In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Configurare un progetto di Azure Migrate.
> * Configurare un'appliance Azure Migrate da eseguire in locale per valutare le macchine virtuali.
> * Avviare l'individuazione continua di macchine virtuali locali. L'appliance invia ad Azure i dati sulla configurazione e le prestazioni delle macchine virtuali individuate.
> * Raggruppare le macchine virtuali individuate e valutare il gruppo di VM.
> * Esaminare la valutazione.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli sulle procedure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- [Completare la prima esercitazione](tutorial-prepare-vmware.md) di questa serie. In caso contrario, le istruzioni di questa esercitazione non funzioneranno.
- Ecco le operazioni che dovrebbero essere state completate nella prima esercitazione:
    - [Preparare Azure](tutorial-prepare-vmware.md#prepare-azure) per l'uso con Azure Migrate.
    - [Preparare VMware per la valutazione](tutorial-prepare-vmware.md#prepare-for-assessment) della migrazione. La procedura include la verifica delle impostazioni di VMware, e la configurazione di un account per l'accesso di Azure Migrate al server vCenter.
    - [Verificare](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) i requisiti per la distribuzione dell'appliance di Azure Migrate per la valutazione di VMware.

## <a name="set-up-an-azure-migrate-project"></a>Configurare un progetto di Azure Migrate

Per configurare un nuovo progetto di Azure Migrate, seguire questa procedura:

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** selezionare **Valutare ed eseguire la migrazione dei server**.

   ![Pulsante per valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware/assess-migrate.png)

4. In **Attività iniziali** selezionare **Aggiungi strumenti**.
5. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.     
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-assess-vmware/migrate-project.png)

7. Selezionare **Avanti**.
8. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

   ![Selezione per lo strumento Valutazione server](./media/tutorial-assess-vmware/assessment-tool.png)

9. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
10. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e selezionare **Aggiungi strumenti**.
11. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate

Azure Migrate: Valutazione server si basa su un'appliance leggera di Azure Migrate. L'appliance esegue l'individuazione delle macchine virtuali e ne invia i metadati e i dati sulle prestazioni ad Azure Migrate. L'appliance può essere configurata in diversi modi.

- Configurare l'appliance in una macchina virtuale VMware usando un modello OVA scaricato. **Questo è il metodo usato in questa esercitazione.**
- Configurare l'appliance in una macchina virtuale VMware o in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale con un modello OVA o se si usa Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.


### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. In **1: Generare la chiave del progetto Azure Migrate** specificare un nome per l'appliance Azure Migrate che verrà configurata per l'individuazione delle macchine virtuali VMware. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-ova-template"></a>Scaricare il modello OVA
In **2: Scaricare l'appliance di Azure Migrate** selezionare il file OVA e fare clic su **Scarica**. 


   ![Selezioni per Individua macchine virtuali](./media/tutorial-assess-vmware/servers-discover.png)


   ![Selezioni per Genera chiave](./media/tutorial-assess-vmware/generate-key-vmware.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza:

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (11,6 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Per Azure per enti pubblici:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 7dab9445a89b47302994d6de4caddaa092c1c582c8f3c1fc5b9c4908c7d2f9f7


### <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare una macchina virtuale:

1. Nella console di vSphere Client selezionare **File** > **Distribuire il modello OVF**.

   ![Comando di menu per la distribuzione di un modello OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la VM. Selezionare l'oggetto inventario in cui verrà ospitata la VM.
4. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale.
5. In **Storage** (Archiviazione) specificare la destinazione di archiviazione della macchina virtuale.
6. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
7. In **Mapping di rete** specificare la rete a cui si connetterà la macchina virtuale. La rete richiede la connettività Internet per l'invio dei metadati allo strumento Valutazione server di Azure Migrate.
8. Rivedere e confermare le impostazioni e quindi selezionare **Fine**.

## <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script di PowerShell](deploy-appliance-script.md) invece del modello OVA scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console del client vSphere fare clic con il pulsante destro del mouse su sulla macchina virtuale e quindi scegliere **Open Console** (Apri console).
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance selezionando il relativo collegamento.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
   - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
     - Fare clic su **Set up proxy** (Configura proxy) per specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **View appliance services** (Visualizza servizi appliance) per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance, come indicato nelle **istruzioni di installazione**.

     Migrazione server di Azure Migrate usa VDDK per replicare le macchine virtuali durante la migrazione ad Azure. 
1. Se lo si desidera, è possibile **rieseguire i prerequisiti** in qualsiasi momento durante la configurazione dell'appliance per verificare se l'appliance soddisfa tutti i prerequisiti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Valutazione server > Individua > Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](tutorial-prepare-vmware.md#prepare-azure) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

L'appliance deve connettersi al server vCenter per individuare la configurazione e i dati sulle prestazioni delle macchine virtuali.

1. In **Passaggio 1: Specificare le credenziali del server vCenter**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **Nome utente** e **Password** per l'account del server vCenter che verrà usato dall'appliance per individuare le macchine virtuali nell'istanza del server vCenter.
    - È necessario aver configurato un account con le autorizzazioni necessarie nell'[esercitazione precedente](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter data center, cluster, una cartella di cluster, host, una cartella di host o singole VM), rivedere le istruzioni riportate in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure Migrate.
1. In **Passaggio 2: Specificare i dettagli del server vCenter**, fare clic su **Aggiungi origine di individuazione** per selezionare il nome descrittivo per le credenziali nell'elenco a discesa, specificare **Indirizzo IP/FQDN** dell'istanza del server vCenter. È possibile lasciare l'impostazione predefinita per **Porta** (443) o specificare una porta personalizzata sulla quale sarà in ascolto il server vCenter e fare clic su **Salva**.
1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione al server vCenter con le credenziali specificate e visualizzerà lo **Stato di convalida** nella tabella per ogni indirizzo IP/FQDN di server vCenter.
1. È possibile **riconvalidare** la connettività al server vCenter in qualsiasi momento prima di avviare l'individuazione.
1. In **Passaggio 3: Specificare le credenziali della macchina virtuale per individuare le applicazioni installate e per eseguire il mapping delle dipendenze senza agente**, fare clic su **Aggiungi credenziali** e specificare il sistema operativo per il quale vengono fornite le credenziali, il nome descrittivo per le credenziali e **Nome utente** e **Password**. Fare quindi clic su **Salva**.

    - Facoltativamente, è possibile aggiungere qui le credenziali se è stato creato un account da usare per la [funzionalità di individuazione delle applicazioni](how-to-discover-applications.md) o la [funzionalità di analisi delle dipendenze senza agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se non si vogliono usare queste funzionalità, è possibile fare clic sul dispositivo di scorrimento per ignorare il passaggio. È possibile cambiare idea in qualsiasi momento in un secondo momento.
    - Esaminare le credenziali necessarie per l'[individuazione delle applicazioni](migrate-support-matrix-vmware.md#application-discovery-requirements) o per l'[analisi delle dipendenze senza agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Fare clic su **Avvia individuazione** per iniziare l'individuazione delle macchine virtuali. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni indirizzo IP/FQDN del server vCenter nella tabella.

L'individuazione funziona come segue:
- Sono necessari circa 15 minuti prima che i metadati delle VM individuate vengano visualizzati nel portale.
- L'individuazione di applicazioni, ruoli e funzionalità installati richiede tempo. La durata dipende dal numero di macchine virtuali individuate. Per 500 VM, è necessaria circa un'ora prima che l'inventario di applicazioni venga visualizzato nel portale di Azure Migrate.

### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Dopo l'individuazione è possibile verificare che le macchine virtuali siano visualizzate nel portale di Azure:

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** selezionare l'icona che mostra il numero di **Server individuati**.

## <a name="set-up-an-assessment"></a>Configurare una valutazione

È possibile creare due tipi di valutazioni con Valutazione server di Azure Migrate:

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione. [Altre informazioni](concepts-assessment-calculation.md)
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

Lo strumento Valutazione server offre due opzioni per i criteri di dimensionamento:

**Criteri di dimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni in base ai dati sulle prestazioni raccolti | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sui dati relativi all'utilizzo di CPU e memoria.<br/><br/> La raccomandazione sul tipo di disco (HDD/SSD standard o dischi gestiti Premium) è basata sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.<br/><br/> **Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sui dati relativi all'utilizzo di CPU e memoria.
**Come in locale** | Valutazioni che non usano i dati sulle prestazioni per fare raccomandazioni. | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sulle dimensioni delle VM locali<br/><br> Il tipo di disco consigliato è basato sull'opzione selezionata nell'impostazione del tipo di archiviazione per la valutazione.<br/><br/> **Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sulle dimensioni delle VM locali.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una *valutazione delle macchine virtuali di Azure* nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
2. Nella scheda **Server**, nel riquadro **Azure Migrate: Valutazione server** selezionare **Valuta**.

   ![Posizione del pulsante Valuta](./media/tutorial-assess-vmware/assess.png)

3. In **Valuta server** selezionare "Macchina virtuale di Azure" come tipo di valutazione, selezionare l'origine di individuazione e specificare il nome della valutazione.

    ![Informazioni di base sulla valutazione](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Selezionare **Visualizza tutto** per rivedere le proprietà della valutazione.

   ![Proprietà valutazione](./media/tutorial-assess-vmware/view-all.png)

5. Fare clic su **Avanti** per **selezionare i computer da valutare**. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie una o più VM per la valutazione.
6. In **Aggiungere le macchine virtuali al gruppo** selezionare le VM da aggiungere al gruppo.
7. Fare clic su **Avanti** per passare a **Rivedi e crea valutazione** e rivedere i dettagli della valutazione.
8. Selezionare **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

   ![Valutazione server](./media/tutorial-assess-vmware/assessment-create.png)

8. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.
9. Selezionare **Esporta valutazione** per scaricarla come file di Excel.

Se si vuole eseguire una **valutazione della soluzione Azure VMware**, seguire i passaggi illustrati [qui](how-to-create-azure-vmware-solution-assessment.md).


## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

Per visualizzare una valutazione:

1. In **Obiettivi della migrazione** > **Server** selezionare **Valutazioni** in **Azure Migrate: Valutazione server**.
2. In **Valutazioni** selezionare una valutazione per aprirla.

   ![Riepilogo della valutazione](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se le VM sono pronte per la migrazione ad Azure.
2. Verificare lo stato delle VM:
    - **Idonea per Azure**: stato usato quando Azure Migrate consiglia le dimensioni e le stime dei costi per le macchine virtuali nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

3. Selezionare uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle macchine virtuali ed eseguire il drill-down per visualizzare i dettagli delle macchine virtuali, incluse le impostazioni di calcolo, archiviazione e rete.

### <a name="review-cost-details"></a>Esaminare i dettagli dei costi

Il riepilogo della valutazione mostra i costi di calcolo e archiviazione stimati per l'esecuzione delle macchine virtuali in Azure. I costi sono aggregati per tutte le VM nel gruppo valutato. È possibile eseguire il drill-down per visualizzare i dettagli dei costi per macchine virtuali specifiche.

> [!NOTE]
> Le stime dei costi sono basate sulle dimensioni consigliate per una macchina virtuale e sui relativi dischi e proprietà. Le stime si riferiscono all'esecuzione delle macchine virtuali locali come macchine virtuali IaaS. Valutazione server di Azure Migrate non considera i costi per PaaS o SaaS.

I costi di archiviazione aggregati per il gruppo valutato sono suddivisi in base ai diversi tipi di dischi di archiviazione. 

### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

Valutazione server di Azure Migrate assegna una classificazione di attendibilità a una valutazione basata sulle prestazioni, da 1 stella (più bassa) a 5 stelle (più alta).

![Classificazione di attendibilità](./media/tutorial-assess-vmware/confidence-rating.png)

La classificazione di attendibilità aiuta a stimare l'affidabilità delle raccomandazioni sulle dimensioni della valutazione. La classificazione è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione:

**Disponibilità dei punti dati** | **Classificazione di attendibilità**
--- | ---
0%-20% | 1 stella
21%-40% | 2 stelle
41%-60% | 3 stelle
61%-80% | 4 stelle
81%-100% | 5 stelle

[Altre informazioni sulle procedure consigliate](best-practices-assessment.md#best-practices-for-confidence-ratings) per le classificazioni di attendibilità.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata un'appliance di Azure Migrate ed è stata creata ed esaminata una valutazione.

Per informazioni su come eseguire la migrazione di macchine virtuali VMware ad Azure con lo strumento Migrazione server di Azure Migrate, continuare con le terza esercitazione della serie:

> [!div class="nextstepaction"]
> [Eseguire la migrazione di VM VMware](./tutorial-migrate-vmware.md)
