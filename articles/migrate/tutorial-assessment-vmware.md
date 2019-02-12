---
title: Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure usando il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dee649c388ee1e9207d1fc0ecb454d03cda304b0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730765"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure

Il servizio [Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account usato da Azure Migrate per individuare le macchine virtuali locali
> * Creare un progetto di Azure Migrate.
> * Configurare una macchina virtuale locale dell'agente di raccolta per individuare le macchine virtuali VMware locali per la valutazione.
> * Raggruppare le macchine virtuali e creare una valutazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- **VMware**: le macchine virtuali che si prevede di migrare devono essere gestite tramite il server vCenter versione 5.5, 6.0 o 6.5. Inoltre, è necessario che sia in esecuzione l'host ESXi versione 5.5 o versione successiva per distribuire la macchina virtuale dell'agente di raccolta.
- **Account server vCenter**: è necessario un account di sola lettura per accedere al server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: nel server vCenter è necessario avere le autorizzazioni per creare una macchina virtuale importando un file con estensione ova.

## <a name="create-an-account-for-vm-discovery"></a>Creare un account per l'individuazione di macchine virtuali

Azure Migrate deve avere accesso ai server VMware per l'individuazione automatica delle VM per la valutazione. Creare un account VMware con le proprietà seguenti. Questo account viene specificato durante la configurazione di Azure Migrate.

- Tipo di utente: Almeno un utente in sola lettura
- Autorizzazioni: Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura)
- Dettagli: L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.
- Per limitare l'accesso, assegnare il ruolo No access (Nessun accesso) con Propagate to Child Object (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, macchine virtuali e reti).


## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Creare un progetto

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Cercare **Azure Migrate** e selezionare il servizio **Azure Migrate** nei risultati della ricerca. Fare quindi clic su **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare l'area geografica in cui creare il progetto e quindi fare clic su **Crea**. È possibile creare un progetto Azure Migrate solo nelle aree geografiche seguenti. Tuttavia, è comunque possibile pianificare la migrazione per qualsiasi località di Azure di destinazione. L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali.

**Area geografica** | **Posizione di archiviazione**
--- | ---
Azure Government | US Gov Virginia
Asia | Asia sud-orientale
Europa | Europa settentrionale o Europa occidentale
Stati Uniti | Stati Uniti orientali o Stati Uniti centro-occidentali

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e si importa il file nel server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare l'appliance.

    L'appliance di Azure Migrate comunica con il server vCenter e profila in modo continuo l'ambiente locale per raccogliere i dati sull'utilizzo in tempo reale per ogni macchina virtuale. L'appliance raccoglie i contatori di picco per ogni metrica (utilizzo della CPU, utilizzo della memoria e così via). Questo modello non dipende dalle impostazioni delle statistiche del server vCenter per la raccolta dei dati sulle prestazioni. È possibile interrompere la profilatura continua in qualsiasi momento dall'appliance.

    > [!NOTE]
    > L'appliance per l'individuazione una tantum è ora deprecata poiché questo metodo si basava sulle impostazioni delle statistiche del server vCenter relative alla disponibilità dei punti dati delle prestazioni e raccoglieva i contatori delle prestazioni medie, determinando così un sottodimensionamento delle macchine virtuali per la migrazione ad Azure.

    **Valutazioni rapide:** con l'appliance per l'individuazione continua, è possibile creare le valutazioni subito dopo che l'individuazione è completata. Il processo di individuazione richiede circa due ore, a seconda del numero di macchine virtuali. Poiché la raccolta dei dati sulle prestazioni inizia quando viene avviata l'individuazione, se si vogliono ottenere valutazioni rapide, è necessario selezionare *Come in locale* come criterio di dimensionamento per la valutazione. Per le valutazioni basate sulle prestazioni, è consigliabile attendere almeno un giorno dall'avvio del processo di individuazione per ottenere indicazioni affidabili relative alle dimensioni.

    L'appliance si limita a raccogliere i dati sulle prestazioni in modo continuo, non rileva eventuali modifiche alla configurazione nell'ambiente locale, ad esempio l'aggiunta e l'eliminazione di macchine virtuali, l'aggiunta di dischi e così via. Se si esegue una modifica della configurazione nell'ambiente locale, è possibile procedere come segue per riflettere le modifiche nel portale:

    - Aggiunta di elementi (macchine virtuali, dischi, core e così via): per riflettere tali modifiche nel portale di Azure, è possibile arrestare l'individuazione dall'appliance e quindi riavviarla. Ciò garantisce che le modifiche vengono aggiornate nel progetto Azure Migrate.

    - Eliminazione di macchine virtuali: a causa del modo in cui è progettata l'appliance, l'eliminazione di macchine virtuali non viene rilevata anche se si arresta e riavvia l'individuazione. I dati acquisiti dalle individuazioni successive vengono infatti aggiunti alle individuazioni precedenti e non sostituiti. In questo caso è possibile semplicemente ignorare la macchina virtuale nel portale, rimuovendola dal gruppo e ricalcolando la valutazione.


