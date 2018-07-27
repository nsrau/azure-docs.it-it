---
title: Effettuare il refactoring di una distribuzione di Team Foundation Server per Visual Studio Team Services (VSTS) in Azure | Microsoft Docs
description: Scopri come Contoso effettua il refactoring della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 6d1d90ff0f9a49d3db9f4dc8894c9837942658f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215000"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Migrazione Contoso:  effettuare il refactoring di una distribuzione di Team Foundation Server per Visual Studio Team Services (VSTS)

Questo articolo illustra come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale tramite la migrazione a Visual Studio Team Services (VSTS) in Azure. Il team di sviluppo di Contoso ha usato i TFS per la collaborazione tra team e il controllo del codice sorgente per gli ultimi cinque anni. A questo punto, si desidera passare a una soluzione basata sul cloud per il lavoro di sviluppo e test di controllo del codice sorgente. Visual Studio Team Services avranno un ruolo nel passaggio a un modello DevOps e svilupperanno nuove app native cloud.

Questo documento è l'undicesimo di una serie di articoli che descrivono il modo in cui la società fittizia Contoso esegue la migrazione delle proprie risorse locali al cloud di Microsoft Azure. La serie include informazioni di base e scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. Gli scenari presentano una complessità crescente e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale dell'App Web mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) a un'istanza gestita di database SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione di SmartHotel in macchine virtuali IaaS di Azure usando il servizio Site Recovery.
[Articolo 6: Eseguire il rehosting nelle macchine virtuali di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue la migrazione dell'app Linux osTicket nelle macchine virtuali IaaS di Azure usando Azure Site Recovery.
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app Linux osTicket. Usa Site Recovery per la migrazione delle macchine virtuali e MySQL Workbench per eseguire la migrazione in un'istanza di Azure MySQL Server. | Disponibile
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure basata su contenitori e come esegue la migrazione del database di app ad Azure SQL Server. | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in Servizio app di Azure e Azure MySQL Server](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket al Servizio app di Azure, usando il contenitore Docker PHP 7.0. Viene eseguita la migrazione a GitHub di un codebase per la distribuzione. Viene eseguita la migrazione del database dell'app ad Azure MySQL. | Disponibile
Articolo 11: Eseguire il refactoring di una distribuzione di TFS in Visual Studio Team Services | Eseguire la migrazione dell'app di sviluppo TFS su Visual Studio Team Services di Azure | Questo articolo
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Illustra come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Illustra come Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app, Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile


## <a name="business-drivers"></a>Driver di business

Il team di leadership IT ha lavorato a stretto contatto con quello commerciale per identificare gli obbiettivi futuri. I partner commerciali non sono eccessivamente interessati agli strumenti e alle tecnologie di sviluppo, ma hanno colto i seguenti punti:

- **Software**: Indipendentemente da quale sia core business, tutte le aziende sono ora aziende di software, tra cui Contoso. La leadership aziendale è interessata a come l'IT consente di guidare l'azienda con nuove pratiche di lavoro per gli utenti e esperienze per i clienti.
- **Efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti. Ciò consentirà di rispondere ai requisiti del cliente in modo più efficiente. L'azienda avrà bisogno dell'IT per velocizzarsi, minimizzando gli sprechi di tempo e denaro.
- **Agilità**: Le IT Contoso devono rispondere alle esigenze aziendali e reagire più rapidamente del mercato, per garantire successo nell'economia globale. Le IT non devono costituire un ostacolo alle attività aziendali.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team cloud di Contoso ha fissato alcuni obiettivi per la migrazione su Visual Studio Team Services:

- È necessario uno strumento per la migrazione dei dati nel cloud. Alcuni processi manuali dovrebbero essere necessari.
- Deve essere effettuata la migrazione dei dati e della cronologia dell'elemento di lavoro dell'ultimo anno dell'ultimo anno.
- Non si intende configurare nuovi nomi utente e password. È necessario mantenere tutte le assegnazioni di sistema corrente.
- Si intende spostare lontano dal controllo della versione di Team Foundation (TFVC) su Git per il controllo del codice sorgente.
- Il trasferimento in Git è una "migrazione leggera" che importa solo la versione più recente del codice sorgente. Si verificherà in un tempo di inattività quando tutto il lavoro verrà interrotto mentre il codebase si sposta. Viene compreso che solo la cronologia attuale del ramo master sarà disponibile dopo lo spostamento.
- Si è preoccupati per la modifica e si vuole testarlo prima di eseguire un'operazione di spostamento completo. Si desidera mantenere l'accesso a TFS anche dopo il passaggio a Visual Studio Team Services.
- Dispone di più raccolte e intende iniziare con una che includa solo alcuni progetti per comprendere meglio il processo.
- Le raccolte TFS sono in una relazione uno a uno con gli account di Visual Studio Team Services, in modo da avere più URL. Tuttavia, ciò corrisponde al proprio modello di separazione per progetti e basi di codice corrente.


## <a name="proposed-architecture"></a>Architettura proposta

- Contoso passerà su cloud i propri progetti TFS e non ospiterà i progetti o il controllo del codice sorgente in locale.
- Verrà eseguita la migrazione di TFS a Visual Studio Team Services.
- Attualmente, Contoso dispone di una raccolta di TFS denominata **ContosoDev**, che verrà migrata su un account di Visual Studio Team Services chiamato **contosodevmigration.visualstudio.com**.
- I progetti, elementi di lavoro, bug e le iterazioni dell'ultimo anno verranno migrate a Visual Studio Team Services.
- Contoso userà Azure Active Directory, che verrà impostata quando [sarà distribuita l'infrastruttura Azure](contoso-migration-infrastructure.md) all'inizio della pianificazione della migrazione. 


![Architettura dello scenario](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Processo di migrazione

In Contoso il processo di migrazione verrà completato come indicato di seguito:

1. È prevista una lunga preparazione. Come primo passaggio, Contoso deve eseguire l'aggiornamento delle implementazione dei TFS a un livello supportato. Sono attualmente in esecuzione i TFS 2017 Update 3, ma per eseguire la migrazione di database è necessaria una versione del 2018 supportata con gli aggiornamenti più recenti.
2. Dopo l'aggiornamento, verrà avviato lo strumento di migrazione di TFS e la convalida della raccolta.
3. Verrà creato un set di file di preparazione ed eseguita una prova di migrazione.
4. Verrà quindi eseguita un'altra migrazione, questa volta una migrazione completa che include elementi di lavoro, bug, sprint e codici.
5. Dopo la migrazione, il codice passerà dal controllo della versione di Team Foundation a Git.

![Processo di migrazione](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>Passaggi dello scenario

Di seguito viene descritto il processo di completamento della migrazione di Azure:

> [!div class="checklist"]
> * **Passaggio 1: Creare un account di archiviazione di Azure**: questo account di archiviazione verrà usato durante il processo di migrazione.
> * **Passaggio 2: Aggiornare TFS**: verrà eseguito l'aggiornamento della distribuzione a TFS 2018 Upgrade 2. 
> * **Passaggio 3: Convalidare la raccolta**: la raccolta TFS verrà convalidata in preparazione per la migrazione.
> * **Passaggio 4: Compilazione dei file di preparazione**: verranno creati i file di migrazione utilizzando lo strumento di migrazione di TFS. 


## <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione

1. Nel portale di Azure, Contoso crea un account di archiviazione (**contosodevmigration**).
2. Verrà inserito l'account nella rispettiva area secondaria usata per il failover (Stati Uniti centrali). Viene usato un account per uso generico standard con archiviazione locale-ridondante.

    ![Account di archiviazione](./media/contoso-migration-tfs-vsts/storage1.png) 


**Ulteriore assistenza?**

- [Introduzione all'archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>Passaggio 2: Aggiornamento TFS

Contoso consente di aggiornare i server da TFS a TFS 2018 Update 2. Prima dell'avvio:

- Dopo aver scaricato [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/)
- Verifica dei [requisiti hardware](https://docs.microsoft.com/tfs/server/requirements)e lettura completa delle [note sulla versione](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) e [aggiornamento gotcha](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

L'aggiornamento viene eseguito come segue:

1. Per iniziare, viene eseguito il backup del server TFS (in esecuzione in una macchina virtuale VMware) e una copia shadow VMware.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. Viene avviato il programma di installazione TFS, e si sceglie il percorso di installazione. Il programma di installazione deve avere accesso a Internet.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. Al termine dell'installazione, viene avviata la configurazione guidata Server.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Dopo la verifica, la procedura guidata completa l'aggiornamento.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Contoso verifica l'installazione di TFS visualizzando progetti, elementi di lavoro e codice.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Alcuni aggiornamenti di TFS necessitano di eseguire la procedura guidata Configura le funzionalità dopo il completamento dell'aggiornamento. [Altre informazioni](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**Ulteriore assistenza?**

Scopri di più sull'[aggiornamento di TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>Passaggio 3: Convalida della raccolta TFS

Contoso avvia lo strumento di migrazione TFS sul database della raccolta ContosoDev per la convalida prima della migrazione.

1. Contoso scarica e decomprime lo [Strumento di migrazione TFS](https://www.microsoft.com/download/details.aspx?id=54274). È importante scaricare la versione per l'aggiornamento TFS in esecuzione. È possibile verificare la versione nella console di amministrazione.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Esecuzione dello strumento per eseguire la convalida, specificando l'URL della raccolta di progetti team:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. Lo strumento indica un errore.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. È stato individuato che i file di registro si trovano nella cartella **Registro**, appena prima della posizione dello strumento. Viene generato un file di registro per ogni convalida principale. **TfsMigration.log** contiene le informazioni principali.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Contoso trova questa voce, correlata all'identità.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Contoso esegue **TfsMigration validate /help** nella riga di comando e vede che il comando **/tenantDomainName** necessita della convalida delle identità.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Viene eseguito nuovamente il comando di convalida, con incluso questo valore, insieme alla denominazione di Azure AD: **TfsMigrator convalidare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Viene visualizzata una schermata di accesso Azure AD e vengono immesse le credenziali di un utente amministratore globale.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. La convalida viene superata e lo strumento è quindi confermato.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>Passaggio 4: Creare i file di migrazione

Una volta completata la convalida, Contoso può utilizzare lo strumento di migrazione TFS per compilare i file di migrazione.

1. Nello strumento viene eseguita la procedura di preparazione.

    **TfsMigrator prepare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Preparazione](./media/contoso-migration-tfs-vsts/prep1.png)

    La preparazione esegue le seguenti attività:
    - Analizza la raccolta per un elenco di tutti gli utenti e completa il registro della mappa di identificazione (**IdentityMapLog.csv**])
    - Prepara la connessione ad Azure Active Directory per trovare una corrispondenza per ogni identità.
    - Contoso ha già implementato e sincronizzato Azure AD tramite AD Connect, quindi la preparazione è in grado di trovare le identità corrispondenti e contrassegnarle come attive.

2. Una schermata di accesso Azure AD viene visualizzata e Contoso inserisce le credenziali di un utente amministratore globale.

    ![Preparazione](./media/contoso-migration-tfs-vsts/prep2.png)

3. La Preparazione viene completata e lo strumento indica che i file di importazione sono stati generati.

    ![Preparazione](./media/contoso-migration-tfs-vsts/prep3.png)

4. Ora Contoso è in grado di vedere che i file IdentityMapLog.csv e import.json sono stati creati in una nuova cartella.

    ![Preparazione](./media/contoso-migration-tfs-vsts/prep4.png)

5. Il file import.json fornisce le impostazioni di importazione. Include delle informazioni, quali il nome account desiderato e le informazioni sull'account di archiviazione. La maggior parte dei campi sono compilati automaticamente. Alcuni campi richiedono l'intervento dell'utente. Contoso apre il file e aggiunge il nome dell'account Visual Studio Team Services da creare: **contosodevmigration**. Con questo nome, il relativo URL di Visual Studio Team Services sarà **contosodevmigration.visualstudio.com**.

    ![Preparazione](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > L'account deve essere creato prima della migrazione e può essere modificato una volta eseguita la migrazione.

6. Viene esaminato il file della mappa del Registro di identità che mostra gli account che verranno visualizzati in Visual Studio Team Services durante l'importazione. 

    - Con identità attive si fa riferimento alle identità che diventeranno utenti in Visual Studio Team Services dopo l'importazione.
    - In Visual Studio Team Services, tali identità saranno autorizzate e verranno visualizzate come utenti nell'account dopo la migrazione.
    - Queste identità sono contrassegnate come **Attive** nella colonna nel file **Stato Importazione prevista**.

    ![Preparazione](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>Passaggio 5: Eseguire la migrazione a Visual Studio Team Services

Con la preparazione in atto, Contoso può concentrarsi sull'esecuzione della migrazione. Dopo aver eseguito la migrazione, si passerà da TFVC a Git per il controllo della versione.

Prima dell'avvio, Contoso consente di pianificare il tempo di inattività con il team di sviluppo, per richiedere la raccolta non in linea per la migrazione. I passaggi per il processo di migrazione sono i seguenti:

1. **Scollegare la raccolta**: i dati di identità per la raccolta risiedono nel database di configurazione del server TFS mentre la raccolta è collegata e in linea. Quando una raccolta viene scollegata dal server TFS, prende una copia di tali dati di identità e la impacchetta con la raccolta per il trasporto. Senza questi dati, non è possibile eseguire la partizione delle identità dell'importazione. È consigliabile che la raccolta rimanga scollegata fino al completamento dell'importazione, in quanto non è possibile importare le modifiche che si sono generate durante l'importazione.
2. **Generare un backup**: il passaggio successivo del processo di migrazione è generare un backup che può essere importato in Visual Studio Team Services. DACPAC (Application Component Packages) di livello dati, è una funzionalità di SQL Server che consente di impacchettare le modifiche del database in un singolo file e di distribuirle ad altre istanze di SQL. È anche possibile ripristinarla direttamente su Visual Studio Team Services e viene quindi utilizzata come metodo di impacchettamento per ottenere i dati di raccolta nel cloud. Contoso utilizzerà lo strumento SqlPackage.exe per generare il file DACPAC. Questo strumento è incluso in SQL Server Data Tools.
3. **Caricare in archiviazione**: una volta creato DACPAC, viene caricato in Archiviazione di Azure. Dopo il caricamento, si riceve una firma di accesso condiviso (SAS), per consentire l'accesso all'archiviazione allo strumento di migrazione di TFS.
4. **Compilare l'importazione**: Contoso può quindi compilare i campi mancanti nel file di importazione, inclusa l'impostazione di DACPAC. Per iniziare si specificherà che si desidera eseguire una **prova** per verificare che tutto funzioni correttamente, prima di eseguire la migrazione completa.
5. **Eseguire una prova**: le prove di importazione consentono di testare la migrazione di raccolte. Le prove hanno una durata limitata e vengono eliminate prima dell'esecuzione della migrazione di un ambiente di produzione. Vengono eliminate automaticamente dopo un periodo di tempo. Una notifica su quando verrà cancellata l'esecuzione di prova è inclusa nell'e-mail, ricevuta al termine dell'importazione. Prendere nota e pianificare di conseguenza.
6. **Completare la migrazione di produzione**: con la migrazione di prova completata, Contoso effettua la migrazione finale aggiornando import.json e eseguendo nuovamente l'importazione.



### <a name="detach-the-collection"></a>Rimuovere la raccolta

Prima di iniziare, Contoso acquisisce un backup locale di SQL Server e un'istantanea VMware del server TFS, prima di scollegarla.

1.  Nella console di amministrazione TFS, viene selezionata la raccolta che si desidera scollegare (**ContosoDev**).

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate1.png)

2. In **Generali**, infine, seleziona **Scollega raccolta**

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate2.png)

3. Nella procedura guidata di raccolta del progetto Team Detach> **Messaggio di manutenzione**, viene inviato un messaggio per gli utenti che tentano di connettersi ai progetti nella raccolta.

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate3.png)

4. In **Stato di avanzamento scollegamento**, monitora l'avanzamento e fa clic su **Successivo** al termine del processo.

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate4.png)

5. Una volta terminati i controlli, fa clic su **Scollega** in **Controlli di conformità**.

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Al termine, fa clic su **Chiudi**.

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate6.png)

6. La raccolta non viene più referenziata nella console di amministrazione TFS.

    ![Migrazione](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Generare un file DACPAC

Contoso crea un backup (con estensione DACPAC) da importare in VSTS.

- SqlPackage.exe consente di creare il file DACPAC in SQL Server Data Tools. Sono disponibili più versioni di SqlPackage.exe installato con SQL Server Data Tools, che si trova in cartelle con nomi come 120, 130 e 140. È importante usare la versione corretta per preparare il file DACPAC.
- Le importazioni di TFS 2018 usano SqlPackage.exe dalla cartella 140 o nella versione successiva.  Per CONTOSOTFS, questo file si trova nella cartella: **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso genera il file DACPAC nel modo seguente:

1. Viene aperto un prompt dei comandi e si passa alla posizione SQLPackage.exe. Digita il comando seguente per generare il file DACPAC:

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. Dopo l'esecuzione del comando, viene visualizzato il messaggio seguente.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Viene eseguita la verifica delle proprietà del file DACPAC

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Aggiorna il file nella memoria

Dopo aver creato il formato DACPAC, Contoso lo carica in Archiviazione di Azure.

1. Viene [scaricato e installato](https://azure.microsoft.com/features/storage-explorer/) Azure Storage Explorer.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup5.png)

4. Vengono connessi alla propria sottoscrizione e individuano l'account di archiviazione per la migrazione (**contosodevmigration**). Viene creato nuovo contenitore blob, **vstsmigration**.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup6.png)

5. Il file con estensione DACPAC per il caricamento è specificato come blob in blocchi.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. Una volta che il file è stato caricato, seleziona il nome file > **Genera firma di accesso condiviso**. I contenitori blob nell'account di archiviazione vengono espansi e il contenitore con i file di importazione viene selezionato, quindi fa clic su **Ottieni firma di accesso condiviso**.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup8.png)

8. Accetta le impostazioni predefinite e fa clic su **Crea**. Ciò consente l'accesso per 24 ore.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup9.png)

9. Viene copiato l'URL della firma di accesso condiviso, così da poter essere utilizzato dallo strumento di migrazione di TFS.

    ![Caricamento](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> La migrazione deve essere eseguita entro la finestra temporale consentita o le autorizzazioni scadranno.
> Non generare una chiave di firma di accesso condiviso dal portale di Azure. Le chiavi generate in questo modo sono associate all'account e non funzioneranno con l'importazione.

### <a name="fill-in-the-import-settings"></a>Specificare le impostazioni di importazione

In precedenza, Contoso ha compilato parzialmente il file delle specifiche di importazione (import.json). A questo punto è necessario aggiungere le impostazioni rimanenti.

Il file import.json viene aperto e compilato nei campi seguenti: •   Percorso: percorso della chiave di firma di accesso condiviso precedentemente generata.
•    Dacpac: impostare il nome del file DACPAC che è stato caricato nell'account di archiviazione. Includere l'estensione ".dacpac".
•   ImportType: per ora, impostare su importazione di prova.


![Importare impostazioni](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Eseguire una migrazione di prova

Contoso esegue una migrazione di prova per verificare che tutti gli elementi funzionino come previsto.

1. Viene aperto un prompt dei comandi e viene individuato il percorso TfsMigration (C:\TFSMigrator).
2. Come primo passaggio viene convalidato il file di importazione. Contoso desidera assicurarsi che il file sia formattato correttamente e che la chiave di firma di accesso condiviso sia funzionante.

    **TfsMigrator importare /importFile:C:\TFSMigrator\import.json /validateonly**

3. La convalida restituisce un errore per il quale la chiave di firma di accesso condiviso richiede tempi di scadenza più lunghi.

    ![Prova](./media/contoso-migration-tfs-vsts/test1.png)

3. Viene utilizzato Azure Storage Explorer per creare una nuova chiave di firma di accesso condiviso con scadenza impostata per sette giorni.

    ![Prova](./media/contoso-migration-tfs-vsts/test2.png)

3. Viene aggiornato il file import.json ed eseguita nuovamente la convalida. Questa volta viene completato correttamente.

    **TfsMigrator importare /importFile:C:\TFSMigrator\import.json /validateonly**

    ![Prova](./media/contoso-migration-tfs-vsts/test3.png)
    
7. Contoso avvia l'esecuzione di prova:

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. Viene generato un messaggio per confermare la migrazione. Si noti il periodo di tempo per il quale i dati di staging verranno mantenuti dopo l'esecuzione di prova.

    ![Prova](./media/contoso-migration-tfs-vsts/test4.png)

9. Accesso a Azure AD viene visualizzato e necessita di essere completato con l'accesso a Contoso Admin.

    ![Prova](./media/contoso-migration-tfs-vsts/test5.png)

10. Verrà visualizzato un messaggio relativo all'importazione.

    ![Prova](./media/contoso-migration-tfs-vsts/test6.png)

11. Dopo 15 minuti, Contoso passa all'URL e visualizza le informazioni seguenti:

     ![Prova](./media/contoso-migration-tfs-vsts/test7.png)

12. Al termine della migrazione, Contoso Dev Leads accede a Visual Studio Team Services per verificare che la prova funzioni correttamente. Dopo l'autenticazione, Visual Studio Team Services necessita di alcuni dettagli per confermare l'account.

    ![Prova](./media/contoso-migration-tfs-vsts/test8.png)

13. In Visual Studio Team Services, Dev Lead è possibile vedere che i progetti sono stati migrati in Visual Studio Team Services. È presente un avviso che l'account verrà eliminato dopo 15 giorni.

    ![Prova](./media/contoso-migration-tfs-vsts/test9.png)

14. Dev Lead apre uno dei progetti e apre **Elementi di lavoro** > **Assegnati a me**. Ciò indica che i dati di elemento di lavoro sono stati migrati, insieme alla propria identità.

    ![Prova](./media/contoso-migration-tfs-vsts/test10.png)

15. Vengono controllati anche gli altri progetti e il codice, per confermare che il codice sorgente e la cronologia sono stati migrati.

    ![Prova](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Eseguire la migrazione di produzione

Con la prova completata, Contoso passa alla migrazione di produzione. Viene eliminata l'esecuzione di prova, aggiornate le impostazioni di importazione ed eseguita nuovamente l'importazione.

1. Nel portale di Visual Studio Team Services, si elimina l'account di prova.
2. Viene aggiornato il file import.json per impostare il **ImportType** al **ProductionRun**.

    ![Produzione](./media/contoso-migration-tfs-vsts/full1.png)

3. Viene avviata la migrazione, alla stessa maniera dell'esecuzione di prova: **TfsMigrator importazione /importFile:C:\TFSMigrator\import.json**.
4. Viene visualizzato un messaggio per confermare la migrazione e informare che i dati potrebbero essere conservati in un luogo sicuro come area di sosta per un massimo di sette giorni.

    ![Produzione](./media/contoso-migration-tfs-vsts/full2.png)

5. In Accesso di Azure AD, Contoso specifica un accesso di amministratore Contoso.

    ![Produzione](./media/contoso-migration-tfs-vsts/full3.png)

6. Verrà visualizzato un messaggio relativo all'importazione.

    ![Produzione](./media/contoso-migration-tfs-vsts/full4.png)

7. Dopo 15 minuti, Contoso passa all'URL e visualizza le informazioni seguenti:

    ![Produzione](./media/contoso-migration-tfs-vsts/full5.png)

8. Al termine della migrazione, Contoso Dev Leads accede a Visual Studio Team Services per verificare che la prova funzioni correttamente. Dopo l'accesso, è possibile rilevare che è stata eseguita la migrazione dei progetti.

    ![Produzione](./media/contoso-migration-tfs-vsts/full6.png)

8. Dev Lead apre uno dei progetti e apre **Elementi di lavoro** > **Assegnati a me**. Ciò indica che i dati di elemento di lavoro sono stati migrati, insieme alla propria identità.

    ![Produzione](./media/contoso-migration-tfs-vsts/full7.png)

9. Viene controllata la presenza di altri dati dell'elemento di lavoro da confermare.

    ![Produzione](./media/contoso-migration-tfs-vsts/full8.png)

15. Vengono controllati anche gli altri progetti e il codice, per confermare che il codice sorgente e la cronologia sono stati migrati.

    ![Produzione](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>Spostare il codice sorgente da controllo della versione di Team Foundation a GIT

Una volta completata la migrazione, Contoso desidera spostare da controllo della versione di Team Foundation a Git per la gestione del codice sorgente. È necessario importare il codice sorgente attualmente nel proprio account di Visual Studio Team Services come repository Git nello stesso account.

1. Nel portale di Visual Studio Team Services, si apre uno dei repository Controllo della versione di Team Foundation (**$/ PolicyConnect**) e viene verificato.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Facendo clic su **Origine** elenco a discesa > **Importa**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. In **Tipo di origine** selezionare **Controllo della versione di Team Foundation**  e specificare il percorso nel repository. Si è deciso di non eseguire la migrazione della cronologia.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > A causa delle differenze nel modo in cui TFVC e Git archiviano le informazioni di controllo di versione, è consigliabile non eseguire la migrazione della cronologia. Questo è l'approccio adottato da Microsoft durante la migrazione di Windows e di altri prodotti, dal controllo della versione centralizzata a Git.

4. Dopo l'importazione, Contoso esamina il codice.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Viene ripetuto il processo per il secondo repository (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Dopo aver esaminato l'origine, il Dev lead decreta come completata la migrazione a Visual Studio Team Services. Visual Studio Team Services diventa l'origine per i progetti di sviluppo all'interno dei team coinvolti nel processo di migrazione.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**Ulteriore assistenza?**

[Altre informazioni](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) sull'importazione di repository da controllo della versione di Team Foundation.

##  <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Una volta che la migrazione è completa, Contoso deve eseguire le operazioni seguenti:

- Esaminare l'articolo [post-importazione](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) per informazioni su attività di importazione aggiuntive.
- Eliminare i repository Controllo della versione di Team Foundation o inserirli in modalità di sola lettura. Le basi di codice non devono essere utilizzate, ma possono essere referenziate per la loro cronologia.

## <a name="next-steps"></a>Passaggi successivi

Contoso dovrà fornire formazione su Visual Studio Team Services e Git per i membri del team pertinenti.



