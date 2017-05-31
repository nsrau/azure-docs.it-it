---

title: Usare il server di Backup di Azure per eseguire il backup dei carichi di lavoro in Azure | Documentazione Microsoft
description: Usare il server di Backup di Azure per proteggere o eseguire il backup dei carichi di lavoro nel portale di Azure.
services: backup
documentationcenter: 
author: PVRK
manager: shivamg
editor: 
keywords: server di Backup di Azure; protezione dei carichi di lavoro; backup dei carichi di lavoro
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: e8838095d621957fe6b4ee50464154617e1c4740
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Preparazione del backup dei carichi di lavoro con il server di Backup di Azure
> [!div class="op_single_selector"]
> * [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Server di Backup di Azure (classico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (classico)](backup-azure-dpm-introduction-classic.md)
>
>

Questo articolo illustra la preparazione dell'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Azure. Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console.

> [!NOTE]
> Il server di Backup di Azure ora consente di proteggere le VM VMware e offre migliori funzionalità di sicurezza. È necessario installare il prodotto come illustrato nelle sezioni seguenti e applicare l'aggiornamento 1 e l'agente di Backup di Azure più recente. Per altre informazioni sul backup VMware con il server di Backup di Azure, vedere il [blog delle procedure](https://azure.microsoft.com/blog/four-simple-steps-to-backup-vmware-vms-using-azure-backup-server/). Per informazioni sulle funzionalità di sicurezza, vedere la [documentazione sulle funzionalità di sicurezza di Backup di Azure](backup-azure-security-feature.md).
>
>

È anche possibile proteggere carichi di lavoro dell'infrastruttura distribuita come servizio (IaaS), ad esempio VM in Azure.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

Il server di Backup di Azure eredita molte funzionalità per il backup dei carichi di lavoro da Data Protection Manager (DPM). Questo articolo contiene collegamenti alla documentazione di DPM in cui vengono illustrate alcune delle funzionalità condivise. Nonostante il server di Backup di Azure condivida molte funzionalità di DPM, non offre il backup su nastro né l'integrazione con System Center.

## <a name="1-choose-an-installation-platform"></a>1. Scegliere una piattaforma di installazione
Per rendere operativo il server di Backup di Azure, è prima di tutto necessario installare un server Windows, in Azure o in locale.

### <a name="using-a-server-in-azure"></a>Uso di un server in Azure
Quando si sceglie un server per eseguire il server di Backup di Azure, è consigliabile usare come punto di partenza un'immagine della raccolta di Windows Server 2012 R2 Datacenter. L'articolo [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)offre un'esercitazione per implementare la macchina virtuale consigliata in Azure anche se si usa Azure per la prima volta. I requisiti minimi consigliati per la macchina virtuale (VM) server sono A2 Standard, 2 core e 3,5 GB di RAM.

La protezione dei carichi di lavoro con il server di Backup di Azure è piuttosto complessa. L'articolo [Installare DPM come una macchina virtuale di Azure](https://technet.microsoft.com/library/jj852163.aspx)ne illustra i diversi aspetti. Leggere completamente questo articolo prima di distribuire la macchina.

### <a name="using-an-on-premises-server"></a>Uso di un server locale
Se non si vuole eseguire il server di base in Azure, è possibile eseguire il server in una VM Hyper-V o VMware oppure in un host fisico. I requisiti minimi consigliati per l'hardware del server sono 2 core e 4 GB di RAM. Nella tabella seguente sono elencati i sistemi operativi supportati.

| Sistema operativo | Piattaforma | SKU |
|:--- | --- |:--- |
| Windows Server 2012 R2 e più recenti SPs |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 e versioni più recenti di SP |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 e versioni più recenti di SP |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 e versioni più recenti di SP |64 bit |Standard, Workgroup |

È possibile deduplicare la risorsa di archiviazione DPM usando la deduplicazione di Windows Server. Vedere altre informazioni sull'interazione di [DPM e deduplicazione](https://technet.microsoft.com/library/dn891438.aspx) in caso di distribuzione in macchine virtuali Hyper-V.

> [!NOTE]
> Il server di Backup di Azure è progettato per essere eseguito su un server dedicato, con un unico scopo. Non è possibile installare il server di Backup di Azure su:
> - Un computer in esecuzione come controller di dominio
> - Un computer in cui è installato il ruolo di server applicazioni
> - Un computer che sia un server di gestione di System Center Operations Manager
> - Un computer su cui è in esecuzione Exchange Server
> - Un computer che sia un nodo di un cluster

È necessario aggiungere il server di backup di Azure a un dominio. Se si prevede di spostare il server in un dominio diverso, è consigliabile aggiungere il server al nuovo dominio prima di installare il server di backup di Azure. Lo spostamento di un server di Backup di Azure esistente in un nuovo dominio dopo la distribuzione *non è supportato*.

## <a name="2-recovery-services-vault"></a>2. Insieme di credenziali dei servizi di ripristino
Indipendentemente dal fatto che i dati di backup vengano inviati ad Azure o vengano archiviati in locale, il software deve essere connesso ad Azure. In particolare, il computer del server di Backup di Azure deve essere registrato in un insieme di credenziali dei servizi di ripristino.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Sfoglia** dal menu Hub e digitare **Servizi di ripristino** nell'elenco di risorse. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.
3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    Verrà visualizzato il pannello degli insiemi di credenziali dei servizi di ripristino, in cui viene richiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Fare clic su **Sottoscrizione** per visualizzare l'elenco di sottoscrizioni disponibili. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.
6. Fare clic su **Gruppo di risorse** per visualizzare l'elenco di gruppi di risorse disponibili oppure fare clic su **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali.
8. Fare clic su **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale.
   Una volta creato, l'insieme di credenziali viene aperto nel portale.

### <a name="set-storage-replication"></a>Impostare la replica di archiviazione
L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e con [ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere la panoramica [Replica di Archiviazione di Azure](../storage/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Selezionare l'insieme di credenziali per aprire il dashboard dell'insieme di credenziali e il pannello Impostazioni. Se il pannello **Impostazioni** non si apre, fare clic su **Tutte le impostazioni** nel dashboard dell'insieme di credenziali.
2. Nel pannello **Impostazioni** fare clic su **Infrastruttura di backup** > **Configurazione backup** per aprire il pannello **Configurazione backup**. Nel pannello **Configurazione backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la macchina virtuale all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## <a name="3-software-package"></a>3. Pacchetto software
### <a name="downloading-the-software-package"></a>Download del pacchetto software
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Se è già aperto un insieme di credenziali dei servizi di ripristino, procedere al passaggio 3. Se non è aperto alcun insieme di credenziali dei servizi di ripristino ma si è nel portale di Azure, nel menu dell'hub fare clic su **Esplora**.

   * Nell'elenco di risorse digitare **servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

     ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
   * Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.

     ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Per impostazione predefinita si apre il pannello **Impostazioni**. Se non viene visualizzato, fare clic su **Impostazioni** per aprire il pannello delle impostazioni.

    ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Fare clic su **Backup** per aprire la procedura guidata.

    ![Attività iniziali di backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Nel pannello **Introduzione al backup** visualizzato, sarà automaticamente selezionato **Obiettivo di backup**.

    ![Backup-obiettivi-predefinito-aperto](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Nel pannello **Obiettivo di backup** selezionare **Locale** dal menu **Dove è in esecuzione il carico di lavoro**.

    ![locale e carichi di lavoro come obiettivi](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Dal menu a discesa **Elementi di cui eseguire il backup** selezionare i carichi di lavoro da proteggere con il server di Backup di Azure e quindi fare clic su **OK**.

    La procedura guidata **Introduzione al backup** imposta l'opzione **Preparare l'infrastruttura** per eseguire il backup dei carichi di lavoro in Azure.

   > [!NOTE]
   > Se si vuole solo eseguire il backup di file e cartelle, è consigliabile usare l'agente di Backup di Azure e seguire le istruzioni disponibili nell'articolo [Primi passi: eseguire il backup di file e cartelle](backup-try-azure-backup-in-10-mins.md). Se si prevede di proteggere altri carichi di lavoro oltre a file e cartelle oppure se si vuole in futuro espandere i requisiti di protezione, selezionare quei carichi di lavoro.
   >
   >

    ![Attività iniziali guidate modifica](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Nel pannello **Preparare l'infrastruttura** visualizzato, fare clic sui collegamenti **Scarica** per installare il server di Backup di Azure e scaricare le credenziali dell'insieme di credenziali. Tali credenziali vengono usate durante la registrazione del server di Backup di Azure nell'insieme di credenziali dei servizi di ripristino. I collegamenti consentono di accedere alla pagina dell'Area download da cui è possibile scaricare il pacchetto software.

    ![Preparare l'infrastruttura per il Server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selezionare tutti i file e fare clic su **Avanti**. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure e salvarli nella stessa cartella.

    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Poiché le dimensioni di download di tutti questi file è pari a > 3 GB, per il download completo potrebbero essere necessari fino a 60 minuti su un collegamento di download a 10 Mbps.

### <a name="extracting-the-software-package"></a>Estrazione del pacchetto software
Dopo aver scaricato tutti i file, fare clic su **MicrosoftAzureBackupInstaller.exe**. Verrà avviata l'installazione guidata **Backup di Microsoft Azure** per estrarre i file di installazione in un percorso specificato dall'utente. Continuare la procedura guidata e fare clic sul pulsante **Estrai** per avviare il processo di estrazione.

> [!WARNING]
> Per estrarre i file di installazione sono necessari almeno 4 GB di spazio libero.
>
>

![Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Al termine del processo di estrazione, selezionare la casella per avviare il file *setup.exe* appena estratto e iniziare l'installazione del server di Backup di Microsoft Azure e quindi fare clic sul pulsante **Fine** .

### <a name="installing-the-software-package"></a>Installazione del pacchetto software
1. Fare clic su **Backup di Microsoft Azure** per avviare l'installazione guidata.

    ![Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Nella schermata iniziale fare clic sul pulsante **Avanti** . Verrà visualizzata la sezione *Prerequisite Checks* (Controlli dei prerequisiti). In questa schermata fare clic sul pulsante **Controlla** per determinare se i prerequisiti hardware e software per il server di Backup di Azure sono stati soddisfatti. Se tutti i prerequisiti sono stati soddisfatti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Fare clic sul pulsante **Avanti** .

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Il server di Backup di Microsoft Azure richiede SQL Server Standard e il pacchetto di installazione del server di Backup di Azure include i file binari appropriati di SQL Server necessari. Quando si avvia una nuova installazione del server di Backup di Azure, è consigliabile selezionare l'opzione **Installa una nuova istanza di SQL Server con questa installazione** e fare clic sul pulsante **Controlla e installa**. Una volta che i prerequisiti vengono installati correttamente, fare clic su **Avanti**.

    ![Server di backup di Azure - Controllo SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se si verifica un errore con l'indicazione di riavviare il computer, eseguire questa operazione e fare clic su **Controlla di nuovo**.

   > [!NOTE]
   > Il server di Backup di Azure non funzionerà con un'istanza remota di SQL Server. L'istanza usata dal server di Backup di Azure deve essere locale.
   >
   >
4. Specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Fornire una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selezionare se si vuole usare *Microsoft Update* per cercare gli aggiornamenti e fare clic su **Avanti**.

   > [!NOTE]
   > È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.
   >
   >

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L'installazione avviene a fasi. Nella prima fase viene eseguita l'installazione dell'Agente servizi di ripristino di Microsoft Azure sul server. La procedura guidata verifica anche la connettività Internet. Se la connettività Internet è disponibile, si può procedere con l'installazione, in caso contrario è necessario fornire i dettagli relativi al proxy per connettersi a Internet.

    Il passaggio successivo consiste nel configurare l'Agente servizi di ripristino di Microsoft Azure. Come parte della configurazione, è necessario specificare le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali dei servizi di ripristino. Sarà anche necessario specificare una passphrase per crittografare/decrittografare i dati inviati tra Azure e l'istanza locale. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Continuare la procedura guidata per completare la configurazione dell'agente.

    ![PreReq2 del server di backup di Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Al termine della registrazione del server di Backup di Microsoft Azure, l'installazione guidata generale passa all'installazione e alla configurazione dei componenti di SQL Server e del server di Backup di Azure. Al termine dell'installazione dei componenti di SQL Server, vengono installati i componenti del server di Backup di Azure.

    ![Server di backup di Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Al termine della fase di installazione, verranno create anche le icone sul desktop per il prodotto. È sufficiente fare doppio clic sull'icona per avviare il prodotto.

### <a name="add-backup-storage"></a>Aggiungere risorse di archiviazione di backup
La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura attuale del server di Backup di Azure, l'insieme di credenziali di Backup di Azure contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima (obbligatoria) copia di backup.
>
>

## <a name="4-network-connectivity"></a>4. Connettività di rete
Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per convalidare la connettività del computer ad Azure, usare il cmdlet ```Get-DPMCloudConnection``` nella console di PowerShell del server di Backup di Azure. C'è connettività solo se l'output di cmdlet è TRUE.

Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro. Per verificare lo stato della sottoscrizione e gestirla, accedere al [portale delle sottoscrizioni](https://account.windowsazure.com/Subscriptions).

Dopo avere verificato lo stato della connettività di Azure e della sottoscrizione di Azure, è possibile usare la tabella seguente per scoprire l'impatto della funzionalità di backup/ripristino offerta.

| Stato di connettività | Sottoscrizione di Azure | Backup in Azure | Backup su disco | Ripristino da Azure | Ripristino da disco |
| --- | --- | --- | --- | --- | --- |
| Connesso |Attivo |Consentito |Consentito |Consentito |Consentito |
| Connesso |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connesso |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |
| Connettività persa > 15 giorni |Attivo |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |

### <a name="recovering-from-loss-of-connectivity"></a>Recupero dalla perdita di connettività
Se è presente un firewall o un proxy che impedisce l'accesso ad Azure, sarà necessario aggiungere all'elenco elementi consentiti gli indirizzi di dominio seguenti nel profilo del firewall/proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Dopo il ripristino della connettività ad Azure nel computer del server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. La tabella precedente include dettagli sulle operazioni consentite quando il computer è "Connesso".

### <a name="handling-subscription-states"></a>Gestione degli stati della sottoscrizione
È possibile modificare lo stato di una sottoscrizione di Azure da *Scaduta* o *Deprovisioning eseguito* ad *Attiva*. Ciò influisce però sul comportamento del prodotto quando lo stato è diverso da *Attiva*:

* Una sottoscrizione con stato *Deprovisioning eseguito* perde funzionalità per il periodo in cui è sottoposta a deprovisioning. Dopo l'impostazione su *Attiva*, la funzionalità di backup/ripristino del prodotto viene riattivata. I dati di backup nel disco locale possono essere recuperati anche se sono stati memorizzati con un periodo di conservazione sufficientemente elevato. I dati di backup in Azure, invece, vengono persi definitivamente al passaggio della sottoscrizione allo stato *Deprovisioning eseguito* .
* Una sottoscrizione *Scaduta* perde funzionalità solo fino a quando non viene resa di nuovo *Attiva*. Eventuali backup pianificati per il periodo in cui la sottoscrizione è *Scaduta* non verranno eseguiti.

## <a name="troubleshooting"></a>Risoluzione dei problemi
In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni.
È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sulla [preparazione dell'ambiente per DPM](https://technet.microsoft.com/library/hh758176.aspx) , vedere il sito Web Microsoft TechNet. Contiene anche informazioni sulle configurazioni supportate in cui è possibile distribuire e usare il server di Backup di Azure.

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

* [Backup di SQL Server](backup-azure-backup-sql.md)
* [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
* [Backup del server alternativo](backup-azure-alternate-dpm-server.md)

