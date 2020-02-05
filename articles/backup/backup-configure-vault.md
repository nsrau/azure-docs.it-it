---
title: Eseguire il backup di computer Windows con l'agente MARS
description: Usare l'agente di backup di Microsoft ripristino di Azure per eseguire il backup dei computer Windows.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025538"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Eseguire il backup di computer Windows con l'agente di Servizi di ripristino di Microsoft Azure di Backup di Azure

Questo articolo illustra come eseguire il backup di computer Windows con il servizio [backup di Azure](backup-overview.md) e l'agente di servizi di ripristino di Microsoft Azure (Mars), noto anche come agente di backup di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> * Verificare i prerequisiti e creare un insieme di credenziali di servizi di ripristino.
> * Scaricare e configurare l'agente di MARS
> * Creare una pianificazione e un criterio di backup.
> * Eseguire un backup su richiesta.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

L'agente MARS viene usato da backup di Azure per eseguire il backup di file, cartelle e stato del sistema da computer locali e VM di Azure a un insieme di credenziali di servizi di ripristino di backup in Azure. È possibile eseguire l'agente come segue:

* Eseguire l'agente direttamente nei computer Windows locali in modo che possano eseguire il backup direttamente in un insieme di credenziali di servizi di ripristino di backup in Azure.
* Eseguire l'agente in macchine virtuali di Azure che eseguono Windows (Side-by-side con l'estensione di backup della macchina virtuale di Azure) per eseguire il backup di file e cartelle specifici nella macchina virtuale.
* Eseguire l'agente in un server di Backup di Microsoft Azure o in un server di System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro eseguono il backup in MAB/DPM, quindi il backup di MAB/DPM viene eseguito in un insieme di credenziali in Azure usando l'agente MARS.
Gli elementi di cui è possibile eseguire il backup dipendono dalla posizione in cui è installato l'agente.

