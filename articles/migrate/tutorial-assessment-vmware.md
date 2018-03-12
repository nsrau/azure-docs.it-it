---
title: Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure usando il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bbd08637894c43c543aeb8236f515e5ed9c5fc19
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
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


## <a name="prerequisites"></a>prerequisiti

- **VMware**: le macchine virtuali che si prevede di migrare devono essere gestite tramite il server vCenter versione 5.5, 6.0 o 6.5. Inoltre, è necessario che sia in esecuzione l'host ESXi versione 5.0 o versione successiva per distribuire la macchina virtuale dell'agente di raccolta. 
 
> [!NOTE]
> Il supporto per Hyper-V è in programma e sarà disponibile a breve. 

- **Account server vCenter**: è necessario un account di sola lettura per accedere al server vCenter. Azure Migrate usa questo account per individuare le macchine virtuali.Azure Migrate usa questo account per individuare le macchine virtuali locali.
- **Autorizzazioni**: nel server vCenter è necessario avere le autorizzazioni per creare una macchina virtuale importando un file con estensione ova. 
- **Impostazioni delle statistiche**: prima di avviare la distribuzione, le statistiche del server vCenter devono essere impostate sul livello 3. Con un livello inferiore a 3, viene eseguita la valutazione, ma non vengono raccolti i dati sulle prestazioni per l'archiviazione e la rete. In questo caso le dimensioni consigliate verranno stabilite in base ai dati sulle prestazioni per i dati di CPU, memoria e configurazione per le schede disco e di rete. 

## <a name="create-an-account-for-vm-discovery"></a>Creare un account per l'individuazione di macchine virtuali

Azure Migrate deve avere accesso ai server VMware per l'individuazione automatica delle VM per la valutazione. Creare un account VMware con le proprietà seguenti. Questo account viene specificato durante la configurazione di Azure Migrate.

