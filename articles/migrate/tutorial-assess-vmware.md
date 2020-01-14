---
title: Valutare le macchine virtuali VMware per la migrazione ad Azure con Valutazione server di Azure Migrate
description: Questo articolo descrive come valutare le macchine virtuali VMware locali per la migrazione ad Azure tramite Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 31af4ad9c6985202555dbcbe86c52e45d5c4154a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453293"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Valutare le macchine virtuali VMware con Valutazione server di Azure Migrate

Questo articolo illustra come valutare le macchine virtuali VMware locali usando lo strumento Valutazione server in Azure Migrate.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) dei partner Microsoft.

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

## <a name="prerequisites"></a>Prerequisites

[Completare la prima esercitazione](tutorial-prepare-vmware.md) di questa serie. In caso contrario, le istruzioni di questa esercitazione non funzioneranno.

Ecco le operazioni che dovrebbero essere state completate nella prima esercitazione:

- [Configurare le autorizzazioni di Azure](tutorial-prepare-vmware.md#prepare-azure) per Azure Migrate.
- [Preparare VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) per la valutazione:
   - Verificare le impostazioni di VMware.
   - Configurare le autorizzazioni per creare una macchina virtuale VMware con un modello OVA.
   - Configurare un account per l'individuazione delle macchine virtuali. 
   - Rendere disponibili le porte necessarie.
   - Prestare attenzione agli URL necessari per l'accesso ad Azure.

## <a name="set-up-an-azure-migrate-project"></a>Configurare un progetto di Azure Migrate

Per configurare un nuovo progetto di Azure Migrate, seguire questa procedura:

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
1. In **Servizi** selezionare **Azure Migrate**.
1. In **Individuare, valutare ed eseguire la migrazione dei server** della pagina **Panoramica** selezionare **Valutare ed eseguire la migrazione dei server**.

   ![Pulsante per valutare ed eseguire la migrazione dei server](./media/tutorial-assess-vmware/assess-migrate.png)

1. In **Attività iniziali** selezionare **Aggiungi strumenti**.
1. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.     
1. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Sono supportate le aree Asia, Europa, Regno Unito e Stati Uniti.

   L'area geografica del progetto viene usata solo per archiviare i metadati raccolti dalle macchine virtuali locali. Per la migrazione è possibile selezionare qualsiasi area di destinazione.

   ![Caselle per il nome del progetto e l'area](./media/tutorial-assess-vmware/migrate-project.png)

1. Selezionare **Avanti**.
1. In **Selezionare lo strumento di valutazione** selezionare **Azure Migrate: Valutazione server** > **Avanti**.

   ![Selezione per lo strumento Valutazione server](./media/tutorial-assess-vmware/assessment-tool.png)

1. In **Selezione strumento di migrazione** selezionare **Ignora l'aggiunta di uno strumento di migrazione per adesso** > **Avanti**.
1. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e selezionare **Aggiungi strumenti**.
1. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Verrà visualizzata la pagina del progetto. Se il progetto non viene visualizzato, è possibile accedervi da **Server** nel dashboard di Azure Migrate.

## <a name="set-up-the-appliance-vm"></a>Configurare l'appliance VM

Valutazione server di Azure Migrate esegue un'appliance macchina virtuale VMware leggera. L'appliance esegue l'individuazione delle macchine virtuali e raccoglie i metadati e i dati sulle prestazioni della macchina virtuale.

Per configurare l'appliance occorre:

- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

È possibile configurare più appliance per un singolo progetto di Azure Migrate. Tra tutte le appliance, Valutazione server supporta l'individuazione di un massimo di 35.000 macchine virtuali. Può individuare un massimo di 10.000 server per appliance.

### <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
1. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
1. Selezionare **Scarica** per scaricare il file modello OVA.

   ![Selezioni per il download di un file OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza:

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
1. Eseguire il comando seguente per generare l'hash per il file OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

Per la versione 2.19.07.30 l'hash generato deve corrispondere a questi valori:

**Algoritmo** | **Valore hash**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

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

### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che l'appliance VM sia in grado di connettersi agli [URL di Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Per configurare l'appliance, seguire questa procedura:

1. Nella console del client vSphere fare clic con il pulsante destro del mouse su sulla macchina virtuale e quindi scegliere **Open Console** (Apri console).
1. Specificare la lingua, il fuso orario e la password per l'appliance.
1. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance selezionando il relativo collegamento.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
   - **License** (Licenza): Accettare le condizioni di licenza e leggere le informazioni di terze parti.
   - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
     - Fare clic su **Impostazioni proxy** e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - Si noti che è supportato solo il proxy HTTP.
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
1. In **Nome utente** e **Password** specificare le credenziali dell'account del server vCenter che verranno usate dall'appliance per individuare le macchine virtuali nell'istanza del server vCenter. 

   Verificare che l'account abbia le [autorizzazioni necessarie per l'individuazione](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). È possibile [ridurre l'ambito dell'individuazione](tutorial-assess-vmware.md#set-the-scope-of-discovery) limitando l'accesso all'account vCenter.
1. Selezionare **Convalida connessione** per verificare che l'appliance possa connettersi al server vCenter.

### <a name="specify-vm-credentials"></a>Specificare le credenziali della VM
Per individuare le applicazioni, i ruoli, le funzionalità e visualizzare le dipendenze delle macchine virtuali, è possibile specificare le credenziali delle macchine virtuali che hanno accesso alle macchine virtuali VMware. È possibile aggiungere credenziali per le macchine virtuali Windows e per le macchine virtuali Linux. [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sulle autorizzazioni di accesso necessarie.

> [!NOTE]
> Questo input è facoltativo, ma è necessario per abilitare l'individuazione delle applicazioni e la visualizzazione delle dipendenze senza agente.

1. In **Individua applicazioni e dipendenze nelle macchine virtuali** selezionare **Aggiungi credenziali**.
1. Effettuare una selezione per **Sistema operativo**.
1. Specificare un nome descrittivo per le credenziali.
1. In **nome utente** e **password**specificare un account che disponga almeno dell'accesso guest nelle macchine virtuali.
1. Selezionare **Aggiungi**.

Dopo aver specificato l'istanza del server vCenter e le credenziali delle macchine virtuali (facoltativo), selezionare **Salva e avvia individuazione** per avviare l'individuazione dell'ambiente locale.

Per visualizzare i metadati delle VM individuate nel portale occorre attendere circa 15 minuti. L'individuazione di applicazioni, ruoli e funzionalità installati richiede tempo. La durata dipende dal numero di macchine virtuali individuate. Per le macchine virtuali 500, la visualizzazione dell'inventario delle applicazioni nel portale di Azure Migrate richiede circa 1 ora.

### <a name="set-the-scope-of-discovery"></a>Impostare l'ambito dell'individuazione

È possibile definire l'ambito dell'individuazione limitando l'accesso dell'account vCenter usato per l'individuazione. L'ambito può essere impostato su data center, cluster, una cartella di cluster, host, una cartella di host o singole macchine virtuali del server vCenter.

Per impostare l'ambito, seguire queste procedure.

#### <a name="1-create-a-vcenter-user-account"></a>1. Creare un account utente vCenter
1.  Accedere al client Web vSphere come amministratore del server vCenter.
1.  Selezionare **Administration** > **SSO users and Groups** (Amministrazione > Utenti e gruppi SSO) e quindi selezionare la scheda **Users** (Utenti).
1.  Selezionare l'icona **New User** (Nuovo utente).
1.  Immettere le informazioni necessarie per creare un nuovo utente e fare clic su **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. Definire un nuovo ruolo con le autorizzazioni necessarie
Questa procedura è necessaria per la migrazione del server senza agente.
1.  Accedere al client Web vSphere come amministratore del server vCenter.
1.  Passare ad **Administration** > **Role Manager** (Amministrazione > Gestione ruoli).
1.  Selezionare l'istanza del server vCenter dal menu a discesa.
1.  Selezionare **Create role** (Crea ruolo).
1.  Immettere un nome per il nuovo ruolo, ad esempio <em>Azure_Migrate</em>.
1.  Assegnare le [autorizzazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) al ruolo appena definito.
1.  Selezionare **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. Assegnare le autorizzazioni per gli oggetti vCenter

Sono disponibili due approcci per assegnare le autorizzazioni per gli oggetti inventario in vCenter all'account utente vCenter con un ruolo assegnato.

Per Valutazione server, è necessario applicare il ruolo **Read-only** (sola lettura) all'account utente vCenter per tutti gli oggetti padre in cui sono ospitate le macchine virtuali da individuare. Tutti gli oggetti padre (host, cartella di host, cluster, cartella di cluster) nella gerarchia fino al data center verranno inclusi. Queste autorizzazioni verranno propagate agli oggetti figlio nella gerarchia.

Allo stesso modo, per la migrazione del server, è necessario applicare un ruolo definito dall'utente con le [autorizzazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) per l'account utente vCenter per tutti gli oggetti padre che ospitano le macchine virtuali di cui verrà eseguita la migrazione. Questo ruolo può essere denominato <em>Azure_Migrate</em>.

![Assegnare le autorizzazioni](./media/tutorial-assess-vmware/assign-perms.png)

L'approccio alternativo consiste nell'assegnare l'account e il ruolo utente al livello del data center e quindi propagarli agli oggetti figlio. Assegnare quindi all'account un ruolo **No access** (nessun accesso) per ogni oggetto, ad esempio una macchina virtuale, da escludere dall'individuazione/migrazione. 

Questa configurazione alternativa è complessa e al tempo stesso espone a controlli di accesso accidentali, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'oggetto padre. È quindi consigliabile usare il primo approccio.

> [!NOTE]
> Attualmente lo strumento Valutazione server non è in grado di individuare le macchine virtuali se all'account vCenter è stato concesso l'accesso al livello della cartella delle macchine virtuali vCenter. Per definire l'ambito di individuazione in base alle cartelle delle macchine virtuali, seguire questa procedura. Garantisce che all'account vCenter sia assegnato l'accesso in sola lettura a livello di macchina virtuale.
>
> 1. Assegnare autorizzazioni di sola lettura su tutte le VM nelle cartelle a cui si vuole limitare l'ambito dell'individuazione.
> 1. Concedere l'accesso in sola lettura a tutti gli oggetti padre in cui sono ospitate le VM. Verranno inclusi tutti gli oggetti padre (host, cartella di host, cluster, cartella di cluster) nella gerarchia fino al data center. Non è necessario propagare le autorizzazioni a tutti gli oggetti figlio.
> 1. Usare le credenziali per l'individuazione selezionando il data center come **Ambito raccolta**. La configurazione del controllo degli accessi in base al ruolo assicura che l'utente vCenter corrispondente possa accedere solo alle macchine virtuali specifiche del tenant.
>
> Si noti che sono supportate le cartelle di host e cluster.

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
