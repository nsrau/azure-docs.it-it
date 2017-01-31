---
title: Imparare a eseguire il backup di file e cartelle da Windows ad Azure con Backup di Azure usando il modello di distribuzione Resource Manager | Microsoft Docs
description: Informazioni su come eseguire il backup di dati di Windows Server creando un insieme di credenziali, installando l&quot;agente di Servizi di ripristino ed eseguendo il backup di file e cartelle in Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: come eseguire il backup; come eseguire il back up
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/20/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: e0b8830861c18c6720bd64b6c15092d12bc1425e
ms.openlocfilehash: a8de3f159e5041c868172f45a31930f180e31a9a


---
# <a name="first-look-back-up-files-and-folders-with-azure-backup-using-the-resource-manager-deployment-model"></a>Primi passi: eseguire il backup di file e cartelle con Backup di Azure usando il modello di distribuzione Resource Manager
Questo articolo illustra come eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure con Backup di Azure usando Resource Manager. Si tratta di un'esercitazione che illustra le informazioni di base, l'ideale per iniziare a usare Backup di Azure.

Per altre informazioni su Backup di Azure, vedere questa [panoramica](backup-introduction-to-azure-backup.md).

Per eseguire il backup di file e cartelle in Azure, è necessario eseguire queste attività:

![Passaggio 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Ottenere una sottoscrizione di Azure (se non se ne possiede già una).<br>
![Passaggio 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Creare l'insieme di credenziali di Servizi di ripristino.<br>
![Passaggio 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Scaricare i file necessari.<br>
![Passaggio 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Installare e registrare l'agente di Servizi di ripristino.<br>
![Passaggio 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) Eseguire il backup di file e cartelle.

![Come eseguire il backup di computer Windows con Backup di Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## <a name="get-an-azure-subscription"></a>Ottenere una sottoscrizione di Azure
Se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio di Azure.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
Per eseguire il backup dei file e delle cartelle, è necessario creare un insieme di credenziali di Servizi di ripristino nell'area in cui archiviare i dati. È anche necessario determinare la modalità di replica di archiviazione.

### <a name="to-create-a-recovery-services-vault"></a>Per creare un insieme di credenziali di Servizi di ripristino
1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com/) , tramite la sottoscrizione di Azure.
2. Scegliere **Altri servizi** dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino**, quindi fare clic su **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se presenti nella sottoscrizione, gli insiemi di credenziali dei servizi di ripristino vengono elencati.
3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Verrà visualizzato il pannello degli insiemi di credenziali dei servizi di ripristino, in cui viene richiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali.
5. Nel menu **Sottoscrizione** scegliere la sottoscrizione da usare. Se si usa una sola sottoscrizione, questa verrà visualizzata e sarà possibile andare al passaggio successivo.
6. In **Gruppo di risorse**:
    - selezionare **Crea nuovo** e specificare il nome del nuovo gruppo di risorse.</br>
    oppure
    - selezionare **Usa esistente** e scegliere un gruppo di risorse nell'elenco dei gruppi di risorse della sottoscrizione.

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup.
8. Fare clic su **Crea**.

    Il portale visualizzerà messaggi durante la creazione e distribuzione dell'insieme di credenziali. Se al termine l'insieme di credenziali non viene elencato, fare clic su **Aggiorna**. ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh.png)</br>
    Dopo aver aggiornato l'elenco selezionare l'insieme di credenziali per aprire il relativo pannello.

### <a name="set-storage-redundancy-for-the-vault"></a>Impostare la ridondanza di archiviazione dell'insieme di credenziali
Quando si crea per la prima volta un insieme di credenziali di Servizi di ripristino, si determina come replicare lo spazio di archiviazione.

1. Nell'elenco di insiemi di credenziali dei servizi di ripristino fare clic sul nuovo insieme per aprire il relativo pannello.
2. Nel pannello del nuovo insieme di credenziali fare clic su **Infrastruttura di backup**.
    Verrà visualizzato il pannello Infrastruttura di backup.
3. Nel pannello Infrastruttura di backup fare clic su **Configurazione backup** per aprire il pannello **Configurazione backup**.

    ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Scegliere l'opzione di replica di archiviazione appropriata per l'insieme di credenziali.

    ![opzioni di configurazione dell'archiviazione](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint primario di archiviazione dei backup, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint non primario di archiviazione dei backup, scegliere l'archiviazione con ridondanza locale, che consente di ridurre i costi di archiviazione dei dati in Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) e [con ridondanza locale](../storage/storage-redundancy.md#locally-redundant-storage), leggere questa [panoramica](../storage/storage-redundancy.md).

Dopo aver creato un insieme di credenziali, configurarlo per il backup di file e cartelle.

## <a name="configure-the-vault"></a>Configurare l'insieme di credenziali
1. Nel pannello dell'insieme di credenziali dei servizi di ripristino appena creato, nella sezione Attività iniziali fare clic su **Backup** e nel pannello **Introduzione al backup** selezionare **Obiettivo del backup**.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Verrà visualizzato il pannello **Obiettivo del backup**.

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

    Non è ancora necessario installare l'agente. Questa operazione verrà eseguita in un passaggio successivo, dopo aver scaricato le credenziali dell'insieme di credenziali.

6. Fare clic su **Scarica** nel pannello **Preparare l'infrastruttura**.

    ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Le credenziali dell'insieme di credenziali verranno scaricate nella cartella Download locale. Al termine del download delle credenziali dell'insieme di credenziali verrà visualizzato un messaggio popup che chiede se aprire o salvare le credenziali. Fare clic su **Save**. Se si fa clic accidentalmente su **Apri**, attendere che il tentativo di apertura delle credenziali termini con un errore. Non è possibile aprire le credenziali dell'insieme di credenziali. Procedere al passaggio successivo. Le credenziali dell'insieme di credenziali si trovano nella cartella Download.   

    ![Il download delle credenziali dell'insieme di credenziali è terminato](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

> [!NOTE]
> L'abilitazione del backup tramite il portale di Azure sarà presto disponibile. Usare al momento l'agente di Servizi di ripristino di Microsoft Azure in locale per eseguire il backup dei file e delle cartelle.
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

## <a name="back-up-your-files-and-folders"></a>Eseguire il backup di file e cartelle
Il backup iniziale comprende due attività fondamentali:

* Pianificare il backup
* Eseguire il backup di file e cartelle per la prima volta

Per completare il backup iniziale, usare l'agente di Servizi di ripristino di Microsoft Azure.

### <a name="to-schedule-the-backup-job"></a>Per pianificare il processo di backup
1. Aprire l'agente di Servizi di ripristino di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Avviare l'agente di Servizi di ripristino di Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. Nell'agente di Servizi di ripristino fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.
4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.
5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.
6. Fare clic su **Avanti**.
7. Nella pagina **Specificare la pianificazione del backup** specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

   > [!NOTE]
   > Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).
   >

8. Nella pagina **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.
9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.
10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.
11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Per eseguire il backup di file e cartelle per la prima volta
1. Nell'agente di Servizi di ripristino fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.
3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

![Completamento infrarossi](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
* Sono disponibili altre informazioni sul [backup di computer Windows](backup-configure-vault.md).
* Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).



<!--HONumber=Jan17_HO4-->


