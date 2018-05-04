---
title: Valutare i carichi di lavoro locali per la migrazione ad Azure con DMA e Azure Migrate | Microsoft Docs
description: Informazioni su come preparare Azure per la migrazione di computer locali con Data Migration Assistant (DMA) e il servizio Azure Migrate.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33e31c47a6125ac363410a9a78e9c9310c74d51e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Scenario 1: Valutare i carichi di lavoro locali per la migrazione ad Azure

Prendendo in considerazione la migrazione ad Azure, Contoso vuole eseguire una valutazione tecnica e finanziaria per determinare se i carichi di lavoro locali dell'azienda sono idonei per la migrazione al cloud. In particolare, l'azienda vuole valutare la compatibilità di computer e database ai fini della migrazione e stimare la capacità e i costi per l'esecuzione delle risorse in Azure.

Per acquisire familiarità e conoscere meglio le tecnologie coinvolte, l'azienda esegue la valutazione e la migrazione di una piccola app di viaggio locale. È un'app a due livelli, con un'app Web in esecuzione in una VM e un database di SQL Server in una seconda VM. L'applicazione viene distribuita in VMware e l'ambiente è gestito da un server vCenter. La valutazione verrà eseguita usando Data Migration Assistant (DMA) e il servizio Azure Migrate.