- Tipo di utente: almeno un utente di sola lettura
- Autorizzazioni: Data Center object (Oggetto data center) > Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura)
- Dettagli: l'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.
- Per limitare l'accesso, assegnare il ruolo No access (Nessun accesso) con Propagate to child object (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Creare un progetto

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Cercare **Azure Migrate** e selezionare il servizio **Azure Migrate** nei risultati della ricerca. Fare quindi clic su **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure per il progetto.
4. Creare un nuovo gruppo di risorse.
5. Specificare il percorso in cui creare il progetto e quindi fare clic su **Crea**. È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali. Tuttavia, è comunque possibile pianificare la migrazione per qualsiasi località di Azure di destinazione. Il percorso specificato per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e si importa il file nel server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare il file con estensione ova.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

    ![Scaricare il file con estensione ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file con estensione ova sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni.

    Per OVA versione 1.0.9.5

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  
    
    Per OVA versione 1.0.9.2

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  
    
    Per OVA versione 1.0.8.59

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    Per OVA versione 1.0.8.49:
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Per OVA versione 1.0.8.40:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

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
4. In Agente di raccolta di Azure Migrate aprire **Set up prerequisites** (Configura prerequisiti).
    - Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - L'agente di raccolta verifica che la macchina virtuale abbia accesso a Internet.
    - Se la macchina virtuale accede a Internet tramite un proxy, fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy. Se il proxy richiede l'autenticazione, specificare le credenziali.

    > [!NOTE]
    > L'indirizzo proxy deve essere immesso nel formato http://ProxyIPAddress o http://ProxyFQDN. È supportato solo il proxy HTTP.

    - L'agente di raccolta verifica che il servizio dell'agente di raccolta sia in esecuzione. Il servizio è installato per impostazione predefinita nella macchina virtuale dell'agente di raccolta.
    - Scaricare e installare VMware PowerCLI.

5. In **Specify vCenter Server details** (Specificare i dettagli del Server vCenter) eseguire queste operazioni:
    - Specificare il nome completo o l'indirizzo IP del server vCenter.
    - In **User name** (Nome utente) e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le macchine virtuali nel server vCenter.
    - In **Collection scope** (Ambito raccolta) selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1000 macchine virtuali. 

6. In **Specify migration project** (Specificare il progetto di migrazione) specificare l'ID e la chiave del progetto di Azure Migrate copiati dal portale. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto fare clic su **Individua macchine virtuali** e copiare i valori.  
7. In **View collection progress** (Visualizza stato raccolta) monitorare l'individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.

> [!NOTE]
> Come lingua del sistema operativo e lingua dell'interfaccia dell'agente di raccolta è supportato solo l'inglese (Stati Uniti). Sarà presto disponibile il supporto per altre lingue.


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Il tempo di individuazione dipende dal numero di macchine virtuali da individuare. In genere, per completare l'individuazione di 100 macchine virtuali, è necessario aspettare circa un'ora dopo l'esecuzione dell'agente di raccolta. 

1. Nel progetto di Azure Migrate fare clic su **Gestisci** > **Macchine virtuali**.
2. Verificare che le macchine virtuali da individuare siano visualizzate nel portale.


## <a name="create-and-view-an-assessment"></a>Creare e visualizzare una valutazione

Dopo aver individuato le macchine virtuali, è possibile raggrupparle e creare una valutazione. 

1. Nella pagina **Panoramica** del progetto fare clic su **+Crea valutazione**.
2. Fare clic su **Visualizza tutto** per rivedere le proprietà di valutazione.
3. Creare il gruppo e specificarne il nome.
4. Selezionare le macchine virtuali da aggiungere al gruppo.
5. Fare clic su **Crea valutazione** per creare il gruppo e la valutazione.
6. Dopo aver creato la valutazione, visualizzarla in **Panoramica** > **Dashboard**.
7. Fare clic su **Esporta valutazione** per scaricarla come file di Excel.

### <a name="assessment-details"></a>Dettagli valutazione

Una valutazione include informazioni sulla compatibilità o meno delle macchine virtuali locali per Azure, sulle dimensioni di macchina virtuale più appropriate in Azure e sui costi di Azure mensili stimati. 

![Report di valutazione](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Idoneità per Azure

La visualizzazione Idoneità per Azure nella valutazione mostra lo stato di idoneità di ogni macchina virtuale. A seconda delle proprietà della macchina virtuale, ogni macchina virtuale può essere contrassegnata come:
- Idonea per Azure
- Idonea con condizioni
- Non idonea per Azure
- Idoneità sconosciuta 

Per le macchine virtuali idonee, Azure Migrate offre un consiglio riguardo alle dimensioni delle macchine in Azure. L'indicazione relativa alle dimensioni fornita da Azure Migrate dipende dal criterio di ridimensionamento specificato nelle proprietà della valutazione. Se il criterio è il ridimensionamento in base alle prestazioni, l'indicazione relativa alle dimensioni prende in considerazione la cronologia delle prestazioni delle macchine virtuali. Se il criterio è il ridimensionamento come in locale, l'indicazione prende in considerazione la dimensione della macchina virtuale in locale (ridimensionamento così com'è). In questo caso, i dati sull'utilizzo non vengono considerati. [Altre informazioni](concepts-assessment-calculation.md) su come viene eseguito il ridimensionamento in Azure Migrate. 

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

Ogni valutazione in Azure Migrate è associata a una classificazione di attendibilità compresa tra 1 stella e 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle corrispondono al livello massimo. La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione. La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate. 

La classificazione di attendibilità è utile quando si esegue un *ridimensionamento in base alle prestazioni*, in quanto Azure Migrate potrebbe non avere a disposizione punti dati sufficienti per eseguire il ridimensionamento in base all'uso. Per un *ridimensionamento come in locale*, la classificazione di attendibilità è sempre 5 stelle, perché Azure Migrate ha a disposizione tutti i dati necessari per ridimensionare la macchina virtuale. 

Per un ridimensionamento della macchina virtuale in base alle prestazioni, Azure Migrate deve avere a disposizione i dati sull'uso di CPU e memoria. Per ogni disco collegato alla macchina virtuale, il servizio deve anche avere i dati relativi alle operazioni di I/O al secondo in lettura/scrittura e alla velocità effettiva. Analogamente, per ogni scheda di rete collegata alla VM, Azure Migrate deve disporre dei dati sull'ingresso/uscita di rete per applicare la determinazione della dimensione in base alle prestazioni. Se una qualsiasi delle cifre sull'utilizzo indicate sopra non è disponibile nel server vCenter, l'indicazione relativa alla dimensione fornita da Azure Migrate potrebbe non essere affidabile. Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

Una valutazione può non avere a disposizione tutti i punti dati a causa di uno dei motivi seguenti:
- L'impostazione delle statistiche nel server vCenter non è pari al livello 3 e la valutazione usa il criterio di determinazione della dimensione in base alle prestazioni. Se l'impostazione delle statistiche nel server vCenter è inferiore al livello 3, i dati sulle prestazioni per i dischi e la rete non vengono raccolti da vCenter Server. In questo caso, l'indicazione fornita da Azure Migrate per i dischi e la rete non è basata sull'uso. Per l'archiviazione, Azure Migrate consiglia dischi standard, perché senza tenere conto delle operazioni di I/O al secondo e della velocità effettiva del disco, Azure Migrate non è in grado di identificare se per il disco sia necessario un disco Premium in Azure.
- L'impostazione delle statistiche nel server vCenter è stata configurata sul livello 3 per un periodo di tempo minore, prima di avviare il processo di individuazione. Si consideri ad esempio uno scenario in cui si cambia l'impostazione delle statistiche al livello 3 oggi e si avvia l'individuazione usando l'appliance dell'agente di raccolta domani (24 ore dopo). Se si crea una valutazione per un giorno, saranno disponibili tutti i punti dati e la classificazione di attendibilità della valutazione sarà 5 stelle. Se invece si modifica a un mese il periodo di tempo delle prestazioni nelle proprietà della valutazione, la classificazione di attendibilità diminuisce, perché i dati sulle prestazioni di dischi e rete per l'ultimo mese non sono disponibili. Per tenere conto dei dati sulle prestazioni dell'ultimo mese, è consigliabile mantenere l'impostazione delle statistiche del server vCenter sul livello 3 per un mese prima di avviare il processo di individuazione. 
- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se una o più VM sono state spente per un certo periodo di tempo, il server vCenter non avrà i dati sulle prestazioni per questo periodo. 
- All'interno del periodo per cui viene calcolata la valutazione sono state create alcune VM. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questi casi, la cronologia delle prestazioni delle nuove VM non sarà disponibile per l'intero periodo.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a 4 stelle, è consigliabile modificare le impostazioni delle statistiche del server vCenter al livello 3, attendere per il periodo che deve essere considerato nella valutazione (1 giorno/1 settimana/1 mese) e quindi eseguire l'individuazione e la valutazione. Se non è possibile applicare la soluzione indicata sopra, la determinazione della dimensione in base alle prestazioni potrebbe non essere affidabile ed è consigliabile passare a quella *come in locale* modificando le proprietà della valutazione.
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](how-to-scale-assessment.md) su come individuare e valutare un ambiente VMware di grandi dimensioni.
- Informazioni su come creare gruppi di valutazione con affidabilità elevata usando il [mapping delle dipendenze delle macchine virtuali](how-to-create-group-machine-dependencies.md)
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
