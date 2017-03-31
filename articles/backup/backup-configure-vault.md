---
title: Usare l&quot;agente di Azure Backup per eseguire il backup di file e cartelle | Documentazione Microsoft
description: Usare l&quot;agente di Backup di Microsoft Azure per eseguire il backup di file e cartelle Windows in Azure. Creare un insieme di credenziali di Servizi di ripristino, installare l&quot;agente di Backup, definire i criteri di backup ed eseguire il backup iniziale di file e cartelle.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: insieme di credenziali di backup; backup di un server Windows; backup di Windows;
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/23/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: b188affca609dd5ff3aa0d2cba3ec81c1c91888f
ms.openlocfilehash: 3528294d944fd71fc98a30e2736e1245e50c3be6
ms.lasthandoff: 02/24/2017


---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Eseguire il backup di un client o server Windows in Azure con Backup di Azure usando il modello di distribuzione Resource Manager
> [!div class="op_single_selector"]
> * [Portale di Azure](backup-configure-vault.md)
> * [Portale classico](backup-configure-vault-classic.md)
>
>

Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure con Backup di Azure tramite il modello di distribuzione di Resource Manager.

[!INCLUDE [learn-about-deployment-models](../../includes/backup-deployment-models.md)]

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare
Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
Un insieme di credenziali dei servizi di ripristino è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup applicati ai file e alle cartelle protette. Quando si crea un insieme di credenziali dei servizi di ripristino, è necessario selezionare anche l'opzione di ridondanza di archiviazione appropriata.

### <a name="to-create-a-recovery-services-vault"></a>Per creare un insieme di credenziali di Servizi di ripristino
1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com/) , tramite la sottoscrizione di Azure.
2. Scegliere **Altri servizi** dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino**, quindi fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se presenti nella sottoscrizione, gli insiemi di credenziali dei servizi di ripristino vengono elencati.

3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Verrà visualizzato il pannello degli insiemi di credenziali dei servizi di ripristino, in cui viene richiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Nella sezione **Sottoscrizione** usare il menu a discesa per scegliere la sottoscrizione di Azure. Se si usa una sola sottoscrizione, questa verrà visualizzata e sarà possibile andare al passaggio successivo. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono disponibili più scelte solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Nella sezione **Gruppo di risorse**:

    * Selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
    Or
    * Selezionare **Usa esistente** e fare clic sul menu a discesa per visualizzare l'elenco di gruppi di risorse disponibili.

  Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup.

8. Nella parte inferiore del pannello Insieme di credenziali dei servizi di ripristino fare clic su **Crea**.

  La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. L'insieme di credenziali, dopo essere stato creato, viene visualizzato negli insiemi di credenziali di Servizi di ripristino. Se l'insieme di credenziali non viene visualizzato dopo qualche minuto, fare clic su **Aggiorna**.

  ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  Dopo la visualizzazione dell'insieme di credenziali nell'elenco corrispondente per i Servizi di ripristino, è possibile configurare la ridondanza di archiviazione.


