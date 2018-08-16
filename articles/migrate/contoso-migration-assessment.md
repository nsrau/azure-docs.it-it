---
title: Valutare i carichi di lavoro in locale per la migrazione di Contoso in Azure | Microsoft Docs
description: Informazioni su come Contoso valuta i computer locali per la migrazione ad Azure con Azure Migrate e Data Migration Assistant.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: raynew
ms.openlocfilehash: 50d1b8fca8e5377c35810e08258a0ecc3770ae75
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422325"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migrazione di Contoso: valutare i carichi di lavoro in locale per la migrazione ad Azure

Questo articolo descrive come Contoso valuta l'app locale SmartHotel in preparazione alla migrazione dell'app ad Azure.

Questo articolo è il terzo di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e una serie di scenari di distribuzione che illustrano la configurazione di un'infrastruttura di migrazione, l'idoneità delle risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Alla serie verranno aggiunti altri articoli in futuro.

Articolo | Dettagli | Status
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della strategia di migrazione di Contoso, della serie di articoli e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
Articolo 3: Valutare le risorse locali per la migrazione in Azure | Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le macchine virtuali dell'app tramite il servizio [Azure Migrate](migrate-overview.md) e il database di SQL Server dell'app tramite [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Questo articolo
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel locale. Esegue la migrazione della macchina virtuale front-end dell'app tramite [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app in un'Istanza gestita di database SQL di Azure tramite il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel alle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Contoso completa una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure mediante Site Recovery. Esegue la migrazione del database dell'app in Database di Azure per MySQL tramite MySQL Workbench. | Disponibile
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel in un'app Web di Azure e del database dell'app in un'istanza di SQL Server di Azure. | Disponibile
[Articolo 10: Eseguire il refactoring di un'app Linux in un'app Web di Azure e Database di Azure per MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket Linux in un'app Web di Azure su più siti. L'app Web è integrata con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile
[Articolo 11: Eseguire il refactoring di Team Foundation Server su Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server locale in Visual Studio Team Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione l'app SmartHotel in Azure e quindi la riprogetta. Il livello dell'app Web viene riprogettato come contenitore di Windows e il database dell'app viene riprogettato tramite il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizio app di Azure, servizio Kubernetes di Azure, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile


## <a name="overview"></a>Panoramica

Prendendo in considerazione la migrazione ad Azure, Contoso vuole eseguire una valutazione tecnica e finanziaria per determinare se i carichi di lavoro locali dell'azienda sono idonei per la migrazione al cloud. In particolare, il team Contoso vuole valutare la compatibilità di computer e database per la migrazione, nonché stimare la capacità e i costi per l'esecuzione delle risorse di Contoso in Azure.

Per iniziare e comprendere meglio le tecnologie coinvolte, Contoso valuta due delle app locali, riepilogate nella tabella seguente. L'azienda esegue la valutazione per scenari di migrazione che prevedono il rehosting e il refactoring delle app per la migrazione. Altre informazioni sul rehosting e il refactoring sono disponibili in [Contoso migration: Overview](contoso-migration-overview.md) (Migrazione Contoso: panoramica).

Nome app | Piattaforma | Livelli app | Dettagli
--- | --- | --- | ---
SmartHotel<br/><br/> (gestisce le esigenze di viaggi e trasferte in Contoso) | In esecuzione in Windows con un database di SQL Server | App a due livelli. Il sito Web ASP.NET front-end viene eseguito in una macchina virtuale (**WEBVM**) e SQL Server viene eseguito in un'altra macchina virtuale (**SQLVM**). | Le macchine virtuali sono VMware, in esecuzione in un host ESXi gestito dal server vCenter.<br/><br/> È possibile scaricare l'app di esempio da [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (app Service Desk di Contoso) | In esecuzione in Linux/Apache, con PHP MySQL (LAMP) | App a due livelli. Un sito Web PHP front-end eseguito in una macchina virtuale (**OSTICKETWEB**) e il database MySQL viene eseguito in un'altra macchina virtuale (**OSTICKETMYSQL**). | L'app viene usata dalle app del servizio clienti per tenere traccia dei problemi per i dipendenti interni e i clienti esterni.<br/><br/> È possibile scaricare l'esempio da [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Architettura corrente

Questo diagramma illustra l'infrastruttura locale corrente di Contoso:

![Architettura corrente di Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso dispone di un data center principale che si trova nella città di New York, negli Stati Uniti orientali.
- Possiede anche tre succursali locali dislocate negli Stati Uniti.
- Il data center principale è connesso a Internet tramite connessione Metro Ethernet Fiber (500 MBps).
- Ogni succursale è connessa in locale a Internet tramite connessioni aziendali, con tunnel VPN IPSec in uscita nel data center principale. Questa configurazione consente all'intera rete Contoso di essere connessa in modo permanente e la connettività Internet risulta ottimizzata.
- Il data center principale è completamente virtualizzato con VMware. Contoso dispone di due host di virtualizzazione ESXi 6.5 gestiti dal server vCenter 6.5.
- Contoso usa Active Directory per la gestione delle identità e i server DNS nella rete interna.
- I controller di dominio nel data center vengono eseguiti in macchine virtuali VMware. Il controller di dominio nelle filiali locali vengono eseguiti in server fisici.

## <a name="business-drivers"></a>Driver di business

Il team dei responsabili IT di Contoso ha collaborato attivamente con i partner commerciali dell'azienda per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Affrontare la crescita del business**: Contoso è in crescita. Di conseguenza, la pressione è aumentata sui sistemi locali e l'infrastruttura dell'azienda.
- **Aumentare l'efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti. L'azienda richiede un reparto IT rapido ed efficiente in termini di tempo e costi, per consentire di soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente dei cambiamenti nel marketplace, in modo da consentire all'azienda di raggiungere risultati positivi in un'economia globale. Il reparto IT di Contoso non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il reparto IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.

## <a name="assessment-goals"></a>Obiettivi della valutazione

Il team dedicato al cloud di Contoso ha identificato alcuni obiettivi per le valutazioni della migrazione:

- Dopo la migrazione, le app in Azure dovranno avere le stesse caratteristiche prestazionali di cui dispongono attualmente nell'ambiente VMware locale di Contoso. Lo spostamento nel cloud non significa che le prestazioni delle app siano meno critiche.
- Occorre analizzare la compatibilità delle applicazioni e dei database con i requisiti di Azure. Contoso deve anche comprendere le opzioni di hosting in Azure.
- Dopo lo spostamento delle app nel cloud, l'amministrazione del database Contoso deve essere ridotta al minimo.  
- Si vogliono comprendere non solo le opzioni di migrazione, ma anche i costi associati all'infrastruttura dopo lo spostamento nel cloud.

## <a name="assessment-tools"></a>Strumenti di valutazione

Contoso usa gli strumenti Microsoft per la valutazione della migrazione. Questi strumenti sono in linea con gli obiettivi aziendali prefissati e devono offrire a Contoso tutte le informazioni necessarie.

Tecnologia | Descrizione | Costi
--- | --- | ---
[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso usa Data Migration Assistant per valutare e rilevare i problemi di compatibilità che potrebbero compromettere le funzionalità del database in Azure. Data Migration Assistant valuta la parità delle funzionalità tra origini e destinazioni SQL e consiglia miglioramenti per le prestazioni e l'affidabilità. | Data Migration Assistant è uno strumento gratuito e scaricabile.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso usa il servizio Azure Migrate per valutare le proprie macchine virtuali VMware. Azure Migrate valuta l'idoneità alla migrazione delle macchine. Fornisce stime di dimensioni e costi per l'esecuzione in Azure.  | A partire da maggio 2018, Azure Migrate è un servizio gratuito.
[Elenco dei servizi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate usa Mapping dei servizi per mostrare le dipendenze tra le macchine di cui l'azienda vuole eseguire la migrazione. | Mapping dei servizi fa parte di Azure Log Analytics. Attualmente, Contoso può usare Mapping dei servizi per 180 giorni senza addebito di costi.

In questo scenario, Contoso scarica ed esegue Data Migration Assistant per valutare il database di SQL Server locale per l'app di viaggi e usa Azure Migrate con il mapping delle dipendenze per valutare le macchine virtuali dell'app prima di eseguirne la migrazione ad Azure.

## <a name="assessment-architecture"></a>Architettura per la valutazione

![Architettura per la valutazione della migrazione](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso è un nome fittizio che rappresenta un'organizzazione aziendale tipica.
- Contoso possiede un data center locale (**contoso-datacenter**) e controller di dominio locali (**CONTOSODC1**, **CONTOSODC2**).
- Le macchine virtuali VMware si trovano in host VMware ESXi che eseguono la versione 6.5(**contosohost1**, **contosohost2**).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com** in esecuzione su una macchina virtuale).
- L'app di viaggi SmartHotel presenta queste caratteristiche:
    - L'app è suddivisa in livelli tra due macchine virtuali VMware (**WEBVM** e **SQLVM**).
    - Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com**.
    - Le macchine virtuali sono in esecuzione in Windows Server 2008 R2 Datacenter con SP1.
- L'ambiente VMware è gestito da un server vCenter (**vcenter.contoso.com**) in esecuzione in una VM.
- App Service Desk osTicket:
    - L'app è suddivisa in livelli tra due macchine virtuali (**OSTICKETWEB** e **OSTICKETMYSQL**).
    - Le macchine virtuali sono in esecuzione in Ubuntu Linux Server 16.04-LTS.
    - **OSTICKETWEB** esegue Apache 2 e PHP 7.0.
    - **OSTICKETMYSQL** esegue MySQL 5.7.22.

## <a name="prerequisites"></a>Prerequisiti

Contoso e gli altri utenti devono soddisfare i prerequisiti seguenti per la valutazione:

- Autorizzazioni di Proprietario o Collaboratore per la sottoscrizione di Azure o per un gruppo di risorse nella sottoscrizione di Azure.
- Un'istanza del server vCenter locale che esegue la versione 6.5, 6.0 o 5.5.
- Account di sola lettura nel server vCenter o autorizzazioni per crearne uno.
- Autorizzazioni per creare una macchina virtuale nell'istanza del server vCenter con un modello OVA.
- Almeno un host ESXi che esegue la versione 5.0 o successiva.
- Almeno due VM VMware locali, in una delle quali viene eseguito un database di SQL Server.
- Autorizzazioni per installare gli agenti di Azure Migrate in ogni macchina virtuale.
- Connettività diretta a Internet delle VM.  
        - È possibile limitare l'accesso a Internet agli [URL necessari](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).  
        - Se le macchine virtuali non hanno connettività Internet, è necessario installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) di Azure Log Analytics e indirizzare ad esse il traffico dell'agente.
- Nome di dominio completo della VM che esegue l'istanza di SQL Server, per la valutazione del database.
- Windows Firewall in esecuzione nella macchina virtuale di SQL Server deve consentire le connessioni esterne sulla porta TCP 1433 (predefinita). Questa configurazione consente la connessione di Data Migration Assistant.

## <a name="assessment-overview"></a>Panoramica della valutazione

Ecco come Contoso esegue la valutazione:

> [!div class="checklist"]
> * **Passaggio 1: Scaricare e installare Data Migration Assistant**: Contoso prepara Data Migration Assistant per la valutazione del database di SQL Server locale.
> * **Passaggio 2: Valutare il database tramite Data Migration Assistant** - Contoso esegue e analizza la valutazione del database.
> * **Passaggio 3: Preparare la valutazione delle macchine virtuali con Azure Migrate** - Contoso configura gli account locali e configura le impostazioni di VMware.
> * **Passaggio 4: Individuare le macchine virtuali locali con Azure Migrate** - Contoso crea una macchina virtuale dell'agente di raccolta di Azure Migrate. Contoso esegue quindi l'agente di raccolta per individuare le macchine virtuali per la valutazione.
> * **Passaggio 5: Preparare l'analisi delle dipendenze con Azure Migrate** - Contoso installa gli agenti di Azure Migrate nelle macchine virtuali per poter verificare il mapping delle dipendenze tra le macchine virtuali.
> * **Passaggio 6: Valutare le macchine virtuali con Azure Migrate** - Contoso verifica le dipendenze, raggruppa le macchine virtuali ed esegue la valutazione. Quando la valutazione è pronta, Contoso la analizza in preparazione per la migrazione.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Passaggio 1: Scaricare e installare Data Migration Assistant

1. Contoso scarica Data Migration Assistant dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - È possibile installare Data Migration Assistant in qualsiasi computer che possa connettersi all'istanza di SQL Server. Non è necessario che venga eseguito nel computer di SQL Server.
    - Non è consigliabile eseguire Data Migration Assistant nel computer host di SQL Server.
2. Contoso esegue il file di installazione scaricato (DownloadMigrationAssistant.msi) per avviare l'installazione.
3. Nella pagina **Fine** Contoso seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Passaggio 2: Eseguire e analizzare la valutazione del database per SmartHotel

Contoso può ora eseguire una valutazione per analizzare il database di SQL Server locale per l'app SmartHotel.

1. In Data Migration Assistant, Contoso seleziona **Nuova** > **Valutazione** e assegna quindi un nome di progetto alla valutazione (**SmartHotel**).
2. In **Tipo del server di origine** Contoso seleziona **SQL Server in macchine virtuali di Azure**.

    ![Data Migration Assistant - Selezionare l'origine](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Attualmente, Data Migration Assistant non supporta la valutazione per la migrazione a Istanza gestita di database SQL di Azure. Come soluzione alternativa, Contoso usa SQL Server in una macchina virtuale di Azure come destinazione prevista per la valutazione.

3. In **Seleziona versione di destinazione** Contoso seleziona SQL Server 2017 come versione di destinazione. Contoso deve selezionare questa versione, perché si tratta della versione che viene usata da Istanza gestita di database SQL di Azure.
4. Contoso seleziona i report per individuare le informazioni sulla compatibilità e sulle nuove funzionalità:
    - **Problemi di compatibilità**: indica le modifiche che potrebbero causare un'interruzione nella migrazione o che richiedono una modifica secondaria prima della migrazione. Questo report mantiene Contoso informata su eventuali funzionalità attualmente in uso che vengono deprecate. I problemi sono organizzati per livello di compatibilità.
    - **New features' recommendation** (Nuove funzionalità consigliate) indica le nuove funzionalità della piattaforma SQL Server di destinazione che possono essere usate per il database dopo la migrazione, Le nuove funzionalità consigliate sono organizzate nelle sezioni **Prestazioni**, **Sicurezza** e **Archiviazione**.

    ![Data Migration Assistant - Problemi di compatibilità e nuove funzionalità](./media/contoso-migration-assessment/dma-assessment-2.png)

2. In **Connetti a un server** Contoso immette il nome della macchina virtuale che esegue il database e le credenziali per accedervi. Contoso seleziona **Considera attendibile il certificato del server** per assicurarsi che la macchina virtuale possa accedere a SQL Server. Contoso seleziona quindi **Connetti**.

    ![Data Migration Assistant - Connettersi a un server](./media/contoso-migration-assessment/dma-assessment-3.png)

3. In **Aggiungi origine** Contoso aggiunge il database che vuole valutare e quindi seleziona **Avanti** per avviare la valutazione.
4. Viene creata la valutazione.

    ![Data Migration Assistant - Creare la valutazione](./media/contoso-migration-assessment/dma-assessment-4.png)

5. In **Verifica risultati** Contoso visualizza i risultati della valutazione.

### <a name="analyze-the-database-assessment"></a>Analizzare la valutazione del database

I risultati vengono visualizzati non appena disponibili. Se Contoso corregge i problemi, deve selezionare **Riavvia valutazione** per eseguire di nuovo la valutazione.

1. Nel report **Problemi di compatibilità** Contoso controlla se sono presenti problemi a ogni livello di compatibilità. I livelli di compatibilità sono associati alle versioni di SQL Server come indicato di seguito.

    - 100: SQL Server 2008/database SQL di Azure
    - 110: SQL Server 2012/database SQL di Azure
    - 120: SQL Server 2014/database SQL di Azure
    - 130: SQL Server 2016/database SQL di Azure
    - 140: SQL Server 2017/database SQL di Azure

    ![Data Migration Assistant -Report Problemi di compatibilità](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Nel report **Funzionalità consigliate** Contoso visualizza le funzionalità di archiviazione, sicurezza e prestazioni che la valutazione consiglia di configurare dopo la migrazione. Vengono consigliate diverse funzionalità, tra cui OLTP in memoria, indici columnstore, Stretch Database, Always Encrypted, Dynamic Data Masking e Transparent Data Encryption.

    ![Data Migration Assistant - Report Funzionalità consigliate](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso deve [abilitare Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) per tutti i database di SQL Server. Ciò è ancora più importante quando un database si trova nel cloud rispetto a quando è ospitato in locale. Transparent Data Encryption deve essere abilitata solo dopo la migrazione. Se la funzionalità Transparent Data Encryption è già abilitata, Contoso deve spostare il certificato o la chiave asimmetrica nel database master del server di destinazione. Informazioni su come [spostare un database protetto con Transparent Data Encryption in un'altra istanza di SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Contoso può esportare la valutazione nel formato JSON o CSV.

> [!NOTE]
> Per le valutazioni su larga scala:
> - Eseguire più valutazioni contemporaneamente e visualizzarne lo stato nella pagina **Tutte le valutazioni**.
> - Consolidare le valutazioni in un [database di SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
> - Consolidare le valutazioni in un [report di Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Passaggio 3: Preparare la valutazione delle macchine virtuali con Azure Migrate

Contoso deve creare un account VMware che può essere usato da Azure Migrate per individuare automaticamente le macchine virtuali per la valutazione, verificare i diritti per la creazione di una macchina virtuale, rilevare le porte che devono essere aperte e impostare il livello delle impostazioni delle statistiche.

### <a name="set-up-a-vmware-account"></a>Configurare un account VMware

L'individuazione delle macchine virtuali richiede un account di sola lettura nel server vCenter, con le proprietà seguenti:

- **Tipo di utente**: almeno un utente di sola lettura.
- **Autorizzazioni**: per l'oggetto data center selezionare la casella di controllo **Propagate to Child Objects** (Propaga a oggetti figlio). Per **Ruolo** selezionare **Sola lettura**.
- **Dettagli**: l'utente viene assegnato a livello di data center, con accesso a tutti gli oggetti nel data center.
- Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to Child Objects** (Propaga a oggetti figlio) agli oggetti figlio (host vSphere, archivi dati, macchine virtuali e reti).

### <a name="verify-permissions-to-create-a-vm"></a>Verificare le autorizzazioni per creare una VM

Contoso verifica di avere le autorizzazioni per creare una macchina virtuale importando un file in formato .ova. Informazioni su come [creare e assegnare un ruolo con privilegi](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificare le porte

La valutazione di Contoso usa il mapping delle dipendenze. Il mapping delle dipendenze richiede l'installazione di un agente nelle macchine virtuali che verranno valutate. L'agente deve potersi connettere ad Azure dalla porta TCP 443 in ogni macchina virtuale. Vedere altre informazioni sui [requisiti di connessione](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Configurare le impostazioni delle statistiche

Prima di avviare la distribuzione, Contoso deve impostare le statistiche per il server vCenter sul livello 3. 

> [!NOTE]
> - Dopo aver impostato il livello, Contoso deve attendere almeno un giorno prima di eseguire la valutazione. In caso contrario, la valutazione potrebbe non funzionare come previsto.
> - Se il livello è superiore a 3, la valutazione funziona, ma si verifica quanto segue:
>    - I dati sulle prestazioni dei dischi e della rete non vengono raccolti.
>    - Per l'archiviazione, Azure Migrate consiglierà un disco standard in Azure delle stesse dimensioni del disco locale.
>    - Per la rete, per ogni scheda di rete locale viene consigliata una scheda di rete in Azure.
>    - Per il calcolo, Azure Migrate esamina le dimensioni della memoria e i core della macchina virtuale e consiglia una macchina virtuale di Azure con la stessa configurazione. Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.
> - Per altre informazioni sul ridimensionamento con il livello 3, vedere [Ridimensionamento](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

Per impostare il livello:

1. In vSphere Web Client, Contoso apre l'istanza del server vCenter.
2. Contoso seleziona **Manage** > **Settings** > **General** > **Edit** (Gestisci, Impostazioni, Generale, Modifica).
3. In **Statistics** (Statistiche) Contoso imposta il livello delle statistiche su **Level 3** (Livello 3).

    ![Livello delle statistiche nel server vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Passaggio 4: Individuare le macchine virtuali

Per individuare le macchine virtuali in Contoso, si crea un progetto di Azure Migrate. Contoso Scarica e configura la macchina virtuale dell'agente di raccolta. Contoso esegue quindi l'agente di raccolta per individuare le macchine virtuali in locale.

### <a name="create-a-project"></a>Creare un progetto

1. Nel [portale di Azure](https://portal.azure.com) Contoso cerca **Azure Migrate**. Contoso crea quindi un progetto.
2. Contoso specifica un nome di progetto (**ContosoMigration**) e la sottoscrizione di Azure. Crea un nuovo gruppo di risorse di Azure (**ContosoFailoverRG**). 
    > [!NOTE]
    > - È possibile creare un progetto Azure Migrate solo nell'area Stati Uniti centro-occidentali o Stati Uniti orientali.
    > - È possibile pianificare una migrazione per qualsiasi località di destinazione.
    > - La località del progetto viene usata solo per archiviare i metadati raccolti dalle macchine virtuali locali.

    ![Azure Migrate - Creare un progetto di migrazione](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Scaricare l'appliance dell'agente di raccolta

Azure Migrate crea una macchina virtuale locale definita *appliance dell'agente di raccolta*. La macchina virtuale individua le macchine virtuali VMware locali e invia i metadati per le macchine virtuali al servizio Azure Migrate. Per configurare l'appliance dell'agente di raccolta, Contoso scarica un modello con estensione ova e quindi lo importa nell'istanza del server vCenter locale per creare la macchina virtuale.

1. Nel progetto di Azure Migrate, Contoso seleziona **Attività iniziali** > **Individua e valuta** > **Individua macchine virtuali**. Contoso scarica il file di modello con estensione ova.
2. Contoso copia l'ID e la chiave del progetto. Il progetto e l'ID sono necessari per configurare l'agente di raccolta.

    ![Azure Migrate - Scaricare il file OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificare l'appliance dell'agente di raccolta

Prima di distribuire la macchina virtuale, Contoso verifica che il file OVA sia sicuro:

1. Nel computer in cui è stato scaricato il file, Contoso apre una finestra del prompt dei comandi con privilegi di amministratore.
2. Contoso esegue il comando seguente per generare l'hash per il file OVA:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    **Esempio** 
    
    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. L'hash generato deve corrispondere a queste impostazioni (versione 1.0.9.12):

    Algoritmo | Valore hash
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Creare l'appliance dell'agente di raccolta

Contoso può ora importare il file scaricato nell'istanza del server vCenter ed eseguire il provisioning della macchina virtuale dell'appliance dell'agente di raccolta:

1. Nella console di vSphere Client, Contoso seleziona **File** > **Deploy OVF Template** (Distribuisci modello OVF).

    ![Client Web vSphere - Distribuire il modello OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Nella distribuzione guidata del modello OVF, Contoso seleziona **Source** (Origine) e quindi specifica il percorso del file OVA.
3. In **Name and Location** (Nome e percorso) Contoso specifica un nome visualizzato per la macchina virtuale dell'agente di raccolta. Seleziona quindi la posizione di inventario in cui ospitare la macchina virtuale. Contoso specifica anche l'host o il cluster in cui eseguire l'appliance dell'agente di raccolta.
4. In **Storage** (Archiviazione) Contoso specifica il percorso di archiviazione. In **Disk Format** (Formato disco) Contoso seleziona la modalità di provisioning dello spazio di archiviazione.
5. In **Network Mapping** (Mapping di rete) Contoso specifica la rete a cui connettere la macchina virtuale dell'agente di raccolta. La rete richiede la connettività Internet per l'invio dei metadati ad Azure.
6. Contoso esamina le impostazioni e quindi seleziona **Power on after deployment** > **Finish** (Accendi dopo la distribuzione, Fine). Quando viene creata l'appliance, viene visualizzato un messaggio che conferma il completamento dell'operazione.

### <a name="run-the-collector-to-discover-vms"></a>Eseguire l'agente di raccolta per individuare le macchine virtuali

A questo punto, Contoso esegue l'agente di raccolta per individuare le macchine virtuali. L'agente di raccolta attualmente supporta solo l'**inglese (Stati Uniti)** come lingua del sistema operativo e della propria interfaccia.

1. Nella console di vSphere Client, Contoso seleziona **Open Console** (Apri console). Contoso specifica le preferenze relative alla lingua, al fuso orario e alla password per la macchina virtuale dell'agente di raccolta.
2. Sul desktop, Contoso seleziona il collegamento **Esegui agente di raccolta**.

    ![Console di vSphere Client - Collegamento per l'agente di raccolta](./media/contoso-migration-assessment/collector-shortcut.png)

3. In Agente di raccolta di Azure Migrate, Contoso seleziona **Configura i prerequisiti**. Contoso accetta le condizioni di licenza e legge le informazioni di terze parti.
4. L'agente di raccolta controlla che la macchina virtuale abbia accesso a Internet, che l'ora sia sincronizzata e che il servizio dell'agente di raccolta sia in esecuzione. (Il servizio dell'agente di raccolta è installato per impostazione predefinita nella macchina virtuale.) Contoso installa anche VMware PowerCLI.

    > [!NOTE]
    > Si presume che la macchina virtuale abbia accesso diretto a Internet, senza usare un proxy.

    ![Agente di raccolta di Azure Migrate - Verificare i prerequisiti](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. In **Specificare i dettagli del server vCenter** Contoso immette il nome (FQDN) o l'indirizzo IP dell'istanza del server vCenter e le credenziali di sola lettura usate per l'individuazione.
6. Contoso seleziona un ambito per l'individuazione delle macchine virtuali. L'agente di raccolta può individuare solo le macchine virtuali all'interno dell'ambito specificato. L'ambito può essere impostato su una cartella, un data center o un cluster specifici, ma non deve contenere più di 1.500 macchine virtuali.

    ![Specificare i dettagli del server vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. In **Specify migration project** (Specificare il progetto di migrazione) Contoso immette l'ID e la chiave del progetto di Azure Migrate copiati dal portale. Per ottenere l'ID e la chiave del progetto, Contoso può passare alla pagina **Panoramica** del progetto > **Individua macchine virtuali**.  

    ![Specificare il progetto di migrazione](./media/contoso-migration-assessment/collector-connect-azure.png)

8. In **Visualizza lo stato della raccolta** è possibile monitorare l'individuazione e verificare che i metadati raccolti dalle macchine virtuali siano inclusi nell'ambito. L'agente di raccolta indica un tempo di individuazione approssimativo.

    ![Visualizzare lo stato della raccolta](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Verificare le macchine virtuali nel portale

Al termine della raccolta, Contoso controlla che le macchine virtuali vengano visualizzate nel portale:

1. Nel progetto di Azure Migrate, Contoso seleziona **Gestisci** > **Macchine virtuali**. Contoso controlla che vengano visualizzate le macchine virtuali che vuole individuare.

    ![Azure Migrate - Macchine virtuali individuate](./media/contoso-migration-assessment/discovery-complete.png)

2. Nelle macchine virtuali non sono attualmente installati gli agenti di Azure Migrate. Contoso deve installare gli agenti per visualizzare le dipendenze.

    ![Azure Migrate - È richiesta l'installazione degli agenti](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Passaggio 5: Preparare l'analisi delle dipendenze

Per visualizzare le dipendenze tra le macchine virtuali da valutare, Contoso scarica e installa gli agenti nelle macchine virtuali dell'app. Contoso installa gli agenti in tutte le macchine virtuali per le app dell'azienda, sia per Windows che per Linux.

### <a name="take-a-snapshot"></a>Creare uno snapshot

Per conservare una copia delle macchine virtuali prima della modifica, Contoso acquisisce uno snapshot prima di installare gli agenti.

![Snapshot del computer](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

1. In **Macchine** Contoso seleziona la macchina. Nella colonna **Dipendenze** Contoso seleziona **Richiede l'installazione**.
2. Nel riquadro **Individua macchine virtuali** Contoso:
    - Scarica Microsoft Monitoring Agent (MMA) e Dependency Agent per ogni macchina virtuale Windows.
    - Scarica MMA e Dependency Agent per ogni macchina virtuale Linux.
3. Contoso copia l'ID e la chiave dell'area di lavoro. L'ID e la chiave dell'area di lavoro sono richieste durante l'installazione di MMA.

    ![Download degli agenti](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Installare gli agenti nelle VM Windows

Contoso esegue l'installazione in ogni macchina virtuale.

#### <a name="install-the-mma-on-windows-vms"></a>Installare MMA nelle VM Windows

1. Contoso fa doppio clic sull'agente scaricato.
2. In **Cartella di destinazione** Contoso mantiene la cartella di installazione predefinita e quindi sceglie **Avanti**.
3. In **Opzioni di installazione dell'agente** Contoso seleziona **Connettere l'agente ad Azure Log Analytics** > **Avanti**.

    ![Installazione di Microsoft Monitoring Agent - Opzioni di installazione dell'agente](./media/contoso-migration-assessment/mma-install.png)

4. In **Azure Log Analytics** Contoso incolla l'ID e la chiave dell'area di lavoro copiati dal portale.

    ![Installazione di Microsoft Monitoring Agent - Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. In **Pronto per l'installazione** Contoso installa MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Installare Dependency Agent in VM Windows

1. Contoso fa doppio clic sul file di Dependency Agent scaricato.
2. Contoso accetta le condizioni di licenza e attende il completamento dell'installazione.

    ![Installazione di Dependency Agent - Installazione](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Installare gli agenti nelle macchine virtuali Linux

Contoso esegue l'installazione in ogni macchina virtuale.

#### <a name="install-the-mma-on-linux-vms"></a>Installare MMA nelle macchine virtuali Linux

1. Contoso installa la libreria ctypes Python in ogni macchina virtuale usando il comando seguente:

    `sudo apt-get install python-ctypeslib`
2. Contoso deve eseguire il comando per installare l'agente MMA come root. Per diventare root, Contoso esegue il comando seguente e immette la password per l'utente root:

    `sudo -i`
3. Contoso installa MMA:
    - Contoso immette l'ID e la chiave dell'area di lavoro nel comando.
    - I comandi sono per la versione a 64 bit.
    - La chiave primaria e l'ID dell'area di lavoro si trovano nel portale di Microsoft Operations Management Suite (OMS). Selezionare **Impostazioni** e quindi selezionare la scheda **Origini connesse**.
    - Eseguire i comandi seguenti per scaricare l'agente di OMS, convalidare il checksum, quindi installare e caricare l'agente:

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Installare Dependency Agent nelle macchine virtuali Linux

Dopo aver installato MMA, Contoso installa Dependency Agent nelle macchine virtuali Linux:

1. Dependency Agent viene installato nei computer Linux usando InstallDependencyAgent-Linux64.bin, uno script della shell con un file binario autoestraente. Contoso esegue il file usando sh oppure aggiunge autorizzazioni di esecuzione al file stesso.

2. Contoso installa Dependency Agent per Linux come root:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Passaggio 6: Eseguire e analizzare la valutazione delle macchine virtuali

In Contoso si possono ora verificare le dipendenze dei computer e si può creare un gruppo. Esegue quindi la valutazione per il gruppo.

### <a name="verify-dependencies-and-create-a-group"></a>Verificare le dipendenze e creare un gruppo

1. Per determinare quali computer analizzare, Contoso seleziona **Visualizza dipendenze**.

    ![Azure Migrate - Visualizzare le dipendenze dei computer](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Per SQLVM, la mappa delle dipendenze mostra i dettagli seguenti:

    - Gruppi di processi o processi con connessioni di rete attive in esecuzione in SQLVM durante il periodo di tempo specificato (per impostazione predefinita, un'ora).
    - Connessioni TCP in ingresso (client) e in uscita (server) da e verso tutti i computer dipendenti
    - I computer dipendenti in cui sono installati gli agenti di Azure Migrate vengono visualizzati in caselle separate.
    - Computer in cui non sono installati gli agenti, con le informazioni relative a porta e indirizzo IP.

3. Per i computer con l'agente installato (WEBVM), Contoso seleziona la casella per visualizzare altre informazioni. Le informazioni includono il nome di dominio completo, il sistema operativo e l'indirizzo MAC.

    ![Azure Migrate - Visualizzare le dipendenze di gruppo](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso seleziona le macchine virtuali da aggiungere al gruppo (SQLVM e WEBVM). Contoso usa CTRL+clic per selezionare più macchine virtuali.
5. Contoso seleziona **Crea gruppo** e quindi immette un nome (**smarthotelapp**).

    > [!NOTE]
    > Per visualizzare le dipendenze a un livello più dettagliato, si può espandere l'intervallo di tempo. È possibile selezionare una durata specifica oppure le date di inizio e di fine.

### <a name="run-an-assessment"></a>Eseguire una valutazione

1. In **Gruppi** Contoso apre il gruppo (**smarthotelapp**) e quindi seleziona **Crea valutazione**.

    ![Azure Migrate - Creare una valutazione](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Per visualizzare la valutazione, Contoso seleziona **Gestisci** > **Valutazioni**.

Contoso usa le impostazioni di valutazione predefinite, ma è possibile [personalizzare le impostazioni](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analizzare la valutazione delle VM

Una valutazione di Azure Migrate include informazioni sulla compatibilità delle macchine virtuali locali con Azure, sul dimensionamento corretto per la macchina virtuale Azure e sui costi mensili stimati di Azure.

![Azure Migrate - Report di valutazione](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Esaminare la classificazione di attendibilità

![Azure Migrate - Visualizzazione della valutazione](./media/contoso-migration-assessment/assessment-display.png)

Una valutazione ottiene una classificazione di attendibilità da 1 a 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle al livello massimo.
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione consente di stimare l'affidabilità dei consigli relativi alle dimensioni offerti da Azure Migrate.
- La classificazione di attendibilità è utile quando si esegue una *determinazione della dimensione in base alle prestazioni*. Azure Migrate potrebbe non avere sufficienti punti dati per eseguire il ridimensionamento in base all'utilizzo. Per un ridimensionamento *come in locale*, la classificazione di attendibilità è sempre 5 stelle, perché Azure Migrate ha a disposizione tutti i punti dati necessari per ridimensionare la macchina virtuale.
- Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili:

   Disponibilità dei punti dati | Classificazione di attendibilità
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

#### <a name="verify-azure-readiness"></a>Verificare l'idoneità per Azure

![Azure Migrate - Idoneità per la valutazione](./media/contoso-migration-assessment/azure-readiness.png)  

Il report di valutazione mostra le informazioni riepilogate nella tabella. Per visualizzare le dimensioni in base alle prestazioni, Azure Migrate necessita delle informazioni riportate di seguito. Se queste informazioni non possono essere raccolte, la valutazione del dimensionamento potrebbe non essere accurata.

- Dati sull'utilizzo di CPU e memoria.
- Operazioni di I/O al secondo in lettura/scrittura e velocità effettiva per ogni disco collegato alla VM.
- Informazioni su rete in ingresso/uscita per ogni scheda di rete collegata alla VM.

Impostazione | Indicazione | Dettagli
--- | --- | ---
**Idoneità della macchina virtuale per Azure** | Indica se la macchina virtuale è pronta per la migrazione. | Stati possibili:</br><br/>- Idonea per Azure<br/><br/>- Idonea con condizioni <br/><br/>- Non idonea per Azure<br/><br/>- Idoneità sconosciuta<br/><br/> Se una macchina virtuale non è pronta, Azure Migrate mostra alcune procedure di correzione.
**Dimensioni macchina virtuale di Azure** | Per le macchine virtuali pronte, Azure Migrate indica le dimensioni consigliate per la macchina virtuale di Azure. | Le dimensioni consigliate dipendono dalle proprietà della valutazione:<br/><br/>- Se è stato usato il dimensionamento in base alle prestazioni, viene considerata la cronologia delle prestazioni delle VM.<br/><br/>- Se è stata usata l'opzione *Come in locale*, il dimensionamento è basato sulle dimensioni delle macchine virtuali locali e i dati sull'utilizzo non vengono usati.
**Strumento suggerito** | Poiché gli agenti vengono eseguiti in macchine di Azure, Azure Migrate esamina i processi in esecuzione all'interno della macchina. Identifica se la macchina contiene database.
**Informazioni sulla VM** | Il report mostra le impostazioni della macchina virtuale locale, con informazioni su sistema operativo, tipo di avvio, disco e spazio di archiviazione.

#### <a name="review-monthly-cost-estimates"></a>Esaminare le stime dei costi mensili

Questa visualizzazione mostra il costo totale di calcolo e di archiviazione associato all'esecuzione delle macchine virtuali in Azure, oltre ai dettagli per ogni macchina.

![Azure Migrate - Costi di Azure](./media/contoso-migration-assessment/azure-costs.png)

- Le stime dei costi vengono calcolate usando le dimensioni consigliate per una macchina.
- I costi mensili stimati per il calcolo e l'archiviazione vengono aggregati per tutte le macchine virtuali del gruppo.

## <a name="clean-up-after-assessment"></a>Pulizia dopo la valutazione

- Al termine della valutazione, Contoso conserva l'appliance di Azure Migrate per usarla in valutazioni future.
- Contoso disattiva la macchina virtuale VMware. Contoso la userà di nuovo per la valutazione di ulteriori macchine virtuali.
- Contoso conserva il progetto **ContosoMigration** in Azure. Il progetto è attualmente distribuito nel gruppo di risorse **ContosoFailoverRG** nell'area di Azure Stati Uniti orientali.
-  La macchina virtuale dell'agente di raccolta ha una licenza di valutazione di 180 giorni. Se questo limite scade, Contoso dovrà scaricare e configurare di nuovo l'agente di raccolta.

## <a name="conclusion"></a>Conclusioni

In questo scenario, Contoso valuta il database dell'app SmartHotel tramite lo strumento Data Migration Assessment. Usa il servizio Azure Migrate per valutare le macchine virtuali in locale. Contoso esamina quindi le valutazioni per verificare l'idoneità delle risorse locali per la migrazione ad Azure.

## <a name="next-steps"></a>Passaggi successivi

Nel prossimo articolo di questa serie, Contoso esegue il rehosting dell'app SmartHotel in Azure con una migrazione di tipo lift-and-shift. Contoso esegue la migrazione della macchina virtuale WEBVM front-end per l'app tramite Azure Site Recovery. Esegue la migrazione del database dell'app a Istanza gestita di database SQL di Azure tramite il servizio Migrazione del database di Azure. [Attività iniziali](contoso-migration-rehost-vm-sql-managed-instance.md) con questa distribuzione.
