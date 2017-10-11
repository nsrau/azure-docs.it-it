---
title: Eseguire il backup dello stato del sistema Windows in Azure | Microsoft Docs
description: Informazioni su come eseguire il backup dello stato del sistema di computer Windows Server e/o Windows in Azure.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: come eseguire il backup; procedura di backup; backup di file e cartelle
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Eseguire il backup dello stato del sistema Windows in una distribuzione Resource Manager
Questo articolo illustra come eseguire il backup dello stato del sistema Windows Server in Azure. Si tratta di un'esercitazione che illustra le informazioni di base,

Per altre informazioni su Backup di Azure, vedere questa [panoramica](backup-introduction-to-azure-backup.md).

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

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Nella sezione **Sottoscrizione** usare il menu a discesa per scegliere la sottoscrizione di Azure. Se si usa una sola sottoscrizione, questa verrà visualizzata e sarà possibile andare al passaggio successivo. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono disponibili più scelte solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Nella sezione **Gruppo di risorse**:

    * Selezionare **Crea nuovo** se si vuole creare un gruppo di risorse.
    Or
    * Selezionare **Usa esistente** e fare clic sul menu a discesa per visualizzare l'elenco di gruppi di risorse disponibili.

  Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup.

8. Nella parte inferiore del pannello Insieme di credenziali dei servizi di ripristino fare clic su **Crea**.

    La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. L'insieme di credenziali, dopo essere stato creato, viene visualizzato negli insiemi di credenziali di Servizi di ripristino. Se l'insieme di credenziali non viene visualizzato dopo qualche minuto, fare clic su **Aggiorna**.

    ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Dopo la visualizzazione dell'insieme di credenziali nell'elenco corrispondente per i Servizi di ripristino, è possibile configurare la ridondanza di archiviazione.

### <a name="set-storage-redundancy-for-the-vault"></a>Impostare la ridondanza di archiviazione dell'insieme di credenziali
Quando si crea un insieme di credenziali dei Servizi di ripristino, assicurarsi che la ridondanza di archiviazione sia configurata in base alle proprie esigenze.

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

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'opzione **Con ridondanza geografica**. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) e [con ridondanza locale](../storage/common/storage-redundancy.md#locally-redundant-storage), vedere [Panoramica della ridondanza di archiviazione](../storage/common/storage-redundancy.md).

Dopo aver creato un insieme di credenziali, configurarlo per il backup dello stato del sistema Windows.

## <a name="configure-the-vault"></a>Configurare l'insieme di credenziali
1. Nel pannello dell'insieme di credenziali dei servizi di ripristino appena creato, nella sezione Attività iniziali fare clic su **Backup** e nel pannello **Introduzione al backup** selezionare **Obiettivo del backup**.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Verrà visualizzato il pannello **Obiettivo del backup**.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Scegliere **Locale** dal menu a discesa **Posizione di esecuzione del carico di lavoro**.

    Si sceglie **Locale** perché il server di Windows o il computer Windows è un computer fisico che non si trova in Azure.

3. Scegliere **Stato del sistema** dal menu **Elementi di cui eseguire il backup** e fare clic su **OK**.

    ![Configurazione di file e cartelle](./media/backup-azure-system-state/backup-goal-system-state.png)

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
> L'abilitazione del backup tramite il portale di Azure non è ancora disponibile. Usare l'agente di Servizi di ripristino di Microsoft Azure per eseguire il backup dello stato del sistema Windows Server.
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

## <a name="back-up-windows-server-system-state-preview"></a>Eseguire il backup dello stato del sistema Windows Server (anteprima)
Il backup iniziale include tre attività:

* Abilitare il backup dello stato del sistema con l'agente di Backup di Azure
* Pianificare il backup
* Eseguire il backup di file e cartelle per la prima volta

Per completare il backup iniziale, usare l'agente di Servizi di ripristino di Microsoft Azure.

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Per abilitare il backup dello stato del sistema con l'agente di Backup di Azure

1. In una sessione di PowerShell eseguire questo comando per arrestare il motore di backup di Azure.

  ```
  PS C:\> Net stop obengine
  ```

2. Aprire il Registro di sistema di Windows.

  ```
  PS C:\> regedit.exe
  ```

3. Aggiungere la chiave del Registro di sistema seguente con il valore DWORD specificato.

  | Percorso del Registro | Chiave del Registro di sistema | Valore DWORD |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. Riavviare il motore di backup eseguendo questo comando in un prompt dei comandi con privilegi elevati.

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>Per pianificare il processo di backup

1. Aprire l'agente di Servizi di ripristino di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Avviare l'agente di Servizi di ripristino di Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Nell'agente di Servizi di ripristino fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.

4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.

5. Selezionare **Stato del sistema** e quindi fare clic su **OK**.

6. Fare clic su **Avanti**.

