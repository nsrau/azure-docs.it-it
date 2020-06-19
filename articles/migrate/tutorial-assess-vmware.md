---
title: Valutare le macchine virtuali VMware con Valutazione server di Azure Migrate
description: Questo articolo descrive come valutare le macchine virtuali VMware locali per la migrazione ad Azure tramite Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 231daff5972e9b2f115df9e6184c43a553f55b83
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771309"
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
1. In **Servizi** selezionare **Azure Migrate**.
1. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** selezionare **Valutare ed eseguire la migrazione dei server**.

   ![Pulsante per valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware/assess-migrate.png)

1. In **Attività iniziali** selezionare **Aggiungi strumenti**.
1. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.     
1. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-assess-vmware/migrate-project.png)

1. Selezionare **Avanti**.
1. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

   ![Selezione per lo strumento Valutazione server](./media/tutorial-assess-vmware/assessment-tool.png)

1. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
1. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e selezionare **Aggiungi strumenti**.
1. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate

Azure Migrate: Valutazione server si basa su un'appliance leggera di Azure Migrate. L'appliance esegue l'individuazione delle macchine virtuali e ne invia i metadati e i dati sulle prestazioni ad Azure Migrate. L'appliance può essere configurata in diversi modi.

- Configurare l'appliance in una macchina virtuale VMware usando un modello OVA scaricato. Questo è il metodo usato in questa esercitazione.
- Configurare l'appliance in una macchina virtuale VMware o in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale con un modello OVA o se si usa Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.