**Technology** | **Descrizione** | **Costii**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA valuta e rileva i problemi di compatibilità che possono influire sulle funzionalità del database in Azure. Valuta anche la parità delle funzionalità tra l'origine e la destinazione SQL Server e consiglia miglioramenti a livello di prestazioni e affidabilità per l'ambiente di destinazione. | Questo strumento è scaricabile gratuitamente. 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Il servizio consente di valutare i computer locali per la migrazione ad Azure. Valuta l'idoneità dei computer per la migrazione e offre stime relative al dimensionamento e ai costi per l'esecuzione in Azure. Il servizio Azure Migrate può attualmente valutare VM VMware locali per la migrazione ad Azure. | Attualmente (aprile 2018) non è previsto alcun addebito per l'uso del servizio.
[Elenco dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate usa Mapping dei servizi per mostrare le dipendenze tra i computer di cui si vuole eseguire la migrazione. |  Mapping dei servizi fa parte di Azure Log Analytics. Attualmente può essere usato senza addebito di costi per 180 giorni. 

In questo scenario, verrà scaricato ed eseguito DMA per valutare il database di SQL Server locale per l'app di viaggio e si userà Azure Migrate con il mapping delle dipendenze per valutare le VM dell'app prima di eseguirne la migrazione ad Azure.

> [!NOTE]
> Per questo scenario, la destinazione del database per la valutazione sarà SQL Server in una VM di Azure. Nell'articolo sullo scenario successivo, invece, verrà eseguita la migrazione a un'istanza gestita di SQL di Azure. Viene usato questo approccio perché DMA attualmente non supporta la valutazione per una destinazione costituita da un'istanza gestita di SQL di Azure.

## <a name="architecture"></a>Architecture

Elementi configurati in questo scenario 

 ![Architettura per la valutazione della migrazione](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

In questo scenario:
- Contoso ha un data center locale (**contoso-datacenter**) con un controller di dominio locale (**contosodc1**).
- L'app di viaggio interna è suddivisa in livelli in due VM, **WEBVM** e **SQLVM**, e risiede nell'host VMware ESXi **contosohost1.contoso.com**.
- L'ambiente VMware è gestito da un server vCenter (**vcenter.contoso.com**) in esecuzione in una VM.




## <a name="prerequisites"></a>prerequisiti

Per distribuire questo scenario è necessario quanto segue:

- Server vCenter locale che esegue la versione 5.5, 6.0 o 6.5.
- Account di sola lettura nel server vCenter o autorizzazioni per crearne uno. 
- Autorizzazioni per creare una VM nel server vCenter con un modello OVA.
- Almeno un host ESXi che esegue la versione 5.0 o una successiva.
- Almeno due VM VMware locali, in una delle quali viene eseguito un database di SQL Server.
- Autorizzazioni per installare gli agenti di Azure Migrate in ogni VM.
- Connettività diretta a Internet delle VM.
        - È possibile limitare l'accesso a Internet agli [URL necessari](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Se sono presenti computer senza connettività a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) in tali computer.
- Nome di dominio completo della VM che esegue l'istanza di SQL Server, per la valutazione del database.
- Windows Firewall in esecuzione nella VM di SQL Server che consente le connessioni esterne sulla porta TCP 1433 (predefinita), affinché DMA possa connettersi.


## <a name="scenario-overview"></a>Panoramica dello scenario

Verranno eseguite le operazioni seguenti.


> [!div class="checklist"]
> * **Passaggio 1: Preparare Azure**. È sufficiente una sottoscrizione di Azure.
> * **Passaggio 2: Scaricare e installare DMA**. Preparare DMA per la valutazione del database di SQL Server locale.
> * **Passaggio 3: Valutare il database**. Eseguire e analizzare la valutazione del database.
> * **Passaggio 4: Preparare la valutazione delle VM con Azure Migrate**. Configurare gli account locali e perfezionare le impostazioni di VMware.
> * **Passaggio 5: Individuare le VM locali**. Creare una VM dell'agente di raccolta di Azure Migrate. Eseguire quindi l'agente di raccolta per individuare le VM per la valutazione.
> * **Passaggio 6: Preparare l'analisi delle dipendenze**. Installare gli agenti di Azure Migrate nelle VM per poter verificare il mapping delle dipendenze tra le VM.
> * **Passaggio 7: Valutare le VM**. Controllare le dipendenze, raggruppare le VM ed eseguire la valutazione. Quando la valutazione è pronta, analizzarla in preparazione della migrazione.


## <a name="step-1-prepare-azure"></a>Passaggio 1: Preparare Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.
- Se si usa una sottoscrizione esistente e non si è l'amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore per la sottoscrizione o per il gruppo di risorse usato per questo scenario.


## <a name="step-2-download-and-install-the-dma"></a>Passaggio 2: Scaricare e installare DMA

1. Scaricare DMA dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - È possibile installare Assistant in qualsiasi computer che possa connettersi all'istanza di SQL. Non è necessario che venga eseguito nel computer di SQL Server.
    - Non è consigliabile eseguirlo nel computer host di SQL Server.
2. Fare doppio clic sul file di installazione scaricato (DownloadMigrationAssistant.msi) per avviare l'installazione.
3. Nella pagina **Finish** (Fine) verificare che sia selezionata l'opzione **Launch Microsoft Data Migration Assistant** (Avvia Microsoft Data Migration Assistant) e fare clic su **Finish** (Fine).

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Passaggio 3: Eseguire e analizzare la valutazione del database

Eseguire una valutazione per analizzare l'istanza di SQL Server di origine rispetto a una destinazione specificata.

1. In **New** (Nuovo) selezionare **Assessment** (Valutazione) e assegnare un nome di progetto alla valutazione.
2. In **Source server type** (Tipo server di origine) selezionare **SQL Server**. In **Target server type** (Tipo server di destinazione) selezionare **SQL Server on Azure Virtual Machines** (SQL Server in macchine virtuali di Azure).

    ![Selezionare l'origine](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      Attualmente DMA non supporta la valutazione per la migrazione a un'istanza gestita di SQL. Come soluzione alternativa, si supporrà che la destinazione per la valutazione sia SQL Server in una VM di Azure.

1.  In **Select Target Version** (Seleziona versione di destinazione) specificare la versione di destinazione di SQL Server da eseguire in Azure e che cosa si vuole individuare nella valutazione:
    - **Compatibility Issues** (Problemi di compatibilità) indica le modifiche che potrebbero causare un'interruzione nella migrazione o che richiedono una modifica secondaria prima della migrazione. Segnala anche le eventuali funzionalità attualmente in uso che sono state deprecate. I problemi sono organizzati per livello di compatibilità. 
    - **New features' recommendation** (Nuove funzionalità consigliate) consente di conoscere le nuove funzionalità della piattaforma SQL Server di destinazione che potranno essere usate per il database dopo la migrazione, organizzate nelle categorie Performance (Prestazioni), Security (Sicurezza) e Storage (Archiviazione). 

    ![Selezionare la destinazione](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. In **Connect to a server** (Connetti a un server) specificare il nome del computer che esegue l'istanza di SQL Server, il tipo di autenticazione e i dettagli della connessione. Fare clic su **Connetti**.

    ![Selezionare la destinazione](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. In **Add source** (Aggiungi origine) selezionare il database che si vuole valutare e fare clic su **Add** (Aggiungi).
4. Viene creata una valutazione con il nome specificato.

    ![Creare la valutazione](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Fare clic su **Next** (Avanti) per avviare la valutazione.
6. In **Review Results** (Verifica risultati) verranno visualizzati i risultati dei test di valutazione abilitati.


### <a name="analyze-the-database-assessment"></a>Analizzare la valutazione del database

I risultati vengono visualizzati in Assistant non appena disponibili. 

1. Nel report **Compatibility Issues** (Problemi di compatibilità) controllare se il database presenta problemi per ogni livello di compatibilità e, in tal caso, come risolverli. I livelli di compatibilità sono associati alle versioni di SQL Server come indicato di seguito.
    - 100: SQL Server 2008/database SQL di Azure
    - 110: SQL Server 2012/database SQL di Azure
    - 120: SQL Server 2014/database SQL di Azure
    - 130: SQL Server 2016/database SQL di Azure
    - 140: SQL Server 2017/database SQL di Azure

    ![Problemi di compatibilità](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. Nel report **Feature recommendations** (Funzionalità consigliate) visualizzare le funzionalità di archiviazione, sicurezza e prestazioni che la valutazione consiglia di configurare dopo la migrazione. Vengono consigliate diverse funzionalità, tra cui OLTP in memoria, columnstore, Stretch Database, Always Encrypted, Dynamic Data Masking e Transparent Data Encryption.

    ![Funzionalità consigliate](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Se si risolvono eventuali problemi, fare clic su **Restart Assessment** (Riavvia valutazione) per eseguire di nuovo la valutazione. 
4. Fare clic su **Export report** (Esporta report) per ottenere il report di valutazione in formato JSON o CSV.

Se si esegue una valutazione su scala più ampia:

- È possibile eseguire più valutazioni contemporaneamente e visualizzarne lo stato aprendo la pagina **All Assessments** (Tutte le valutazioni).
- È possibile [consolidare le valutazioni in un database di SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- È possibile [consolidare le valutazioni in un report di PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Passaggio 4: Preparare la valutazione delle VM con Azure Migrate

Creare un account VMware che verrà usato da Azure Migrate per individuare automaticamente le VM per la valutazione, verificare che l'utente abbia le autorizzazioni necessarie per creare una VM, rilevare le porte che devono essere aperte e impostare il livello delle impostazioni delle statistiche.

### <a name="set-up-a-vmware-account"></a>Configurare un account VMware

 È necessario un account di sola lettura in vCenter. Se non se ne ha uno, creare un account VMware con le proprietà seguenti.

- Tipo di utente: almeno un utente di sola lettura.
- Autorizzazioni: Data Center object (Oggetto data center) > Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura).
- Dettagli: l'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.
- Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

### <a name="verify-permissions-to-create-a-vm"></a>Verificare le autorizzazioni per creare una VM

Controllare di avere le autorizzazioni per creare una VM importando un file in formato OVA. [Altre informazioni](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificare le porte

In questo scenario verrà configurato il mapping delle dipendenze. Per questa funzionalità è necessario che nelle VM valutate sia installato un agente, che deve potersi connettere ad Azure dalla porta TCP 443 in ogni VM. Vedere [altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sui requisiti di connessione.


### <a name="set-statistics-settings"></a>Configurare le impostazioni delle statistiche

Prima di avviare la distribuzione, le statistiche per il server vCenter devono essere impostate sul livello 3. Si noti che:
- Dopo aver impostato il livello, è necessario attendere almeno un giorno prima di eseguire la valutazione. In caso contrario, potrebbe non funzionare come previsto.
- Se il livello è superiore a 3, la valutazione funziona, ma si verifica quanto segue:
    - I dati sulle prestazioni dei dischi e della rete non verranno raccolti.
    - Per l'archiviazione, Azure Migrate consiglierà un disco standard in Azure delle stesse dimensioni del disco locale.
    - Per la rete, per ogni scheda di rete locale verrà consigliata una scheda di rete in Azure.
    - Per il calcolo, Azure Migrate esaminerà le dimensioni della memoria e i core della VM e consiglierà una VM di Azure con la stessa configurazione. Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.
   
    
Vedere [altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sul dimensionamento con il livello 3.

Impostare il livello come descritto di seguito:

1. In vSphere Web Client aprire l'istanza del server vCenter.
2. Selezionare la scheda **Manage** (Gestisci) e in **Settings** (Impostazioni) fare clic su **General** (Generale).
3. Fare clic su **Edit** (Modifica) e in **Statistics** (Statistiche) impostare il livello delle statistiche su **Level 3** (Livello 3).

    ![Livello delle statistiche in vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Passaggio 5: Individuare le VM

Creare un progetto di Azure Migrate e scaricare e configurare la VM dell'agente di raccolta. Eseguire quindi l'agente di raccolta per individuare le VM.

### <a name="create-a-project"></a>Creare un progetto

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Crea una risorsa**.
2. Cercare **Azure Migrate**. Selezionare **Azure Migrate** nei risultati della ricerca e fare clic su **Crea**.
3. Specificare un nome di progetto e la sottoscrizione di Azure.
4. Creare un nuovo gruppo di risorse.
5. Specificare la località per il progetto e quindi fare clic su **Crea**.

    - È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali.
    - È possibile pianificare una migrazione per qualsiasi località di destinazione.
    - La località del progetto viene usata solo per archiviare i metadati raccolti dalle VM locali.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un file con estensione ova e si importa il file nel server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate fare clic su **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**.
2. In **Individua macchine virtuali** fare clic su **Scarica** per scaricare il file con estensione ova.
3. In **Copiare le credenziali del progetto** copiare l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

    ![Scaricare il file con estensione ova](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Verificare che il file con estensione ova sia sicuro prima di distribuirlo.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni (versione 1.0.9.7).
    
    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>Creare l'appliance dell'agente di raccolta

Importare il file scaricato nel server vCenter.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Distribuire il modello OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. In Deploy OVF Template Wizard (Distribuzione guidata del modello OVF) selezionare **Source** (Origine), specificare il percorso del file con estensione ova e fare clic su **Next** (Avanti).
3. In **OVF Template Details** (Dettagli modello OVF) fare clic su **Next** (Avanti). In **End User License Agreement** (Contratto di licenza con l'utente finale) fare clic su **Accept** (Accetta) per accettare il contratto e quindi su **Next** (Avanti).
4. In **Name and Location** (Nome e posizione) specificare un nome descrittivo per la VM dell'agente di raccolta e la posizione di inventario in cui verrà ospitata e fare clic su **Next** (Avanti). Specificare l'host o il cluster in cui verrà eseguita l'appliance dell'agente di raccolta.
5. In **Storage** (Archiviazione) specificare dove si vogliono archiviare i file per l'appliance e fare clic su **Next** (Avanti).
6. In **Disk Format** (Formato disco) specificare come si vuole effettuare il provisioning dello spazio di archiviazione.
7. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure. 
8. In **Ready to Complete** (Completamento) esaminare le impostazioni, selezionare **Power on after deployment** (Accendi al termine della distribuzione) e quindi fare clic su **Finish** (Fine).

Al termine della creazione dell'appliance verrà visualizzato un messaggio di conferma del completamento.

### <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Prima di iniziare, si noti che l'agente di raccolta attualmente supporta solo l'inglese (Stati Uniti) come lingua del sistema operativo e della propria interfaccia.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare le preferenze relative alla lingua, al fuso orario e alla password per l'appliance.
3. Sul desktop fare clic sul collegamento **Run collector** (Esegui agente di raccolta).

    ![Collegamento per l'agente di raccolta](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. In Agente di raccolta di Azure Migrate aprire **Set up prerequisites** (Configura prerequisiti).
    - Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - L'agente di raccolta controlla che la VM abbia accesso a Internet, che l'ora sia sincronizzata e che il servizio dell'agente di raccolta (installato per impostazione predefinita nella VM) sia in esecuzione. Installa anche VMWare PowerCLI. 
    
    > [!NOTE]
    > Si presume che la VM abbia accesso diretto a Internet, senza proxy.

    ![Verificare i prerequisiti](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. In **Specify vCenter Server details** (Specificare i dettagli del Server vCenter) eseguire queste operazioni:
    - Specificare il nome completo o l'indirizzo IP del server vCenter.
    - In **Nome utente** e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'agente di raccolta per individuare le VM nel server vCenter.
    - In **Ambito raccolta** selezionare un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1500 macchine virtuali. 

    ![Connettersi a vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. In **Specify migration project** (Specificare il progetto di migrazione) specificare l'ID e la chiave del progetto di Azure Migrate copiati dal portale. Se questi valori non sono stati copiati, aprire il portale di Azure dalla macchina virtuale dell'agente di raccolta. Nella pagina **Panoramica** del progetto fare clic su **Individua macchine virtuali** e copiare i valori.  

    ![Connect to Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. In **View collection progress** (Visualizza stato raccolta) monitorare l'individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.

    ![Raccolta in corso](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Al termine della raccolta, controllare che le VM vengano visualizzate nel portale.

1. Nel progetto di Azure Migrate fare clic su **Gestisci** > **Macchine virtuali**.
2. Controllare che le VM da individuare vengano visualizzate nella pagina.

    ![Macchine virtuali individuate](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Si noti che nei computer non sono attualmente installati gli agenti di Azure Migrate. È necessario installarli per poter visualizzare le dipendenze.
    
    ![Macchine virtuali individuate](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Passaggio 6: Preparare l'analisi delle dipendenze

Per visualizzare le dipendenze tra le VM da valutare, si scaricano e si installano gli agenti nelle VM dell'app Web, WEBVM e SQLVM.

### <a name="take-a-snapshot"></a>Creare uno snapshot

Per avere una copia della VM prima della modifica, creare uno snapshot prima di installare gli agenti.

![Snapshot del computer](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

1.  Nella pagina **Macchine virtuali** del progetto di Azure Migrate selezionare il computer e fare clic su **Richiede l'installazione** nella colonna **Dipendenze**.
2.  Nella pagina **Individua macchine virtuali** scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent per ogni VM.
3.  Copiare l'ID e la chiave dell'area di lavoro. Saranno necessari durante l'installazione di MMA.

    ![Download degli agenti](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>Installare MMA

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere la cartella di installazione predefinita e quindi fare clic su **Avanti**. 
4. In **Opzioni di installazione dell'agente** selezionare **Connect the agent to Azure Log Analytics** (Connetti l'agente ad Azure Log Analytics) > **Avanti**. 

    ![Installazione di MMA](./media/migrate-scenarios-assessment/mma-install.png) 
5. In **Azure Log Analytics** incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.
    ![Installazione di MMA](./media/migrate-scenarios-assessment/mma-install2.png) 

6. In **Pronto per l'installazione** installare MMA.



#### <a name="install-the-dependency-agent"></a>Installare Dependency Agent

1.  Fare doppio clic sul file di Dependency Agent scaricato.
2.  Nella pagina **License Terms** (Condizioni di licenza) fare clic su **I Agree to accept the license** (Accetto la licenza).
3.  In **Installing** (Installazione in corso) attendere il completamento dell'installazione. Quindi fare clic su **Next**.

    ![Dependency Agent](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Passaggio 7: Eseguire e analizzare la valutazione delle VM

Verificare le dipendenze dei computer e creare un gruppo. Eseguire quindi la valutazione.

### <a name="verify-dependencies-and-create-a-group"></a>Verificare le dipendenze e creare un gruppo

1.  Nella pagina **Macchine virtuali** fare clic su **Visualizza dipendenze** per le VM da analizzare.

    ![Visualizzare le dipendenze dei computer](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. Per SQLVM, la mappa delle dipendenze mostra i dettagli seguenti:

    - Processi/gruppi di processi con connessioni di rete attive in esecuzione in SQLVM durante il periodo di tempo specificato (per impostazione predefinita, un'ora)
    - Connessioni TCP in ingresso (client) e in uscita (server) da e verso tutti i computer dipendenti
    - Computer dipendenti in cui sono installati gli agenti di Azure Migrate, in caselle separate
    - Computer in cui non sono installati gli agenti, con le informazioni relative a porta e indirizzo IP
    
 3. Per i computer in cui è installato l'agente (WEBVM), fare clic sulla relativa casella per visualizzare altre informazioni, come nome di dominio completo, sistema operativo e indirizzo MAC. 

    ![Visualizzazione delle dipendenze del gruppo](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Selezionare quindi le VM da aggiungere al gruppo (SQLVM e WEBVM).  Usare CTRL+clic per selezionare più VM.
5. Fare clic su **Crea gruppo** e specificare un nome (smarthotelapp).

> [!NOTE]
    > Per visualizzare le dipendenze a un livello più dettagliato, si può espandere l'intervallo di tempo. È possibile selezionare una durata specifica oppure le date di inizio e di fine. 


### <a name="run-an-assessment"></a>Eseguire una valutazione


1. Nella pagina **Gruppi**  aprire il gruppo smarthotelapp.
2. Fare clic su **Crea valutazione**.

    ![Creare una valutazione](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. La valutazione viene visualizzata nella pagina **Gestisci** > **Valutazioni**.


### <a name="modify-assessment-settings"></a>Modificare le impostazioni di valutazione

Per questa esercitazione sono state usate le impostazioni di valutazione predefinite, ma è possibile personalizzare le impostazioni come segue:

1. Nella pagina **Valutazioni** del progetto di migrazione selezionare la valutazione e fare clic su **Modifica proprietà**.
2. Modificare le proprietà in base alla tabella seguente:

    **Impostazione** | **Dettagli** | **Default**
    --- | --- | ---
    **Posizione di destinazione** | Località di Azure verso cui si vuole eseguire la migrazione. | Nessuna impostazione predefinita.
    **Ridondanza dell'archiviazione** | Tipo di ridondanza dell'archiviazione che le macchine virtuali di Azure useranno dopo la migrazione. | Il valore predefinito è [Archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md). Questa opzione è determinata dal fatto che Azure Migrate supporta solo valutazioni basate su dischi gestiti e questi ultimi supportano solo l'archiviazione con ridondanza locale. 
    **Criterio di dimensionamento** | Criterio che Azure Migrate deve usare per definire correttamente le dimensioni delle macchine virtuali per Azure. È possibile eseguire il dimensionamento *basato sulle prestazioni* o definire le dimensioni delle macchine virtuali *come in locale*, senza considerare la cronologia delle prestazioni. | L'opzione predefinita è il dimensionamento basato sulle prestazioni.
    **Cronologia delle prestazioni** | Durata da considerare per la valutazione delle prestazioni delle macchine virtuali. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*. | Il valore predefinito è un giorno.
    **Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.  | Il valore predefinito è 95° percentile.
    **Piano tariffario** | È possibile specificare il [piano tariffario (Basic o Standard)](../virtual-machines/windows/sizes-general.md) per le macchine virtuali di Azure di destinazione. Se, ad esempio, si prevede di eseguire la migrazione di un ambiente di produzione, considerare il livello Standard, che offre macchine virtuali con bassa latenza, anche se a un costo superiore. D'altra parte, se si ha un ambiente di sviluppo e test, può essere preferibile il livello Basic, che ha macchine virtuali con latenza maggiore e costi minori. | Per impostazione predefinita, viene usato il livello [Standard](../virtual-machines/windows/sizes-general.md).
    **Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. | L'impostazione predefinita è 1.3x.
    **Offerta** | [Offerta Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. | L'opzione predefinita è [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Valuta** | Valuta di fatturazione. | La valuta predefinita è il dollaro statunitense.
    **Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure. | L'impostazione predefinita è 0%.
    **Vantaggio Azure Hybrid** | Specificare se si dispone di licenze Software Assurance e se si è idonei per l'opzione [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. | Il valore predefinito è Yes.

3. Fare clic su **Salva** per aggiornare le impostazioni di valutazione.


### <a name="analyze-the-vm-assessment"></a>Analizzare la valutazione delle VM

Una valutazione di Azure Migrate include informazioni sulla compatibilità o meno delle VM locali per Azure, sul dimensionamento corretto per la VM di Azure e sui costi mensili stimati di Azure. 

![Report di valutazione](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

![Visualizzazione della valutazione](./media/migrate-scenarios-assessment/assessment-display.png)

La valutazione ottiene una classificazione di attendibilità da 1 a 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle al livello massimo.
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione consente di stimare l'affidabilità dei consigli relativi alle dimensioni offerti da Azure Migrate.
- La classificazione di attendibilità è utile quando si esegue un *ridimensionamento in base alle prestazioni*, in quanto Azure Migrate potrebbe non avere a disposizione punti dati sufficienti per eseguire il ridimensionamento in base all'uso. Per un *ridimensionamento come in locale*, la classificazione di attendibilità è sempre 5 stelle, perché Azure Migrate ha a disposizione tutti i dati necessari per ridimensionare la macchina virtuale.
- Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

#### <a name="verify-readiness"></a>Verificare l'idoneità

![Idoneità per la valutazione](./media/migrate-scenarios-assessment/azure-readiness.png)  

Il report di valutazione mostra un riepilogo delle informazioni nella tabella. Si noti che per visualizzare il dimensionamento in base alle prestazioni, Azure Migrate necessita delle informazioni riportate di seguito. Se queste informazioni non possono essere raccolte, la valutazione del dimensionamento potrebbe non essere accurata.

- Dati sull'utilizzo di CPU e memoria.
- Operazioni di I/O al secondo in lettura/scrittura e velocità effettiva per ogni disco collegato alla VM.
- Informazioni su rete in ingresso/uscita per ogni scheda di rete collegata alla VM.


**Impostazione** | **Indicazione** | **Dettagli**
--- | --- | ---
**Idoneità della macchina virtuale per Azure** | Indica se la VM è idonea per la migrazione. | Stati possibili:</br><br/>- Idonea per Azure<br/><br/>- Idonea con condizioni <br/><br/>- Non idonea per Azure<br/><br/>- Idoneità sconosciuta<br/><br/> Se una VM non è idonea, verranno mostrate alcune procedure di correzione.
**Dimensioni macchina virtuale di Azure** | Per le VM idonee, viene consigliata una dimensione di VM di Azure. | Le dimensioni consigliate dipendono dalle proprietà della valutazione:<br/><br/>- Se è stato usato il dimensionamento in base alle prestazioni, viene considerata la cronologia delle prestazioni delle VM.<br/><br/>- Se è stata usata l'opzione "Come in locale", il dimensionamento è basato sulle dimensioni delle VM locali e i dati sull'utilizzo non vengono usati.
**Strumento suggerito** | Dato che i computer eseguono gli agenti, Azure Migrate esamina i processi in esecuzione nel computer e determina se è un computer di database o meno.
**Informazioni sulla VM** | Il report mostra le impostazioni della VM locale, con informazioni su sistema operativo, tipo di avvio, dischi e spazio di archiviazione.




#### <a name="review-monthly-cost-estimates"></a>Esaminare le stime dei costi mensili

In questa visualizzazione viene mostrato il costo totale di calcolo e di archiviazione associato all'esecuzione delle VM in Azure, con i dettagli per ogni computer. 

![Idoneità per la valutazione](./media/migrate-scenarios-assessment/azure-costs.png) 

- Le stime dei costi vengono calcolate usando le dimensioni consigliate per un computer.
- I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo. 


## <a name="conclusion"></a>Conclusioni

In questo scenario:

> [!div class="checklist"]
> * È stato valutato il database locale con lo strumento DMA.
> * Sono state valutate le VM locali, usando il mapping delle dipendenze, con il servizio Azure Migrate.
> * Sono state esaminate le valutazioni per verificare l'idoneità delle risorse locali per la migrazione ad Azure.

## <a name="next-steps"></a>Passaggi successivi

Passare allo scenario successivo, per eseguire una migrazione in modalità lift-and-shift delle VM locali ad Azure.