> [!NOTE]
> Il metodo principale per il backup di macchine virtuali di Azure consiste nell'usare un'estensione di backup di Azure nella macchina virtuale. In questo modo viene eseguito il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici nella macchina virtuale, è possibile installare e usare l'agente MARS insieme all'estensione. [Altre informazioni](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare

* [Scopri come](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Backup di Azure esegue il backup dei computer Windows con l'agente MARS.
* Informazioni [sull'architettura di](backup-architecture.md#architecture-back-up-to-dpmmabs) backup in cui è in esecuzione l'agente Mars in un server MAB o DPM secondario.
* [Esaminare](backup-support-matrix-mars-agent.md) gli elementi supportati e gli elementi di cui è possibile eseguire il backup con l'agente Mars.
* Verificare l'accesso a Internet nei computer di cui si vuole eseguire il backup.
* Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/free/) in pochi minuti.

### <a name="verify-internet-access"></a>Verificare l'accesso a Internet

Se il computer ha accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano questi URL e l'indirizzo IP:

#### <a name="urls"></a>URL

* www\.msftncsi.com
* *.Microsoft.com
* *.windowsazure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>Indirizzi IP

* 20.190.128.0/18
* 40.126.0.0/18

L'accesso a tutti gli URL e gli indirizzi IP elencati in precedenza usa il protocollo HTTPS sulla porta 443.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino Archivia tutti i backup e i punti di ripristino creati nel tempo e contiene i criteri di backup applicati ai computer sottoposti a backup. Creare un insieme di credenziali nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando la sottoscrizione di Azure.

2. Cercare e selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. Nel menu insiemi di credenziali **dei servizi di ripristino** fare clic su **+ Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali.

   * Il nome deve essere univoco per la sottoscrizione di Azure.
   * Può contenere da 2 a 50 caratteri.
   * Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Selezionare la sottoscrizione di Azure, il gruppo di risorse e l'area geografica in cui creare l'insieme di credenziali. I dati di backup vengono inviati all'insieme di credenziali. Fare quindi clic su **Crea**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * La creazione dell'insieme di credenziali può richiedere alcuni minuti.
   * Monitorare le notifiche di stato nell'area superiore destra del portale. Se dopo alcuni minuti l'insieme di credenziali non viene visualizzato, fare clic su **Aggiorna**.

     ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione

Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. È necessario specificare la modalità di replica dell'archiviazione.

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali. Nella sezione **Impostazioni** fare clic su **Proprietà**.
2. In **Proprietà**, in **configurazione backup**, fare clic su **Aggiorna**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Se si usa Azure come endpoint di archiviazione di backup primario, è consigliabile continuare a usare l'impostazione con **ridondanza geografica** predefinita.
* Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
* Altre informazioni sulla [ridondanza geografica](../storage/common/storage-redundancy-grs.md) e [locale](../storage/common/storage-redundancy-lrs.md) .

## <a name="download-the-mars-agent"></a>Scaricare l'agente MARS

Scaricare l'agente MARS per l'installazione nei computer di cui si vuole eseguire il backup.

* Se l'agente è già stato installato in qualsiasi computer, verificare che sia in esecuzione la versione più recente.
* La versione più recente è disponibile nel portale o tramite [download diretto](https://aka.ms/azurebackup_agent)

1. Nell'insieme di credenziali, in **Introduzione**, fare clic su **backup**.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. In **dove è in esecuzione il carico di lavoro?** selezionare **locale**. È necessario selezionare questa opzione anche se si vuole installare l'agente MARS in una macchina virtuale di Azure.
3. In **che cosa si desidera eseguire il backup?** selezionare **file e cartelle** e/o **stato del sistema**. Sono disponibili diverse altre opzioni, ma queste sono supportate solo se si esegue un server di backup secondario. Fare clic su **preparare l'infrastruttura**.

      ![Configurazione di file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Nell' **infrastruttura di preparazione**, in **Install Recovery Services Agent**, scaricare l'agente Mars.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. Fare clic su **Salva** nel menu a comparsa del download. Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads.

6. A questo punto, selezionare **già scaricare o usare l'agente di servizi di ripristino più recente**e quindi scaricare le credenziali dell'insieme di credenziali.

    ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Fare clic su **Salva**. Il file viene scaricato nella cartella di download. Non è possibile aprire il file dell'insieme di credenziali.

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Eseguire il file **MARSagentinstaller. exe** nei computer di cui si vuole eseguire il backup.
2. Nell'installazione guidata dell'agente MARS > **impostazioni di installazione**, specificare dove si desidera installare l'agente e un percorso da utilizzare per la cache. Quindi fare clic su **Next**.
   * Backup di Azure usa la cache per archiviare gli snapshot dei dati prima di inviarli ad Azure.
   * Il percorso della cache deve avere uno spazio disponibile pari almeno al 5% delle dimensioni dei dati di cui eseguire il backup.

    ![Impostazioni di installazione della procedura guidata MARS](./media/backup-configure-vault/mars1.png)

3. In **configurazione proxy**specificare il modo in cui l'agente in esecuzione nel computer Windows si connetterà a Internet. Quindi fare clic su **Next**.

   * Se si usa un proxy personalizzato, specificare le impostazioni proxy e le credenziali, se necessario.
   * Tenere presente che l'agente deve accedere a [questi URL](#verify-internet-access).

     ![Accesso a Internet della procedura guidata MARS](./media/backup-configure-vault/mars2.png)

4. In **installazione** esaminare il controllo dei prerequisiti e fare clic su **Installa**.
5. Dopo l'installazione dell'agente, fare clic su **procedi alla registrazione**.
6. In **registrazione guidata Server** > l' **Identificazione**dell'insieme di credenziali individuare e selezionare il file di credenziali scaricato. Quindi fare clic su **Next**.

    ![Credenziali di Register-Vault](./media/backup-configure-vault/register1.png)

7. In **impostazione crittografia**specificare una passphrase che verrà usata per crittografare e decrittografare i backup per il computer.

    * Salvare la passphrase di crittografia in un luogo sicuro.
    * Se la passphrase viene persa o dimenticata, Microsoft non può contribuire a recuperare i dati di backup. Salvare il file in una posizione sicura. È necessario ripristinare un backup.

8. Fare clic su **Fine**. L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Il criterio di backup specifica quando creare snapshot dei dati per creare punti di ripristino e per quanto tempo mantenere i punti di ripristino.

* È possibile configurare un criterio di backup usando l'agente MARS.
* Backup di Azure non prende automaticamente in considerazione l'ora legale (DST). Questo può causare una discrepanza tra l'ora effettiva e l'ora di backup pianificata.

Creare un criterio come segue:
1. Dopo aver scaricato e registrato l'agente MARS, avviare la console di Agent. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.  
2. In **azioni**fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. Nella pianificazione guidata backup > **Guida introduttiva**fare clic su **Avanti**.
4. In **Seleziona elementi di cui eseguire il backup**fare clic su **Aggiungi elementi**.

    ![Selezionare gli elementi di cui eseguire il backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. In **Seleziona elementi selezionare gli elementi**di cui si desidera eseguire il backup e fare clic su **OK**.

    ![Elementi selezionati di cui eseguire il backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Nella pagina **selezionare gli elementi da sottoporre a backup** fare clic su **Avanti**.
7. Nella pagina **impostazione pianificazione backup** specificare quando si desidera eseguire backup giornalieri o settimanali. Quindi fare clic su **Next**.

    - Quando viene creato un backup, viene creato un punto di ripristino.
    - Il numero di punti di ripristino creati nell'ambiente dipende dalla pianificazione del backup.


8. È possibile pianificare i backup giornalieri, fino a tre volte al giorno. Ad esempio, lo screenshot mostra due backup giornalieri, uno a mezzanotte e uno alle 6:00 PM.

    ![Pianificazione giornaliera](./media/backup-configure-vault/day-schedule.png)


9. È anche possibile eseguire backup settimanali. Ad esempio, lo screenshot mostra i backup eseguiti ogni alternativa domenica & mercoledì alle 9:30 AM e 1:00 AM.

    ![Pianificazione settimanale](./media/backup-configure-vault/week-schedule.png)


10. Nella pagina **Seleziona criteri di conservazione** specificare la modalità di archiviazione delle copie cronologiche dei dati. Quindi fare clic su **Next**.

    - Le impostazioni di conservazione specificano i punti di ripristino da archiviare e la durata di archiviazione.
    - Ad esempio, quando si imposta un'impostazione di conservazione giornaliera, si indica che nel momento specificato per la conservazione giornaliera, il punto di ripristino più recente verrà mantenuto per il numero di giorni specificato. Oppure, come altro esempio, è possibile specificare un criterio di conservazione mensile per indicare che il punto di ripristino creato il 30 di ogni mese deve essere archiviato per 12 mesi.
    - La conservazione dei punti di ripristino giornaliera e settimanale in genere coincide con la pianificazione del backup. Ciò significa che quando il backup viene attivato in base alla pianificazione, il punto di ripristino creato dal backup viene archiviato per la durata indicata nei criteri di conservazione giornalieri o settimanali.
    - Come esempio, nello screenshot seguente:

        -   I backup giornalieri a mezzanotte e 6:00 PM vengono conservati per sette giorni.
        -   I backup eseguiti a mezzanotte e 6:00 PM vengono conservati per quattro settimane.
        -   I backup eseguiti il sabato l'ultima settimana del mese a mezzanotte e 6:00 PM vengono conservati per 12 mesi.
        -   I backup eseguiti a sabato nell'ultima settimana di marzo sono conservati per 10 anni.

        ![Esempio di conservazione](./media/backup-configure-vault/retention-example.png)


11. In **Scegli il tipo di backup iniziale** decidere se si vuole eseguire il backup iniziale sulla rete o usare il backup offline. per altre informazioni, vedere questo [articolo](offline-backup-azure-data-box.md). Per eseguire il backup iniziale sulla rete, selezionare **automaticamente in rete** e fare clic su **Avanti**.

    ![Tipo di backup iniziale](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. In **conferma**esaminare le informazioni e quindi fare clic su **fine**.

    ![Conferma tipo di backup](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

    ![Conferma modifica processo di backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

È necessario creare un criterio in ogni computer in cui è installato l'agente.

### <a name="perform-the-initial-backup-offline"></a>Eseguire il backup iniziale offline

È possibile eseguire un backup iniziale automaticamente in rete o offline. Il seeding offline per un backup iniziale è utile se si dispone di grandi quantità di dati per cui è necessaria una quantità elevata di larghezza di banda di rete da trasferire. Si esegue un trasferimento offline come segue:

1. I dati di backup vengono scritti in un percorso di gestione temporanea.
2. Usare lo strumento AzureOfflineBackupDiskPrep per copiare i dati dal percorso di gestione temporanea a uno o più dischi SATA.
3. Lo strumento crea un processo di importazione di Azure. [Altre](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) informazioni su importazione ed esportazione di Azure.
4. I dischi SATA vengono inviati a un Data Center di Azure.
5. Nel Data Center i dati del disco vengono copiati in un account di archiviazione di Azure.
6. Backup di Azure copia i dati dall'account di archiviazione all'insieme di credenziali e vengono pianificati i backup incrementali.

[Altre](offline-backup-azure-data-box.md) informazioni sul seeding offline.

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda

È possibile controllare la modalità di utilizzo della larghezza di banda di rete da parte dell'agente MARS abilitando la limitazione delle richieste di rete. La limitazione è utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma si vuole controllare la quantità di larghezza di banda usata per l'attività di backup e ripristino.

* La limitazione della rete di backup di Azure usa la [qualità del servizio (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) nel sistema operativo locale.
* La limitazione di rete per il backup è disponibile in Windows Server 2012 e versioni successive e Windows 8. I sistemi operativi devono eseguire i Service Pack più recenti.

Abilitare la limitazione della rete nel modo seguente:

1. Nell'agente MARS fare clic su **modifica proprietà**.
2. Nella scheda **limitazione** selezionare **Abilita limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault/throttling-dialog.png)
3. Specificare la larghezza di banda consentita durante il lavoro e fuori dall'orario di lavoro. I valori della larghezza di banda iniziano a 512 kbps e passano fino a 1.023 MBps. Fare quindi clic su **OK**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

1. Nell'agente MARS fare clic su **Esegui backup ora**.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault/backup-now.png)

2. Se la versione dell'agente MARS è 2.0.9169.0 o successiva, è possibile impostare un periodo di memorizzazione personalizzato. Nella sezione **Mantieni backup fino** a scegliere una data dal calendario presentato:

   ![Mantieni calendario backup](./media/backup-configure-vault/mars-ondemand.png)

3. In **conferma**, rivedere le impostazioni e fare clic su **backup**.
4. Fare clic su **Chiudi** per chiudere la procedura guidata. Se si esegue questa operazione prima del completamento del backup, la procedura guidata continuerà a essere eseguita in background.
5. Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

## <a name="on-demand-backup-policy-retention-behavior"></a>Comportamento di conservazione dei criteri di backup su richiesta

>[!NOTE]
>Applicabile solo alle versioni dell'agente MARS precedenti a 2.0.9169.0
>

* Per ulteriori informazioni, fare riferimento al passaggio 8 di [creare un criterio di backup](backup-configure-vault.md#create-a-backup-policy) .

| Opzione pianificazione backup | Per quanto tempo verranno conservati i dati di backup?
| -- | --
| Pianifica un backup ogni: * giorno | **Conservazione predefinita**: equivalente alla "conservazione in giorni per i backup giornalieri" <br/><br/> **Eccezione**: se un set di backup giornaliero pianificato per la conservazione a lungo termine (settimane, mesi e anni) ha esito negativo, un backup su richiesta attivato subito dopo questo backup pianificato non riuscito viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine.<br/><br/> Esempio: se ( **ad esempio**) il backup pianificato eseguito il giovedì 8:00 AM ha esito negativo e lo stesso backup deve essere considerato per la conservazione settimanale/mensile/annuale, il primo backup su richiesta attivato prima del successivo backup pianificato (ad esempio, venerdì), 8:00 verrà automaticamente contrassegnato per la conservazione settimanale/mensile/annuale come applicabile al backup di giovedì 8:00.
| Pianifica un backup ogni: * settimanalmente | **Conservazione predefinita**: 1 giorno <br/> I backup su richiesta eseguiti per un'origine dati con criteri di backup settimanali vengono eliminati il giorno successivo, anche se sono i backup più recenti per l'origine dati. <br/><br/> **Eccezione**: se un set di backup pianificato settimanale per la conservazione a lungo termine (settimane, mesi e anni) ha esito negativo, un backup su richiesta attivato subito dopo questo backup pianificato non riuscito viene considerato per la conservazione a lungo termine. In caso contrario, il backup pianificato successivo viene considerato per la conservazione a lungo termine. <br/><br/> Esempio: se ( **ad esempio**) il backup pianificato eseguito il giovedì 8:00 AM ha esito negativo e lo stesso backup deve essere considerato per la conservazione mensile/annuale, il primo backup su richiesta attivato prima del successivo backup pianificato (ad esempio) giovedì, 8:00 verrà automaticamente contrassegnato per la conservazione mensile/annuale come applicabile al backup di giovedì 8:00

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come ripristinare i file](backup-azure-restore-windows-server.md).