7. La pianificazione del backup e della conservazione dello stato del sistema viene impostata automaticamente su un backup ogni domenica alle 21:00 ora locale e su un periodo di conservazione di 60 giorni.

   > [!NOTE]
   > I criteri di backup e di conservazione dello stato del sistema vengono configurati automaticamente. Se si vuole eseguire il backup di file e cartelle oltre che dello stato del sistema Windows Server, nella procedura guidata specificare solo i criteri di backup e di conservazione per i backup dei file. 
   >

8. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.

9. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Eseguire il backup dello stato del sistema Windows Server per la prima volta

1. Verificare che non siano presenti aggiornamenti in sospeso di Windows Server che richiedono un riavvio.

2. Nell'agente di Servizi di ripristino fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.

4. Fare clic su **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

5. Se si esegue il backup di file e cartelle oltre che dello stato del sistema Windows Server, la procedura guidata Esegui backup ora eseguirà il backup solo dei file. Per eseguire un backup dello stato del sistema ad hoc, usare questo comando di PowerShell:

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

  ![Completamento infrarossi](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti offrono informazioni supplementari.

### <a name="what-is-the-staging-volume"></a>Che cos'è il volume di staging?

Il volume di staging rappresenta la posizione intermedia in cui Windows Server Backup, disponibile in modo nativo, inserisce temporaneamente il backup dello stato del sistema. L'agente di Backup di Azure quindi comprime e crittografa questo backup intermedio e lo invia tramite il protocollo HTTPS sicuro all'insieme di credenziali dei servizi di ripristino. **È consigliabile definire il volume di staging in una posizione che non sia un volume del sistema operativo Windows. Se si riscontrano problemi con i backup dello stato del sistema, il primo passaggio per risolverli è controllare la posizione del volume di staging.** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>Come si può modificare il percorso del volume di staging specificato nell'agente di Backup di Azure?

Per impostazione predefinita, il volume di staging si trova nella cartella della cache. 

1. Per modificare tale posizione, usare il comando seguente in un prompt dei comandi con privilegi elevati:
  ```
  PS C:\> Net stop obengine
  ```

2. Aggiornare quindi la voce del Registro di sistema seguente con il percorso della nuova cartella del volume di staging.

  |Percorso del Registro|Chiave del Registro di sistema|Valore|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | Nuovo percorso del volume di staging |

Per il percorso di staging viene fatta distinzione tra maiuscole e minuscole e l'uso di maiuscole e minuscole deve corrispondere esattamente a quello esistente nel server. 

3. Dopo aver modificato il percorso del volume di staging, riavviare il motore di backup:
  ```
  PS C:\> Net start obengine
  ```
4. Per acquisire il percorso modificato, aprire l'agente di Servizi di ripristino di Microsoft Azure e attivare un backup ad hoc dello stato del sistema.

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>Perché l'impostazione predefinita della conservazione dello stato del sistema è 60 giorni?

La vita utile di un backup dello stato del sistema è uguale all'impostazione della "durata di rimozione definitiva" del ruolo Active Directory di Windows Server. Il valore predefinito della voce relativa alla durata di rimozione definitiva è 60 giorni. Questo valore può essere impostato nell'oggetto di configurazione del servizio directory (NTDS).

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>Come si modificano i criteri di backup e di conservazione predefiniti per lo stato del sistema?

Per modificare i criteri di backup e di conservazione predefiniti per lo stato del sistema:
1. Arrestare il motore di backup. Eseguire questo comando da un prompt dei comandi con privilegi elevati.

  ```
  PS C:\> Net stop obengine
  ```

2. Aggiungere o aggiornare le voci di chiave del Registro di sistema seguenti in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.

  |Nome nel Registro di sistema|Descrizione|Valore|
  |-------------|-----------|-----|
  |SSBScheduleTime|Consente di configurare l'ora di backup. L'impostazione predefinita sono le 21 ora locale.|DWORD: formato HHMM (numero decimale), ad esempio 2130 per le 21:30 ora locale|
  |SSBScheduleDays|Consente di configurare i giorni in cui il backup dello stato del sistema deve essere eseguito all'ora specificata. I giorni della settimana sono specificati da singole cifre. 0 rappresenta domenica, 1 lunedì e così via. Il giorno predefinito per il backup è domenica.|DWORD: giorni della settimana in cui eseguire il backup (numero decimale). Ad esempio, 1230 pianifica i backup di lunedì, martedì, mercoledì e domenica.|
  |SSBRetentionDays|Consente di configurare i giorni di conservazione del backup. Il valore predefinito è 60. Il massimo consentito è 180.|DWORD: giorni di conservazione del backup (numero decimale).|

3. Usare il comando seguente per riavviare il motore di backup.
    ```
    PS C:\> Net start obengine
    ```

4. Aprire l'agente di Servizi di ripristino di Microsoft Azure.

5. Fare clic su **Pianifica backup** e quindi su **Avanti** finché non vengono visualizzate le modifiche.

6. Fare clic su **Fine** per applicare le modifiche.


## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
* Sono disponibili altre informazioni sul [backup di computer Windows](backup-configure-vault.md).
* Ora che si è eseguito il backup dei file e delle cartelle, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