### <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
1. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
1. Selezionare **Scarica** per scaricare il file modello OVA.

   ![Selezioni per il download di un file OVA](./media/tutorial-assess-vmware/download-ova.png)

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
        VMware (10,9 GB) | [Versione più recente](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Per Azure per enti pubblici:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare una macchina virtuale:

1. Nella console di vSphere Client selezionare **File** > **Distribuire il modello OVF**.

   ![Comando di menu per la distribuzione di un modello OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
1. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la VM. Selezionare l'oggetto inventario in cui verrà ospitata la VM.
1. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale.
1. In **Storage** (Archiviazione) specificare la destinazione di archiviazione della macchina virtuale.
1. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
1. In **Mapping di rete** specificare la rete a cui si connetterà la macchina virtuale. La rete richiede la connettività Internet per l'invio dei metadati allo strumento Valutazione server di Azure Migrate.
1. Rivedere e confermare le impostazioni e quindi selezionare **Fine**.

## <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script di PowerShell](deploy-appliance-script.md) invece del modello OVA scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console del client vSphere fare clic con il pulsante destro del mouse su sulla macchina virtuale e quindi scegliere **Open Console** (Apri console).
1. Specificare la lingua, il fuso orario e la password per l'appliance.
1. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance selezionando il relativo collegamento.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
   - **License** (Licenza): Accettare le condizioni di licenza e leggere le informazioni di terze parti.
   - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
     - Fare clic su **Impostazioni proxy** e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMWare vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance.

     Migrazione server di Azure Migrate usa VDDK per replicare le macchine virtuali durante la migrazione ad Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Selezionare **Accedi**. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
1. Dopo aver eseguito l'accesso, tornare all'app Web.
1. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate, quindi selezionare il progetto.
1. Specificare un nome per l'appliance. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Selezionare **Registra**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

L'appliance deve connettersi al server vCenter per individuare la configurazione e i dati sulle prestazioni delle macchine virtuali.

### <a name="specify-vcenter-server-details"></a>Specificare i dettagli del server vCenter
1. In **Specificare i dettagli del server vCenter** specificare il nome di dominio completo o l'indirizzo IP dell'istanza del server vCenter. È possibile lasciare la porta predefinita o specificare una porta personalizzata su cui il server vCenter rimane in ascolto.
2. In **Nome utente** e **Password** specificare le credenziali dell'account del server vCenter che verranno usate dall'appliance per individuare le macchine virtuali nell'istanza del server vCenter. 

    - È necessario aver configurato un account con le autorizzazioni necessarie nell'[esercitazione precedente](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter data center, cluster, una cartella di cluster, host, una cartella di host o singole VM), rivedere le istruzioni riportate in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure Migrate.

3. Selezionare **Convalida connessione** per verificare che l'appliance possa connettersi al server vCenter.
4. In **Individua applicazioni e dipendenze nelle macchine virtuali** facoltativamente fare clic su **Aggiungi credenziali** e specificare il sistema operativo per cui le credenziali sono appropriate, oltre ai relativi nome utente e password. Fare quindi clic su **Aggiungi**.

    - Facoltativamente, è possibile aggiungere qui le credenziali se è stato creato un account da usare per la [funzionalità di individuazione delle applicazioni](how-to-discover-applications.md) o la [funzionalità di analisi delle dipendenze senza agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se non si usano queste funzionalità, è possibile ignorare questa impostazione.
    - Verificare le credenziali necessarie per l'[individuazione delle app](migrate-support-matrix-vmware.md#application-discovery-requirements) o l'[analisi senza agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. **Salvare e avviare l'individuazione** delle VM.

L'individuazione funziona come segue:
- Sono necessari circa 15 minuti prima che i metadati delle VM individuate vengano visualizzati nel portale.
- L'individuazione di applicazioni, ruoli e funzionalità installati richiede tempo. La durata dipende dal numero di macchine virtuali individuate. Per 500 VM, è necessaria circa un'ora prima che l'inventario di applicazioni venga visualizzato nel portale di Azure Migrate.

### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Dopo l'individuazione è possibile verificare che le macchine virtuali siano visualizzate nel portale di Azure:

1. Aprire il dashboard di Azure Migrate.
1. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** selezionare l'icona che mostra il numero di **Server individuati**.

## <a name="set-up-an-assessment"></a>Configurare una valutazione

È possibile creare due tipi di valutazioni con Valutazione server di Azure Migrate:

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: in base ai dati sull'utilizzo di CPU e memoria.<br/><br/> **Tipo di disco consigliato (disco gestito Standard o Premium)** : in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale | **Dimensioni VM consigliate**: in base alle dimensioni delle macchine virtuali locali.<br/><br> **Tipo di disco consigliato**: in base all'impostazione del tipo di archiviazione selezionata per la valutazione.

## <a name="run-an-assessment"></a>Eseguire una valutazione

Eseguire una valutazione nel modo seguente:

1. Rivedere le [procedure consigliate](best-practices-assessment.md) per la creazione di valutazioni.
1. Nella scheda **Server**, nel riquadro **Azure Migrate: Valutazione server** selezionare **Valuta**.

   ![Posizione del pulsante Valuta](./media/tutorial-assess-vmware/assess.png)

1. In **Valuta server** specificare un nome per la valutazione.
1. Selezionare **Visualizza tutto** per rivedere le proprietà della valutazione.

   ![Proprietà valutazione](./media/tutorial-assess-vmware/view-all.png)

1. In **Selezionare o creare un gruppo** selezionare **Crea nuovo** e specificare un nome per il gruppo. Un gruppo raccoglie una o più VM per la valutazione.
1. In **Aggiungere le macchine virtuali al gruppo** selezionare le VM da aggiungere al gruppo.
1. Selezionare **Crea valutazione** per creare il gruppo ed eseguire la valutazione.

   ![Valutazione server](./media/tutorial-assess-vmware/assessment-create.png)

1. Dopo aver creato la valutazione, visualizzarla in **Server** > **Azure Migrate: Valutazione server** > **Valutazioni**.
1. Selezionare **Esporta valutazione** per scaricarla come file di Excel.

## <a name="review-an-assessment"></a>Esaminare una valutazione

Una valutazione descrive:

- **Idoneità per Azure**: se le VM sono idonee per la migrazione ad Azure.
- **Stima dei costi mensili**: i costi mensili di calcolo e archiviazione stimati per l'esecuzione delle VM in Azure.
- **Stima costo di archiviazione mensile**: i costi stimati per l'archiviazione su disco dopo la migrazione.

Per visualizzare una valutazione:

1. In **Obiettivi della migrazione** > **Server** selezionare **Valutazioni** in **Azure Migrate: Valutazione server**.
1. In **Valutazioni** selezionare una valutazione per aprirla.

   ![Riepilogo della valutazione](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Esaminare l'idoneità per Azure

1. In **Idoneità per Azure** verificare se le VM sono pronte per la migrazione ad Azure.
1. Verificare lo stato delle VM:
    - **Idonea per Azure**: stato usato quando Azure Migrate consiglia le dimensioni e le stime dei costi per le macchine virtuali nella valutazione.
    - **Idonea con condizioni**: mostra i problemi e le correzioni consigliate.
    - **Non idonea per Azure**: mostra i problemi e le correzioni consigliate.
    - **Idoneità sconosciuta**: stato usato quando Azure Migrate non è in grado di valutare l'idoneità a causa di problemi di disponibilità dei dati.

1. Selezionare uno stato di **Idoneità per Azure**. È possibile visualizzare i dettagli sull'idoneità delle macchine virtuali ed eseguire il drill-down per visualizzare i dettagli delle macchine virtuali, incluse le impostazioni di calcolo, archiviazione e rete.

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