3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

    ![Scaricare il file con estensione ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file con estensione ova sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni.

#### <a name="continuous-discovery"></a>Individuazione continua

  Per OVA versione 1.0.10.11

  **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  Per OVA versione 1.0.10.9

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  Per OVA versione 1.0.10.4

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Individuazione una tantum (ora deprecata)

Questo modello è ora deprecato. Verrà fornito il supporto per le appliance esistenti.

  Per OVA versione 1.0.9.15

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Per OVA versione 1.0.9.14

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Per OVA versione 1.0.9.12

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

## <a name="create-the-collector-vm"></a>Creare la macchina virtuale dell'agente di raccolta

Importare il file scaricato nel server vCenter.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Distribuire il modello OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. In Deploy OVF Template Wizard (Distribuzione guidata del modello OVF) > **Source** (Origine) specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la macchina virtuale dell'agente di raccolta e l'oggetto dell'inventario in cui verrà ospitata la macchina virtuale.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale dell'agente di raccolta.
7. Nell'area relativa ai dati di archiviazione specificare la destinazione di archiviazione per la macchina virtuale dell'agente di raccolta.
8. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
9. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure.
10. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).

## <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop fare clic sul collegamento **Run collector** (Esegui agente di raccolta).
4. Fare clic su **Check for updates** (Controlla aggiornamenti) nella barra superiore dell'interfaccia utente dell'agente di raccolta e verificare che sia in esecuzione la versione più recente dell'agente di raccolta. In caso contrario, è possibile scegliere di scaricare il pacchetto di aggiornamento più recente dal collegamento e aggiornare l'agente di raccolta.
5. In Agente di raccolta di Azure Migrate aprire **Set up prerequisites** (Configura prerequisiti).
    - Selezionare il cloud di Azure in cui si intende eseguire la migrazione (Azure globale o Azure per enti pubblici).
    - Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
    - Se la macchina virtuale accede a Internet tramite un proxy, fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) sui requisiti di connettività Internet e l'[elenco di URL](https://docs.microsoft.com/azure/migrate/concepts-collector#connect-to-urls) ai quali accede l'agente di raccolta.

      > [!NOTE]
      > È necessario immettere l'indirizzo proxy nel formato http://ProxyIPAddress o http://ProxyFQDN. È supportato solo il proxy HTTP. Se si ha un proxy di intercettazione, inizialmente la connessione Internet potrebbe non riuscire se non è stato importato il certificato proxy. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-collector) sul modo in cui è possibile risolvere il problema importando il certificato proxy come certificato attendibile nella macchina virtuale dell'agente di raccolta.

    - L'agente di raccolta verifica che il servizio dell'agente di raccolta sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.
    - Scaricare e installare VMware PowerCLI.

6. In **Specify vCenter Server details** (Specificare i dettagli del Server vCenter) eseguire queste operazioni:
    - Specificare il nome completo o l'indirizzo IP del server vCenter.
    - In **User name** (Nome utente) e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Collection scope** (Ambito raccolta) selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1500 macchine virtuali. [Altre informazioni](how-to-scale-assessment.md) sul modo in cui è possibile individuare un ambiente più grande.

