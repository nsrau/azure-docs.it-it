---
title: Eseguire il backup delle macchine Windows con l'agente Azure Backup MARS
description: Usare l'agente di servizi di ripristino Microsoft (MARS) di Azure Backup per eseguire il backup delle macchine Windows.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810371"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Eseguire il backup delle macchine Windows con l'agente Azure Backup MARS

Questo articolo illustra come eseguire il backup di macchine Windows utilizzando il [Backup di Azure](backup-overview.md) servizio e l'agente di Microsoft Azure Recovery Services (MARS), noto anche come Azure Backup agent.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Verificare i prerequisiti e creare un insieme di credenziali di servizi di ripristino.
> * Scaricare e configurare l'agente di MARS
> * Creare un criterio di backup e pianificazione.
> * Eseguire il backup, eseguire un ad-hoc.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

L'agente MARS viene usato da Backup di Azure per eseguire il backup di file, cartelle e dello stato del sistema dalle macchine locali e macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino backup in Azure. È possibile eseguire l'agente come segue:

- Eseguire l'agente direttamente in macchine virtuali Windows locali in modo che è possibile eseguire il backup direttamente a un insieme di credenziali di servizi di ripristino backup in Azure.
- Eseguire macchine virtuali di Azure di agente che esegue Windows (side-by-side con l'estensione di backup di macchine Virtuali di Azure) per eseguire il backup di file e cartelle specifici nella macchina virtuale.
- Eseguire l'agente in un server di Backup di Microsoft Azure o in un server di System Center Data Protection Manager (DPM). In questo scenario, le macchine e carichi di lavoro di eseguire il backup in DPM/MABS e quindi DPM/MABS esegue il backup in un insieme di credenziali in Azure usando l'agente MARS.
Gli elementi di cui è possibile eseguire il backup dipendono dalla posizione in cui è installato l'agente.

> [!NOTE]
> Il metodo principale per backup di macchine virtuali di Azure consiste nell'usare un'estensione di Backup di Azure nella macchina virtuale. In questo modo viene eseguito il backup dell'intera macchina virtuale. È possibile installare e usare l'agente MARS insieme all'estensione, se si desidera eseguire il backup di file e cartelle specifici nella macchina virtuale. [Altre informazioni](backup-architecture.md#architecture-direct-backup-of-azure-vms)

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni su come](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Backup di Azure esegue il backup delle macchine Windows con l'agente MARS.
- [Informazioni su](backup-architecture.md#architecture-back-up-to-dpmmabs) l'architettura del backup in esecuzione l'agente di MARS in un server secondario di backup di Microsoft AZURE o Data Protection Manager.
- [Revisione](backup-support-matrix-mars-agent.md) sugli elementi supportati e quali possono essere sottoposte a backup con l'agente MARS.
- Verificare l'accesso a internet nei computer di cui che si desidera eseguire il backup.
- Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/free/) in pochi minuti.

### <a name="verify-internet-access"></a>Verificare l'accesso a Internet

Se il computer ha limitato l'accesso a internet, assicurarsi che le impostazioni del firewall nel computer o nel proxy di consentiranno tali URL e l'indirizzo IP:

**URL**

- www\.msftncsi.com
- *.Microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

**Indirizzo IP**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino vengono archiviati tutti i backup e i punti di ripristino creati nel tempo e contiene il criterio di backup applicato alle macchine sottoposti a backup. Creare un insieme di credenziali nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando la sottoscrizione di Azure.
2. Nella ricerca, digitare **servizi di ripristino** e fare clic su **insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Nel **insiemi di credenziali dei servizi di ripristino** menu, fare clic su **+ Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali.

   - Il nome deve essere univoco per la sottoscrizione di Azure.
   - Può contenere da 2 a 50 caratteri.
   - Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Selezionare la sottoscrizione di Azure, gruppo di risorse e area geografica in cui deve essere creato l'insieme di credenziali. I dati di backup viene inviati all'insieme di credenziali. Fare quindi clic su **Crea**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - La creazione dell'insieme di credenziali può richiedere alcuni minuti.
   - Monitorare le notifiche di stato nell'area superiore destra del portale. Se dopo alcuni minuti non viene visualizzato l'insieme di credenziali, fare clic su **Aggiorna**.

     ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Impostare la ridondanza di archiviazione

Backup di Azure gestisce automaticamente l'archiviazione per l'insieme di credenziali. È necessario specificare una modalità di replica archiviazione.

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali. Sotto il **le impostazioni** fare clic su **proprietà**.
2. Nelle **delle proprietà**, in **configurazione Backup**, fare clic su **Update**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **salvare**.

      ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- È consigliabile che se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare il valore predefinito **geograficamente ridondante** impostazione.
- Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure.
- Altre informazioni sulle [geografica](../storage/common/storage-redundancy-grs.md) e [locale](../storage/common/storage-redundancy-lrs.md) ridondanza.

## <a name="download-the-mars-agent"></a>Scaricare l'agente di MARS

Scaricare l'agente di MARS per l'installazione nei computer che si desidera eseguire il backup.

- Se già stato installato l'agente in eventuali computer, assicurarsi che si esegue la versione più recente.
- La versione più recente è disponibile nel portale o tramite un [download diretto](https://aka.ms/azurebackup_agent)

1. Nell'insieme di credenziali, sotto **Guida introduttiva**, fare clic su **Backup**.

    ![Aprire il Pannello Obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. Nelle **in cui viene eseguito il carico di lavoro?**, selezionare **On-premises**. È consigliabile selezionare questa opzione anche se si vuole installare l'agente di MARS in una VM di Azure.
3. Nelle **cosa si desidera eseguire il backup?**, selezionare **i file e cartelle** e/o **dello stato del sistema**. Sono disponibili numerose altre opzioni, ma questi sono supportati solo se si esegue un server di backup secondario. Fare clic su **preparare l'infrastruttura**.

      ![Configurazione di file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Nel **preparare l'infrastruttura**, in **agente installare servizi di ripristino**, scaricare l'agente MARS.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. Fare clic su **Salva** nel menu a comparsa del download. Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads.

6. A questo punto, controllare **Already download o tramite l'agente di servizi di ripristino più recente**e quindi scaricare le credenziali dell'insieme di credenziali.

    ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Fare clic su **Save**. Il file viene scaricato alla cartella di Download. È Impossibile aprire il file delle credenziali dell'insieme di credenziali.

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Eseguire la **MARSagentinstaller.exe** file nei computer che si desidera eseguire il backup.
2. Nell'installazione guidata agente MARS > **impostazioni di installazione**, specificare dove si desidera installare l'agente e un percorso da usare per la cache. Quindi fare clic su **Next**.
   - Backup di Azure Usa la cache per archiviare gli snapshot di dati prima di inviarli ad Azure.
   - Il percorso della cache deve avere uno spazio disponibile pari almeno al 5% delle dimensioni dei dati che è possibile eseguire il backup.

     ![Impostazioni di installazione guidata di MARS](./media/backup-configure-vault/mars1.png)

2. Nelle **configurazione del Proxy**, specificare in modo in cui l'agente in esecuzione nel computer Windows si connetterà a internet. Quindi fare clic su **Next**.

   - Se si usa un oggetto personalizzato proxy specificare le impostazioni di proxy e le credenziali se necessario.
   - Tenere presente che l'agente deve accedervi [questi URL](#verify-internet-access).

     ![Accesso a internet guidata MARS](./media/backup-configure-vault/mars2.png)

3. Nelle **installazione** esaminare il controllo dei prerequisiti e fare clic su **installare**.
4. Dopo aver installato l'agente, fare clic su **continua con la registrazione**.
5. Nel **registrazione guidata Server** > **identificazione insieme di credenziali**, Sfoglia e selezionare il file di credenziali scaricato. Quindi fare clic su **Next**.

    ![Register - insieme di credenziali](./media/backup-configure-vault/register1.png)

6. Nelle **impostazione di crittografia**, specificare una passphrase che verrà utilizzata per crittografare e decrittografare i backup per la macchina.

    - Salvare la passphrase di crittografia in un luogo sicuro.
    - Se si perde o dimentica la passphrase, Microsoft non consente di ripristinare i dati di backup. Salvare il file in una posizione sicura. È necessario per ripristinare un backup.

7. Fare clic su **fine**. L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Il criterio di backup specifica quando eseguire snapshot dei dati per creare i punti di ripristino e il tempo di mantenere i punti di ripristino.

- Si configura un criterio di backup utilizzando l'agente MARS.
- Backup di Azure non diventano automaticamente l'ora legale (DST) nell'account. Ciò potrebbe comportare alcune discrepanze tra il tempo effettivo e l'ora del backup pianificato.

Creare un criterio come indicato di seguito:

1. In ogni computer, aprire l'agente MARS. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.
2. Nelle **azioni**, fare clic su **pianifica Backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. In Pianificazione guidata Backup > **Guida introduttiva**, fare clic su **successivo**.
4. Nelle **Seleziona elementi per Backup**, fare clic su **Aggiunta elementi**.
5. Nelle **Seleziona elementi**, selezionare quello che vuoi eseguire il backup. Fare quindi clic su **OK**.
6. Nelle **Seleziona elementi per Backup** pagina, fare clic su **successivo**.
7. Nelle **specificare la pianificazione del Backup** specificare quando si desidera eseguire i backup giornalieri o settimanali. Quindi fare clic su **Next**.

    - Quando viene eseguito un backup, viene creato un punto di ripristino.
    - Il numero di punti di ripristino creati nell'ambiente in uso dipende dalla pianificazione del backup.

1. È possibile pianificare i backup giornalieri, fino a tre volte al giorno. Ad esempio, lo screenshot Mostra due backup giornalieri, una a mezzanotte e alle ore 18.00.

    ![Pianificazione giornaliera](./media/backup-configure-vault/day-schedule.png)

9. È possibile eseguire i backup settimanali troppo. Lo screenshot Mostra ad esempio, i backup eseguiti ogni domenica e mercoledì alternativo alle 9:30 e all'1: 00.

    ![Pianificazione settimanale](./media/backup-configure-vault/week-schedule.png)

8. Nel **selezionare i criteri di conservazione** , specificare come si archiviano copie della cronologia dei dati. Quindi fare clic su **Next**.

   - Le impostazioni di conservazione specificano quali punti di ripristino devono essere archiviati e quanto tempo devono essere archiviati per.
   - Ad esempio, quando si imposta un'impostazione di conservazione giornalieri, si indicano che all'ora specificata per il periodo di conservazione giornaliero, l'ultimo punto di ripristino verrà conservato per il numero di giorni specificato. In alternativa, un altro esempio, è possibile specificare un criterio di conservazione mensile per indicare che il punto di ripristino creato il 30 del mese deve essere archiviato per 12 mesi.
   - Conservazione del punto di ripristino giornalieri e settimanali in genere coincide con la pianificazione del backup. Vale a dire che, quando viene attivato il backup in base alla pianificazione, il punto di ripristino creato per il backup viene archiviato per la durata indicata nei criteri di conservazione giornalieri o settimanali.
   - Ad esempio, nella schermata seguente:
     - Backup giornaliero a mezzanotte e alle 18.00 vengono conservati per sette giorni.
     - I backup eseguiti su un sabato a mezzanotte e le 18 vengono conservati per quattro settimane.
     - I backup eseguiti sabato nell'ultima settimana del mese a mezzanotte e le 18 vengono conservati per 12 mesi. -I backup di sabato nell'ultima settimana del mese di marzo vengono conservati per 10 anni.

   ![Esempio di memorizzazione](./media/backup-configure-vault/retention-example.png)

11. Nelle **tipo di Backup iniziale** specificano come eseguire backup, iniziale sulla rete o offline. Quindi fare clic su **Next**.

10. Nelle **conferma**, esaminare le informazioni e quindi fare clic su **fine**.
11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="perform-the-initial-backup-offline"></a>Eseguire il backup iniziale offline

È possibile eseguire automaticamente un'iniziale eseguire il backup in rete o non in linea. Il seeding offline per un backup iniziale è utile se sono presenti grandi quantità di dati che richiedono una grande quantità di larghezza di banda di rete per il trasferimento. Eseguire un trasferimento offline come indicato di seguito:

1. Scrivere i dati di backup in un percorso di gestione temporanea.
2. Utilizzare lo strumento AzureOfflineBackupDiskPrep per copiare i dati dal percorso di staging a uno o più dischi SATA.
3. Lo strumento crea un processo di importazione di Azure. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) sull'esportazione e importazione di Azure.
4. Si inviano i dischi SATA a Data Center di Azure.
5. Presso il Data Center, i dati del disco viene copiati in un account di archiviazione di Azure.
6. Backup di Azure copia i dati dall'account di archiviazione all'insieme di credenziali e di backup incrementali.

[Altre informazioni](backup-azure-backup-import-export.md) sul seeding offline.

### <a name="enable-network-throttling"></a>Abilitare la limitazione della larghezza di banda

È possibile controllare l'utilizzo della larghezza di banda di rete dall'agente MARS, consentendo la limitazione delle richieste di rete. La limitazione delle richieste è utile se è necessario eseguire il backup dei dati durante le ore lavorative, ma la necessità di controllare quanta larghezza di banda viene utilizzata per il backup e ripristino di attività.

- Rete di Backup di Azure la limitazione delle richieste viene utilizzata [Quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) nel sistema operativo locale.
- La limitazione per il backup di rete è disponibile in Windows Server 2008 R2 e versioni successive e Windows 7 e versioni successive. Sistemi operativi devono essere in esecuzione il service pack più recenti.

Abilitare la limitazione come indicato di seguito:

1. Nell'agente di MARS, fare clic su **modificare le proprietà**.
2. Nel **limitazione** scheda, verificare **abilita la limitazione delle richieste per operazioni di backup all'utilizzo della larghezza di banda di internet**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault/throttling-dialog.png)
3. Specificare la larghezza di banda consentita durante il processo e fuori dall'orario di lavoro. I valori di larghezza di banda partono da 512 Kbps e arrivare fino a 1023 MBps. Fare quindi clic su **OK**.

## <a name="run-an-ad-hoc-backup"></a>Eseguire un backup ad hoc

1. Nell'agente di MARS, fare clic su **subito il backup**. Questo comando avvia la replica iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault/backup-now.png)

2. Nelle **conferma**, rivedere le impostazioni e fare clic su **eseguire il backup**.
3. Fare clic su **Chiudi** per chiudere la procedura guidata. In questo caso prima che venga completato il backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](backup-azure-restore-windows-server.md) ripristinare i file.
