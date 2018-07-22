---
title: Valutare i carichi di lavoro in locale per la migrazione di Contoso in Azure | Microsoft Docs
description: Informazioni su come Contoso valuta i computer locali per la migrazione ad Azure con Azure Migrate e il Servizio Migrazione del database
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006567"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migrazione di Contoso: valutare i carichi di lavoro in locale per la migrazione ad Azure

Questo articolo descrive come Contoso valuta l'app locale SmartHotel in preparazione alla migrazione ad Azure.

Questo documento è il terzo di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e una serie di scenari di distribuzione che illustrano la configurazione di un'infrastruttura di migrazione, l'idoneità delle risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli relativi alla migrazione. | Disponibile
Articolo 3: Valutare le risorse locali per la migrazione in Azure  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Questo articolo.
[Articolo 4: Eseguire il rehosting di un'app in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue una migrazione ad Azure in modalità lift-and-shift per l'app SmartHotel locale. usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM front-end dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel alle VM di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in VM di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. usando Site Recovery per la migrazione delle VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle VM di Azure usando Site Recovery. | Disponibile
[Articolo 8: Rehosting di un'app Linux in VM di Azure e Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Disponibile
[Articolo 10: Refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app osTicket Linux alle app Web di Azure in più siti, integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Viene illustrato come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Viene illustrato come Contoso ricompila le app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app di Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile


## <a name="overview"></a>Panoramica

Prendendo in considerazione la migrazione ad Azure, in Contoso si vuole eseguire una valutazione tecnica e finanziaria per determinare se i carichi di lavoro locali dell'azienda sono idonei per la migrazione al cloud. In particolare, il team di Contoso vuole valutare la compatibilità di computer e database ai fini della migrazione e stimare la capacità e i costi per l'esecuzione delle risorse in Azure.

Per iniziare e capire meglio le tecnologie coinvolte, il team intende valutare due app in locale, riepilogate nella tabella seguente. Si noti che il team esegue la valutazione per scenari di migrazione che prevedono il rehosting e il refactoring di app per la migrazione. Altre informazioni sul rehosting e il refactoring sono disponibili in [Contoso migration: Overview](contoso-migration-overview.md) (Migrazione Contoso: panoramica).

**Nome app** | **Piattaforma** | **Livelli app** | **Dettagli**
--- | --- | --- | ---
SmartHotel<br/><br/> Gestisce le esigenze di viaggi e trasferte in Contoso | Eseguita in Windows con un database di SQL Server | App a due livelli con sito Web ASP.NET di front-end in esecuzione in una macchina virtuale (WEBVM) e SQL Server in esecuzione in un'altra macchina virtuale (SQLVM) | Le macchine virtuali sono VMware, in esecuzione in un host ESXi gestito dal server vCenter.<br/><br/> L'app di esempio può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> App Service Desk di Contoso | In esecuzione in Linux/Apache, con PHP MySQL (LAMP). | App a due livelli con sito Web PHP di front-end in una macchina virtuale (OSTICKETWEB) e database MySQL in esecuzione in un'altra macchina virtuale (OSTICKETMYSQL) | L'app viene usata dalle app del servizio clienti per tenere traccia dei problemi per i dipendenti interni e i clienti esterni.<br/><br/> L'app di esempio può essere scaricata da [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Architettura corrente


Ecco un diagramma che illustra l'infrastruttura locale corrente di Contoso.

![Architettura di Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso dispone di un data center principale nella città di New York, negli Stati Uniti orientali.
- Possiede anche tre filiali locali dislocate in tre località negli Stati Uniti.
- Il data center principale è connesso a Internet tramite connessione Metro Ethernet in fibra (500 Mbps).
- Ogni filiale è connessa in locale a Internet tramite connessioni aziendali, con tunnel VPN IPSec in uscita nel data center principale. In questo modo l'intera rete è connessa in modo permanente e la connettività Internet risulta ottimizzata.
- Il data center principale è completamente virtualizzato con VMware. Due host di virtualizzazione ESXi 6.5 sono gestiti dal server vCenter 6.5.
- Contoso usa Active Directory per la gestione delle identità e server DNS nella rete interna.
- I controller di dominio nel data center vengono eseguiti in macchine virtuali VMware. Il controller di dominio nelle filiali locali vengono eseguiti in server fisici.





## <a name="business-drivers"></a>Driver di business

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: occorre rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.

## <a name="assessment-goals"></a>Obiettivi della valutazione

Il team dedicato al cloud di Contoso ha definito alcuni obiettivi per le valutazioni della migrazione:

- Dopo la migrazione, le app in Azure devono avere le stesse caratteristiche prestazionali di cui dispongono attualmente nell'ambiente VMware locale.  Lo spostamento nel cloud non significa che le prestazioni delle app siano meno critiche.
- Occorre analizzare la compatibilità delle applicazioni e dei database con i requisiti di Azure, nonché le opzioni di hosting disponibili in Azure.
- Dopo che le app sono state spostate nel cloud, l'amministrazione del database Contoso deve essere ridotta al minimo.  
- Si desidera comprendere non solo le opzioni di migrazione, ma anche i costi associati l'infrastruttura dopo lo spostamento nel cloud.

## <a name="assessment-tools"></a>Strumenti di valutazione
In Contoso si usano gli strumenti di Microsoft per la valutazione. Questi strumenti sono in linea con gli obiettivi prefissati e devono offrire tutte le informazioni necessarie.

**Technology** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Si usa DMA per valutare e rilevare i problemi di compatibilità che potrebbero compromettere le funzionalità del database in Azure. DMA valuta l'analogia nelle funzionalità tra le origini e le destinazioni SQL e consiglia miglioramenti nelle prestazioni e nell'affidabilità. | Questo strumento è scaricabile gratuitamente.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso usa questo servizio per valutare le macchine virtuali VMware. Valuta l'idoneità dei computer per la migrazione e offre stime relative al dimensionamento e ai costi per l'esecuzione in Azure.  | Attualmente (maggio 2018) non è previsto alcun addebito per l'uso del servizio.
[Elenco dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate usa Mapping dei servizi per mostrare le dipendenze tra i computer di cui si vuole eseguire la migrazione. |  Mapping dei servizi fa parte di Azure Log Analytics. Attualmente può essere usato senza addebito di costi per 180 giorni.

In questo scenario, si scarica e si esegue DMA per valutare il database di SQL Server locale per l'app di viaggi e si usa Azure Migrate con il mapping delle dipendenze per valutare le macchine virtuali dell'app prima di eseguirne la migrazione ad Azure.



## <a name="assessment-architecture"></a>Architettura per la valutazione


![Architettura per la valutazione della migrazione](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso è un nome fittizio che rappresenta una organizzazione aziendale tipica.
- Contoso possiede un data center locale (**contoso-datacenter**) con controller di dominio locali (CONTOSODC1, CONTOSODC2).
- Le macchine virtuali VMware si trovano in host ESXI VMware che eseguono la versione 6.5. Host: **contosohost1**, **contosohost2**
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter**) in esecuzione in una macchina virtuale.
- App di viaggi SmartHotel:
    - L'app è suddivisa in livelli tra due macchine virtuali VMware, **WEBVM** e **SQLVM**.
    - Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com**.
    - Le macchine virtuali sono in esecuzione in Windows Server 2008 R2 Datacenter con SP1.
- L'ambiente VMware è gestito da un server vCenter (**vcenter.contoso.com**) in esecuzione in una VM.
- App Service Desk OSTicket:
    - L'app è suddivisa in livelli tra due macchine virtuali, **OSTICKETWEB** e **OSTICKETMYSQL**.
    - Le macchine virtuali sono in esecuzione in Ubuntu Linux Server 16.04-LTS.
    - La macchina virtuale OSTICKETWEB esegue Apache 2 e PHP 7.0.
    - La macchina virtuale OSTICKETMYSQL esegue MySQL 5.7.22.

![Architettura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Prerequisiti

Di seguito vengono indicati i requisiti necessari a Contoso per la valutazione:

- Accesso come proprietario o collaboratore per la sottoscrizione di Azure o per un gruppo di risorse nella sottoscrizione di Azure.
- Server vCenter locale che esegue la versione 5.5, 6.0 o 6.5.
- Account di sola lettura nel server vCenter o autorizzazioni per crearne uno.
- Autorizzazioni per creare una VM nel server vCenter con un modello OVA.
- Almeno un host ESXi che esegue la versione 5.0 o una successiva.
- Almeno due VM VMware locali, in una delle quali viene eseguito un database di SQL Server.
- Autorizzazioni per installare gli agenti di Azure Migrate in ogni macchina virtuale.
- Connettività diretta a Internet delle VM.
        - È possibile limitare l'accesso a Internet agli [URL necessari](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Se i computer non hanno connessione a Internet, è necessario installarvi il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).
- Nome di dominio completo della VM che esegue l'istanza di SQL Server, per la valutazione del database.
- Windows Firewall in esecuzione nella VM di SQL Server che consente le connessioni esterne sulla porta TCP 1433 (predefinita), affinché DMA possa connettersi.


## <a name="assessment-overview"></a>Panoramica della valutazione

Di seguito vengono descritti i passaggi della valutazione in Contoso:


> [!div class="checklist"]
> * **Passaggio 1: Scaricare e installare DMA**. Si prepara DMA per la valutazione del database di SQL Server locale.
> * **Passaggio 2: Valutare il database con DMA**. Si esegue e si analizza la valutazione del database.
> * **Passaggio 3: Preparare la valutazione delle macchine virtuali con Azure Migrate**. Si configurano gli account locali e si ottimizzano le impostazioni di VMware.
> * **Passaggio 4: Individuare le macchine virtuali locali con Azure Migrate**. Si creare una macchina virtuale dell'agente di raccolta di Azure Migrate. Si esegue quindi l'agente di raccolta per individuare le macchine virtuali per la valutazione.
> * **Passaggio 5: Preparare l'analisi delle dipendenze con Azure Migrate**. Si installano gli agenti di Azure Migrate nelle macchine virtuali per poter verificare il mapping delle dipendenze tra le macchine virtuali.
> * **Passaggio 6: Valutare le macchine virtuali con Azure Migrate**. Si controllano le dipendenze, si raggruppano le macchine virtuali e si esegue la valutazione. Quando la valutazione è pronta, la si analizza in preparazione della migrazione.


## <a name="step-1-download-and-install-the-dma"></a>Passaggio 1: Scaricare e installare DMA

1. In Contoso si scarica DMA dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - È possibile installare Data Migration Assistant in qualsiasi computer che possa connettersi all'istanza di SQL. Non è necessario che venga eseguito nel computer di SQL Server.
    - Non è consigliabile eseguirlo nel computer host di SQL Server.
2. Si esegue il file di installazione scaricato (DownloadMigrationAssistant.msi) per avviare l'installazione.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Passaggio 2: Eseguire e analizzare la valutazione del database per SmartHotel

Si può ora eseguire una valutazione per analizzare il server SQL locale per l'app SmartHotel.

1. In Database Migration Assistant, si fa clic su **New** (Nuova), si seleziona **Assessment** (Valutazione) e si assegna alla valutazione il nome di progetto **SmartHotel**.
2. In **Tipo del server di origine** si seleziona **SQL Server on Azure Virtual Machines** (SQL Server in macchine virtuali di Azure).

    ![Selezionare l'origine](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Attualmente DMA non supporta la valutazione per la migrazione a un'istanza gestita di SQL. Come soluzione alternativa, in Contoso si usa SQL Server nella macchina virtuale Azure come destinazione prevista per la valutazione.

3. In **Seleziona versione di destinazione** si seleziona SQL Server 2017 come versione di destinazione. È necessario selezionare questa opzione perché è la versione usata dall'istanza gestita di database SQL.
4. Si sceglie di individuare le informazioni sulla compatibilità e le nuove funzionalità:
    - **Compatibility Issues** (Problemi di compatibilità) indica le modifiche che potrebbero causare un'interruzione nella migrazione o che richiedono una modifica secondaria prima della migrazione. Informa su eventuali funzionalità attualmente in uso che sono state deprecate. I problemi sono organizzati per livello di compatibilità.
    - **New features' recommendation** (Nuove funzionalità consigliate) indica le nuove funzionalità della piattaforma SQL Server di destinazione che possono essere usate per il database dopo la migrazione, organizzate nelle categorie Performance (Prestazioni), Security (Sicurezza) e Storage (Archiviazione).

    ![Selezionare la destinazione](./media/contoso-migration-assessment/dma-assessment-2.png)

2. In **Connetti a un server** si immettono il nome della macchina virtuale in esecuzione nel database e le credenziali di accesso. Occorre abilitare un **Certificato server attendibile** per assicurarsi di ottenere SQL Server. Si fa quindi clic su **Connetti**.

    ![Selezionare la destinazione](./media/contoso-migration-assessment/dma-assessment-3.png)

3. In **Aggiungi origine** si aggiunge il database da valutare e si fa clic su **Avanti** per avviare la valutazione.
4. Viene creata la valutazione.

    ![Creare la valutazione](./media/contoso-migration-assessment/dma-assessment-4.png)

5. In **Verifica risultati** è possibile visualizzare i risultati della valutazione.


### <a name="analyze-the-database-assessment"></a>Analizzare la valutazione del database

I risultati vengono visualizzati non appena disponibili. Se vengono corretti i problemi, occorre selezionare **Riavvia valutazione** per eseguire nuovamente la valutazione.

1. Nel report dei **problemi di compatibilità** si controllano tutti i problemi a ogni livello di compatibilità. I livelli di compatibilità sono associati alle versioni di SQL Server come indicato di seguito.

    - 100: SQL Server 2008/database SQL di Azure
    - 110: SQL Server 2012/database SQL di Azure
    - 120: SQL Server 2014/database SQL di Azure
    - 130: SQL Server 2016/database SQL di Azure
    - 140: SQL Server 2017/database SQL di Azure

    ![Problemi di compatibilità](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Nel report **Funzionalità consigliate** vengono visualizzate le funzionalità di archiviazione, sicurezza e prestazioni che la valutazione consiglia di configurare dopo la migrazione. Vengono consigliate diverse funzionalità, tra cui OLTP in memoria, Columnstore, Stretch Database, Always Encrypted, Dynamic Data Masking e Transparent Data Encryption (TDE).

    ![Funzionalità consigliate](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > È consigliabile [abilitare TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) per tutti i database SQL Server e questa funzionalità diventa ancora più importante quando i database risiedono nel cloud. La funzionalità TDE viene abilitata solo dopo la migrazione. Se la funzionalità TDE è già abilitata, sarà necessario spostare il certificato o la chiave asimmetrica nel database master del server di destinazione. [Altre informazioni](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. È possibile esportare la valutazione nel formato JSON o CSV.

Si noti che se si esegue una valutazione su scala più ampia, è possibile:

- Eseguire più valutazioni contemporaneamente e visualizzarne lo stato aprendo la pagina **All Assessments** (Tutte le valutazioni).
- [Consolidare le valutazioni in un database di SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Consolidare le valutazioni in un report di PowerBI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Passaggio 3: Preparare la valutazione delle macchine virtuali con Azure Migrate

In Contoso occorre creare un account VMware che verrà usato da Azure Migrate per individuare automaticamente le macchine virtuali per la valutazione, verificare le autorizzazioni per creare una macchina virtuale, rilevare le porte che devono essere aperte e impostare il livello delle impostazioni delle statistiche.

### <a name="set-up-a-vmware-account"></a>Configurare un account VMware

 L'individuazione delle macchine virtuali richiede un account di sola lettura in vCenter, con le proprietà seguenti:

- Tipo di utente: almeno un utente di sola lettura.
- Autorizzazioni: Data Center object (Oggetto data center) > Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura).
- Dettagli: l'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.
- Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

### <a name="verify-permissions-to-create-a-vm"></a>Verificare le autorizzazioni per creare una VM

Il team di Contoso verifica di avere le autorizzazioni per creare una macchina virtuale importando un file in formato .OVA. [Altre informazioni](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificare le porte

La valutazione di Contoso usa il mapping delle dipendenze. Per questa funzionalità è necessario che nelle macchine virtuali da valutare sia installato un agente, che deve potersi connettere ad Azure dalla porta TCP 443 in ogni macchina virtuale. Vedere [altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sui requisiti di connessione.


### <a name="set-statistics-settings"></a>Configurare le impostazioni delle statistiche

Prima di avviare la distribuzione, è necessario impostare le statistiche per il server vCenter sul livello 3. Si noti che:

- Dopo aver impostato il livello, è necessario attendere almeno un giorno prima di eseguire la valutazione. In caso contrario, potrebbe non funzionare come previsto.
- Se il livello è superiore a 3, la valutazione funziona, ma si verifica quanto segue:
    - I dati sulle prestazioni dei dischi e della rete non verranno raccolti.
    - Per l'archiviazione, Azure Migrate consiglierà un disco standard in Azure delle stesse dimensioni del disco locale.
    - Per la rete, per ogni scheda di rete locale verrà consigliata una scheda di rete in Azure.
    - Per il calcolo, Azure Migrate esaminerà le dimensioni della memoria e i core della VM e consiglierà una VM di Azure con la stessa configurazione. Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.
- Vedere [altre informazioni](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sul dimensionamento con il livello 3.

Si imposta il livello come descritto di seguito:

1. In vSphere Web Client si apre l'istanza del server vCenter.
2. In **Manage** (Gestisci)  > **Settings** (Impostazioni)  > **General** (Generale) si fa clic su **Edit** (Modifica).
3. In **Statistics** (Statistiche) si imposta il livello delle statistiche su **Level 3** (Livello 3).

    ![Livello delle statistiche in vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Passaggio 4: Individuare le macchine virtuali

Per individuare le macchine virtuali in Contoso, si crea un progetto di Azure Migrate. Si scarica e si configura la macchina virtuale dell'agente di raccolta e si esegue l'agente di raccolta per individuare le macchine virtuali locali.

### <a name="create-a-project"></a>Creare un progetto

1. Nel [portale di Azure](https://portal.azure.com) si cerca **Azure Migrate** e si crea un progetto (ContosoMigration).
2. Si specificano un nome di progetto, la sottoscrizione di Azure e si crea un nuovo gruppo di risorse di Azure denominato **ContosoFailoverRG**. Si noti che:

    - È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali.
    - È possibile pianificare una migrazione per qualsiasi località di destinazione.
    - La località del progetto viene usata solo per archiviare i metadati raccolti dalle VM locali.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita appliance dell'agente di raccolta. Questa macchina virtuale individua le macchine virtuali VMware locali e invia i relativi metadati al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, si scarica un modello con estensione ova e lo si importa nel server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate > **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**, si scarica il file di modello con estensione ova.
2. Si copiano l'ID e la chiave del progetto. Queste informazioni sono necessarie per configurare l'agente di raccolta.

    ![Scaricare il file con estensione ova](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Prima di distribuire la macchina virtuale, si verifica che il file con estensione ova sia sicuro.

1. Nel computer in cui è stato scaricato il file si apre una finestra dei comandi con privilegi di amministratore.
2. Si esegue il comando riportato di seguito per generare il valore hash per il file con estensione ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Il valore hash generato deve corrispondere a queste impostazioni (versione 1.0.9.12)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Creare l'appliance dell'agente di raccolta

È ora possibile importare il file scaricato nel server vCenter ed eseguire il provisioning della macchina virtuale del server di configurazione.

1. Nella console di vSphere Client si fa clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Distribuire il modello OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Nella procedura guidata Distribuire il modello OVF > **Origine** si specifica il percorso del file con estensione ova.
3. In **Name and Location** (Nome e posizione) si specificano un nome descrittivo per la macchina virtuale dell'agente di raccolta e la posizione di inventario in cui verrà ospitata. Si specifica inoltre l'host o il cluster in cui verrà eseguita l'appliance dell'agente di raccolta.
5. In **Storage** (Archiviazione) si specifica il percorso di archiviazione e in **Disk Format** (Formato disco) si specifica come eseguire il provisioning dell'archiviazione.
7. In **Network Mapping** (Mapping di rete) si specifica la rete a cui si connetterà la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure.
8. Si esaminano le impostazioni e si seleziona **Power on after deployment** (Accendi dopo distribuzione)> **Finish** (Fine). Al termine della creazione dell'appliance verrà visualizzato un messaggio di conferma del completamento.

### <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

Si esegue a questo punto l'agente di raccolta per individuare le macchine virtuali. Si noti che l'agente di raccolta attualmente supporta solo l'inglese (Stati Uniti) come lingua del sistema operativo e della propria interfaccia.

1. Nella console di vSphere Client > **Open Console** (Apri console), si specificano le preferenze di lingua, fuso orario e password per la macchina virtuale dell'agente di raccolta.
2. Sul desktop, si seleziona il collegamento **Run collector** (Esegui agente di raccolta).

    ![Collegamento per l'agente di raccolta](./media/contoso-migration-assessment/collector-shortcut.png)

4. In Agente di raccolta di Azure Migrate > **Set up prerequisites** (Imposta prerequisiti), si accettano le condizioni di licenza e si leggono le informazioni sulle terze parti.
5. L'agente di raccolta controlla che la macchina virtuale abbia accesso a Internet, che l'ora sia sincronizzata e che il servizio dell'agente di raccolta (installato per impostazione predefinita nella VM) sia in esecuzione. Installa anche VMware PowerCLI.

    > [!NOTE]
    > Si presume che la VM abbia accesso diretto a Internet, senza proxy.

    ![Verificare i prerequisiti](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. In **Specificare i dettagli del server vCenter** si specificano il nome (FQDN) o l'indirizzo IP del server vCenter, nonché le credenziali di sola lettura usate per l'individuazione.
7. Si seleziona un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster, ma non deve contenere più di 1500 macchine virtuali.

    ![Connettersi a vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. In **Specificare il progetto di migrazione** si specificano l'ID e la chiave del progetto di Azure Migrate copiati dal portale. È possibile ottenere queste informazioni nuovamente nella pagina **Overview** (Panoramica) del progetto > **Individua macchine virtuali**.  

    ![Connect to Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. In **Visualizza lo stato della raccolta** è possibile monitorare l'individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.

    ![Raccolta in corso](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Al termine della raccolta, si verifica che le macchine virtuali vengano visualizzate nel portale.

1. Nel progetto di Azure Migrate > **Manage** (Gestisci)  > **Machines** (Computer), si controlla che siano presenti le macchine virtuali da individuare.

    ![Macchine virtuali individuate](./media/contoso-migration-assessment/discovery-complete.png)

3. Si noti che nei computer non sono attualmente installati gli agenti di Azure Migrate. Occorre installarli per poter visualizzare le dipendenze.

    ![Macchine virtuali individuate](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Passaggio 5: Preparare l'analisi delle dipendenze

Per visualizzare le dipendenze tra le macchine virtuali da valutare, in Contoso si scaricano e si installano gli agenti nelle macchine virtuali dell'app. Queste operazioni vengono eseguite in tutte le macchine virtuali per le app, sia Windows che Linux.

### <a name="take-a-snapshot"></a>Creare uno snapshot

Si conserva una copia della macchina virtuale prima di modificarla, acquisendo uno snapshot prima di installare gli agenti.

![Snapshot del computer](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

1. Nella pagina **Computer** si seleziona il computer e **Richiede l'installazione** nella colonna **Dipendenze**.
2. Nella pagina **Individua macchine virtuali** si eseguono le operazioni descritte di seguito:
    - Si scaricano Microsoft Monitoring Agent (MMA) e Dependency Agent per ogni computer Windows
    - Si scaricano Microsoft Monitoring Agent (MMA) e Dependency Agent per ogni computer Linux
3. Si copiano l'ID e la chiave dell'area di lavoro. Queste informazioni sono necessarie durante l'installazione di MMA.

    ![Download degli agenti](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Installare gli agenti nelle VM Windows

Si esegue l'installazione in ogni macchina virtuale.

#### <a name="install-the-mma-on-windows-vms"></a>Installare MMA nelle VM Windows

1. Si fa doppio clic sull'agente scaricato.
2. In **Cartella di destinazione** si mantiene la cartella di installazione predefinita e quindi si fa clic su **Avanti**.
2. In **Opzioni di installazione dell'agente** si seleziona **Connect the agent to Azure Log Analytics** (Connetti l'agente ad Azure Log Analytics) > **Avanti**.

    ![Installazione di MMA](./media/contoso-migration-assessment/mma-install.png)

5. In **Azure Log Analytics** si incollano l'ID e la chiave dell'area di lavoro copiati dal portale.

    ![Installazione di MMA](./media/contoso-migration-assessment/mma-install2.png)

6. In **Pronto per l'installazione** è ora possibile installare MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Installare Dependency Agent in VM Windows

1. Si fa doppio clic sul file di Dependency Agent scaricato.
2. Si accettano le condizioni di licenza e si attende il completamento dell'installazione.

    ![Dependency Agent](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Installare gli agenti nelle macchine virtuali Linux

Si esegue l'installazione in ogni macchina virtuale.

#### <a name="install-the-mma-on-linux-vms"></a>Installare MMA nelle macchine virtuali Linux

1. Si installa la libreria python ctypes in ogni macchina virtuale usando questo comando: **sudo apt-get install python-ctypeslib**.
2. È consigliabile eseguire il comando per installare l'agente MMA come radice.  Per installarlo come radice, eseguire il comando e immettere la password della radice: **sudo -i**.
3. Si installa a questo punto l'agente MMA.
    - Si immettono la chiave e l'ID dell'area di lavoro corretti nel comando.
    - I comandi sono per la versione a 64 bit.
    - I valori di **ID area di lavoro** e **Chiave primaria** sono disponibili nel portale OMS > **Impostazioni**, nella scheda **Origini connesse**.
    - Eseguire i comandi seguenti per scaricare l'agente di OMS, convalidare il checksum, quindi installare e caricare l'agente.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Installare Dependency Agent nelle VM Linux

Dopo aver installato MMA, è possibile installare Dependency Agent nelle macchine virtuali Linux.

1. Dependency Agent viene installato nei computer Linux usando InstallDependencyAgent-Linux64.bin, uno script della shell con un file binario autoestraente. Si può eseguire il file usando sh oppure aggiungere autorizzazioni di esecuzione al file stesso.

2. Si installa Dependency Agent per Linux come radice:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Passaggio 6: Eseguire e analizzare la valutazione delle macchine virtuali

In Contoso si possono ora verificare le dipendenze dei computer e si può creare un gruppo. Si esegue quindi la valutazione per il gruppo.

### <a name="verify-dependencies-and-create-a-group"></a>Verificare le dipendenze e creare un gruppo


1. Per i computer da analizzare, si seleziona **Visualizza dipendenze**.

    ![Visualizzare le dipendenze dei computer](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Per SQLVM, la mappa delle dipendenze mostra i dettagli seguenti:

    - Processi/gruppi di processi con connessioni di rete attive in esecuzione in SQLVM durante il periodo di tempo specificato (per impostazione predefinita, un'ora)
    - Connessioni TCP in ingresso (client) e in uscita (server) da e verso tutti i computer dipendenti
    - Computer dipendenti in cui sono installati gli agenti di Azure Migrate, in caselle separate
    - Computer in cui non sono installati gli agenti, con le informazioni relative a porta e indirizzo IP

3. Per i computer in cui è installato l'agente (WEBVM),si seleziona la relativa casella per visualizzare altre informazioni, come il nome di dominio completo, il sistema operativo e l'indirizzo MAC.

    ![Visualizzazione delle dipendenze del gruppo](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Si selezionano quindi le macchine virtuali da aggiungere al gruppo (SQLVM e WEBVM).  Per selezionare più macchine virtuali, si usa CTRL+clic.
5. Si seleziona **Crea gruppo** e si specifica un nome (smarthotelapp).

> [!NOTE]
    > Per visualizzare le dipendenze a un livello più dettagliato, si può espandere l'intervallo di tempo. È possibile selezionare una durata specifica oppure le date di inizio e di fine.


### <a name="run-an-assessment"></a>Eseguire una valutazione


1. Nella pagina **Gruppi**  si apre il gruppo (smarthotelapp) e si seleziona **Crea valutazione**.

    ![Creare una valutazione](./media/contoso-migration-assessment/run-vm-assessment.png)

2. La valutazione viene visualizzata nella pagina **Gestisci** > **Valutazioni**.

In Contoso sono state usate le impostazioni di valutazione predefinite, ma è possibile personalizzare le impostazioni. [Altre informazioni](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analizzare la valutazione delle VM

Una valutazione di Azure Migrate include informazioni sulla compatibilità delle macchine virtuali locali per Azure, sul dimensionamento corretto per la macchina virtuale Azure e sui costi mensili stimati di Azure.

![Report di valutazione](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

![Visualizzazione della valutazione](./media/contoso-migration-assessment/assessment-display.png)

Una valutazione ottiene una classificazione di attendibilità da 1 a 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle al livello massimo.
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

![Idoneità per la valutazione](./media/contoso-migration-assessment/azure-readiness.png)  

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

![Idoneità per la valutazione](./media/contoso-migration-assessment/azure-costs.png)

- Le stime dei costi vengono calcolate usando le dimensioni consigliate per un computer.
- I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo.


## <a name="clean-up-after-assessment"></a>Pulizia dopo la valutazione

- Al termine della valutazione, in Contoso si conserva l'appliance di Azure Migrate per valutazioni future.
- Si spegne la macchina virtuale VMware. La si riavvierà quando si valuteranno altre macchine virtuali.
- Si conserva il processo migrazione di Contoso in Azure.  Al momento è distribuito nel gruppo di risorse ContosoFailoverRG, nell'area di Microsoft Azure Stati Uniti orientali.
-  La macchina virtuale dell'agente di raccolta dispone di una licenza di valutazione di 180 giorni. Se scade, sarà necessario scaricare e configurare di nuovo l'agente di raccolta.


## <a name="conclusion"></a>Conclusioni

In questo scenario di Contoso sono stati valutati il database dell'app SmartHotel usando lo strumento DMA e le macchine virtuali in locale usando il servizio Azure Migrate. Sono state esaminate le valutazioni per verificare l'idoneità delle risorse locali per la migrazione ad Azure.

## <a name="next-steps"></a>Passaggi successivi

Nel prossimo articolo di questa serie, viene eseguito il rehosting dell'app SmartHotel di Contoso in Azure con una migrazione di tipo lift-and-shift. Viene eseguita la migrazione della macchina virtuale WEBVM di front-end per l'app usando Azure Site Recovery e viene eseguita la migrazione del database dell'app a un'istanza gestita di Azure SQL usando il Servizio Migrazione del database. [Attività iniziali](contoso-migration-rehost-vm-sql-managed-instance.md) con questa distribuzione.