### <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione
Quando si crea per la prima volta un insieme di credenziali di Servizi di ripristino, si determina come replicare lo spazio di archiviazione.

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali.

    ![Selezionare il nuovo insieme di credenziali dall'elenco corrispondente per Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando si seleziona l'insieme di credenziali, il pannello **Insieme di credenziali dei servizi di ripristino** si restringe e vengono aperti il pannello Impostazioni,*con il nome dell'insieme di credenziali nella parte superiore*, e il pannello dei dettagli dell'insieme di credenziali.

    ![Visualizzare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. Nel pannello Impostazioni del nuovo insieme di credenziali usare il dispositivo di scorrimento verticale per passare alla sezione Gestisci e fare clic su **Infrastruttura di backup**.

  Verrà visualizzato il pannello Infrastruttura di backup.

3. Nel pannello Infrastruttura di backup fare clic su **Configurazione backup** per aprire il pannello **Configurazione backup**.

  ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)

4. Scegliere l'opzione di replica di archiviazione appropriata per l'insieme di credenziali.

  ![opzioni di configurazione dell'archiviazione](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

  Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'opzione **Con ridondanza geografica**. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e [con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), vedere [Panoramica della ridondanza di archiviazione](../storage/storage-redundancy.md).

Dopo avere creato un insieme di credenziali, preparare l'infrastruttura per il backup di file e cartelle scaricando e installando l'agente di Servizi di ripristino di Microsoft Azure, scaricando le credenziali dell'insieme di credenziali e usandole per registrare l'agente con l'insieme di credenziali.

## <a name="configure-the-vault"></a>Configurare l'insieme di credenziali

1. Nel pannello dell'insieme di credenziali dei servizi di ripristino appena creato, nella sezione Attività iniziali fare clic su **Backup** e nel pannello **Introduzione al backup** selezionare **Obiettivo del backup**.

  ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Verrà visualizzato il pannello **Obiettivo del backup**. Se l'insieme di credenziali di Servizi di ripristino è stato configurato in precedenza, viene visualizzato il pannello **Obiettivo del backup** quando si fa clic su **Backup** nel pannello dell'insieme di credenziali di Servizi di ripristino.

  ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Scegliere **Locale** dal menu a discesa **Posizione di esecuzione del carico di lavoro**.

  Si sceglie **Locale** perché il server di Windows o il computer Windows è un computer fisico che non si trova in Azure.

3. Scegliere **File e cartelle** dal menu **Elementi di cui eseguire il backup**, quindi fare clic su **OK**.

  ![Configurazione di file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

  Dopo aver fatto clic su OK verrà visualizzato un segno di spunta accanto a **Obiettivo del backup** e si aprirà il pannello **Preparare l'infrastruttura**.

  ![Preparare l'infrastruttura dopo aver configurato l'obiettivo del backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Nel pannello **Preparare l'infrastruttura** fare clic su **Scaricare l'agente per Windows Server o Windows Client**.

  ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

  Se si usa Windows Server Essentials, scegliere di scaricare l'agente per Windows Server Essentials. Un menu a comparsa chiederà di eseguire o salvare MARSAgentInstaller.exe.

  ![Finestra di dialogo di MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Fare clic su **Salva** nel menu a comparsa del download.

  Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads. Al termine del programma di installazione verrà visualizzato un messaggio popup che chiede se eseguire il programma di installazione o aprire la cartella.

  ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

  Non è ancora necessario installare l'agente. È possibile installare l'agente al termine del download delle credenziali dell'insieme di credenziali.

6. Fare clic su **Scarica** nel pannello **Preparare l'infrastruttura**.

  ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

  Le credenziali dell'insieme di credenziali verranno scaricate nella cartella Download locale. Al termine del download delle credenziali dell'insieme di credenziali verrà visualizzato un messaggio popup che chiede se aprire o salvare le credenziali. Fare clic su **Save**. Se si fa clic accidentalmente su **Apri**, attendere che il tentativo di apertura delle credenziali termini con un errore. Non è possibile aprire le credenziali dell'insieme di credenziali. Procedere con il passaggio successivo. Le credenziali dell'insieme di credenziali si trovano nella cartella Downloads.   

  ![Il download delle credenziali dell'insieme di credenziali è terminato](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

> [!NOTE]
> L'abilitazione del backup tramite il portale di Azure non è ancora disponibile. Usare l'agente di Servizi di ripristino di Microsoft Azure per eseguire il backup di file e cartelle.
>

1. Cercare e fare doppio clic sul file **MARSagentinstaller.exe** nella cartella Downloads o nella cartella in cui è stato salvato.

  Il programma di installazione visualizzerà una serie di messaggi durante l'estrazione, l'installazione e la registrazione dell'agente di Servizi di ripristino.

  ![Eseguire il programma di installazione dell'agente di Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Completare l'Installazione guidata di Agente servizi di ripristino di Microsoft Azure. Per completare la procedura guidata, è necessario:

  * Scegliere un percorso per la cartella di installazione e della cache.
  * Fornire le informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
  * Se si usa un proxy autenticato, immettere il nome utente e la password.
  * Fornire le credenziali dell'insieme di credenziali scaricate.
  * Salvare la passphrase di crittografia in un luogo sicuro.

  > [!NOTE]
  > Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. Salvare il file in una posizione sicura. È necessario per ripristinare un backup.
  >
  >

L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.


## <a name="create-the-backup-policy"></a>Creare i criteri di backup
I criteri di backup costituiscono la pianificazione per l'acquisizione degli snapshot di backup e la durata di conservazione di questi snapshot. Usare l'agente di Backup di Microsoft Azure per creare il criterio di backup di file e cartelle.

### <a name="to-create-a-backup-schedule"></a>Per creare una pianificazione di backup
1. Aprire l'agente Backup di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Avviare Azure Backup Agent](./media/backup-configure-vault/snap-in-search.png)
2. Nel riquadro **Azioni** dell'agente di Backup fare clic su **Pianifica backup** per avviare la Pianificazione guidata backup.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Nella pagina **Attività iniziali** della Pianificazione guidata backup fare clic su **Avanti**.
4. Nella pagina **Seleziona elementi per backup** fare clic su **Aggiungi elementi**.

  Verrà visualizzata la finestra di dialogo Seleziona elementi.

5. Selezionare i file e le cartelle da proteggere e fare clic su **OK**.
6. Nella pagina **Seleziona elementi per backup** fare clic su **Avanti**.
7. Nella pagina **Specificare la pianificazione del backup** specificare la pianificazione del backup e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. Nella pagina ** 	Seleziona i criteri di conservazione** selezionare i criteri di conservazione specifici per la copia di backup e fare clic su **Avanti**.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.
9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.
10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.
11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda
L'agente di Microsoft Backup di Azure consente di limitare la larghezza di banda della rete. La limitazione controlla l'uso della larghezza di banda della rete durante il trasferimento dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione si applica alle attività di backup e ripristino.

> [!NOTE]
> La limitazione di rete non è disponibile su Windows Server 2008 R2 SP1, Windows Server 2008 SP2 o Windows 7 (con i pacchetti di servizio). La funzione di limitazione della rete di Backup di Azure attiva il QoS ( Quality of Service) sul sistema operativo locale. Anche se il Backup di Azure è in grado di proteggere questi sistemi operativi, la versione del QoS disponibile su queste piattaforme non funziona con la limitazione di rete di Backup di Azure. La limitazione di rete può essere utilizzata in tutti gli altri [sistemi operativi supportati](backup-azure-backup-faq.md).
>
>

**Per abilitare la limitazione larghezza di banda**

1. Nell'agente di Backup di Microsoft Azure fare clic su **Modifica proprietà**.

    ![Modifica proprietà](./media/backup-configure-vault/change-properties.png)
2. Nella scheda **Limitazione larghezza di banda rete** selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault/throttling-dialog.png)
3. Dopo aver abilitato la limitazione, specificare la larghezza di banda consentita per trasferire i dati di backup durante le **ore lavorative** e le **ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobit al secondo (Kbps) e possono arrivare fino a 1.023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine delle **ore lavorative**e i giorni della settimana da considerare come giorni lavorativi. Gli orari al di fuori delle ore lavorative definite vengono considerati ore non lavorative.
4. Fare clic su **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Per eseguire il backup di file e cartelle per la prima volta
1. Nell'agente di Backup fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault/backup-now.png)
2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.
3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

![Completamento infrarossi](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul backup di macchine virtuali o altri carichi di lavoro, vedere:

* Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).