7. In **Specify migration project** (Specificare il progetto di migrazione) specificare l'ID e la chiave del progetto di Azure Migrate copiati dal portale. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto fare clic su **Individua macchine virtuali** e copiare i valori.  
8. In **Visualizza lo stato della raccolta** monitorare lo stato di individuazione. [Altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-collector) sul tipo di dati raccolti dall'agente di raccolta di Azure Migrate.

> [!NOTE]
> Come lingua del sistema operativo e lingua dell'interfaccia dell'agente di raccolta è supportato solo l'inglese (Stati Uniti).
> Se si modificano le impostazioni in un computer a cui si vuole accedere, attivare di nuovo l'individuazione prima di eseguire la valutazione. A questo scopo, usare l'opzione **Avvia di nuovo la raccolta** nell'agente di raccolta. Al termine della raccolta, selezionare l'opzione **Ricalcola** per la valutazione nel portale in modo da ottenere i risultati della valutazione aggiornati.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

L'appliance dell'agente di raccolta profilerà in modo continuo l'ambiente locale e continuerà a inviare i dati sulle prestazioni ogni ora. È possibile visualizzare le macchine virtuali nel portale dopo un'ora dall'avvio del processo di individuazione.

1. Nel progetto di migrazione fare clic su **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="create-and-view-an-assessment"></a>Creare e visualizzare una valutazione

Dopo aver individuato le macchine virtuali nel portale, è possibile raggrupparle e creare valutazioni. Le valutazioni basate su un criterio di dimensionamento come in locale possono essere create immediatamente, non appena vengono individuate le macchine virtuali nel portale. È invece consigliabile attendere almeno un giorno prima di creare valutazioni basate sulle prestazioni per ottenere indicazioni affidabili relative alle dimensioni.

1. Nella pagina **Panoramica** del progetto fare clic su **+Crea valutazione**.
2. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.
3. Creare il gruppo e specificarne il nome.
4. Selezionare le macchine virtuali da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo e la valutazione.
6. Dopo aver creato la valutazione, visualizzarla in **Panoramica** > **Dashboard**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.

> [!NOTE]
> Prima di creare una valutazione, è consigliabile attendere almeno un giorno dall'avvio del processo di individuazione. Se si desidera aggiornare una valutazione esistente con dati sulle prestazioni più recenti, è possibile usare il comando **Ricalcola** sulla valutazione per aggiornarla.

### <a name="assessment-details"></a>Dettagli valutazione

Una valutazione include informazioni sulla compatibilità o meno delle macchine virtuali locali per Azure, sulle dimensioni di macchina virtuale più appropriate in Azure e sui costi di Azure mensili stimati.

![Report di valutazione](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Idoneità per Azure

La visualizzazione Idoneità per Azure nella valutazione mostra lo stato di idoneità di ogni macchina virtuale. A seconda delle proprietà della macchina virtuale, ogni macchina virtuale può essere contrassegnata come:
- Idonea per Azure
- Idonea con condizioni
- Non idonea per Azure
- Idoneità sconosciuta

Per le macchine virtuali idonee, Azure Migrate offre un consiglio riguardo alle dimensioni delle macchine in Azure. L'indicazione relativa alle dimensioni fornita da Azure Migrate dipende dal criterio di ridimensionamento specificato nelle proprietà della valutazione. Se il criterio è il ridimensionamento in base alle prestazioni, l'indicazione relativa alle dimensioni prende in considerazione la cronologia delle prestazioni delle macchine virtuali (CPU e memoria) e dei dischi (operazioni di I/O al secondo e velocità effettiva). Se il criterio è il ridimensionamento come in locale, Azure Migrate non prende in considerazione i dati sulle prestazioni per la macchina virtuale e i dischi. L'indicazione per le dimensioni delle VM in Azure prende in considerazione le dimensioni della macchina virtuale in locale e il ridimensionamento dei dischi viene eseguito in base al tipo di archiviazione specificato nelle proprietà di valutazione (il valore predefinito è dischi Premium). [Altre informazioni](concepts-assessment-calculation.md) su come viene eseguito il ridimensionamento in Azure Migrate.

Per le macchine virtuali non idonee o idonee con condizioni, Azure Migrate descrive il problema correlato e indica i passaggi correttivi da eseguire.

Le macchine virtuali per cui Azure Migrate non è in grado di identificare l'idoneità per Azure (a causa della mancata disponibilità dei dati) sono contrassegnate con idoneità sconosciuta.

Oltre all'idoneità per Azure e al ridimensionamento, Azure Migrate consiglia anche gli strumenti che è possibile usare per la migrazione della macchina virtuale. Ciò richiede un'operazione di individuazione più avanzata nell'ambiente locale. Vedere [altre informazioni](how-to-get-migration-tool.md) su come eseguire un'individuazione più avanzata installando agenti nei computer locali. Se gli agenti non sono installati nei computer locali, è consigliabile la migrazione in modalità lift-and-shift con [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Se gli agenti sono installati nel computer locale, Azure Migrate esamina i processi in esecuzione nel computer e determina se il computer è un computer di database o meno. Se il computer è un computer di database, è consigliabile usare il [servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview), altrimenti è consigliabile usare Azure Site Recovery come strumento di migrazione.

  ![Idoneità per la valutazione](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Stima costo mensile

In questa visualizzazione viene mostrato il costo di calcolo e archiviazione totale dell'esecuzione delle macchine virtuali in Azure con i dettagli per ogni computer. Le stime dei costi vengono calcolate prendendo in considerazione le dimensioni consigliate da Azure Migrate per un computer, i suoi dischi e le proprietà di valutazione.

> [!NOTE]
> La stima dei costi fornita da Azure Migrate si riferisce all'esecuzione delle macchine virtuali locali come macchine virtuali dell'infrastruttura distribuita come servizio (IaaS) di Azure. In Azure Migrate non vengono considerati i costi relativi alla piattaforma distribuita come servizio (PaaS, Platform as a Service) o al software come un servizio (SaaS, Software as a Service).

I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo.

![Valutazione dei costi delle macchine virtuali](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Classificazione di attendibilità

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità compresa tra 1 stella e 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle corrispondono al livello massimo. La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione. La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate. La classificazione di attendibilità non è applicabile alle valutazioni locali "così come sono".

Per una determinazione delle dimensioni in base alle prestazioni, Azure Migrate deve avere a disposizione i dati sull'uso di CPU e memoria della macchina virtuale. Per ogni disco collegato alla macchina virtuale, il servizio deve anche avere i dati relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco. Analogamente, per ogni scheda di rete collegata a una VM, Azure Migrate deve disporre dei dati sull'ingresso/uscita di rete per applicare la determinazione delle dimensioni in base alle prestazioni. Se una qualsiasi delle cifre sull'utilizzo indicate sopra non è disponibile nel server vCenter, l'indicazione relativa alla dimensione fornita da Azure Migrate potrebbe non essere affidabile. Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili come riportato di seguito:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

Una valutazione può non avere a disposizione tutti i punti dati a causa di uno dei motivi seguenti:

- L'ambiente non è stato analizzato per il perioro di tempo per cui si sta creando la valutazione. Ad esempio, se si sta creando la valutazione con il periodo di tempo delle prestazioni impostato su 1 giorno, è necessario attendere almeno un giorno dopo avere avviato l'individuazione perché siano raccolti tutti i punti dati.

- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se una o più VM sono state spente per un certo periodo di tempo, non sarà possibile raccogliere i dati sulle prestazioni per questo periodo.

- All'interno del periodo per cui viene calcolata la valutazione sono state create alcune VM. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questi casi, la cronologia delle prestazioni delle nuove VM non sarà disponibile per l'intero periodo.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a 5 stelle, attendere almeno un giorno per consentire all'appliance di profilare l'ambiente e quindi *ricalcolare* la valutazione. Se non è possibile applicare la soluzione indicata sopra, la determinazione della dimensione in base alle prestazioni potrebbe non essere affidabile ed è consigliabile passare a quella *come in locale* modificando le proprietà della valutazione.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](how-to-modify-assessment.md) su come personalizzare una valutazione in base alle esigenze.
- Informazioni su come creare gruppi di valutazione con affidabilità elevata usando il [mapping delle dipendenze delle macchine virtuali](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
- [Informazioni](how-to-scale-assessment.md) su come individuare e valutare un ambiente VMware di grandi dimensioni.
- [Altre informazioni](resources-faq.md) sulle domande frequenti su Azure Migrate
