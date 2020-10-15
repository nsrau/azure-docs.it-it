---
title: Eseguire il backup di macchine virtuali della soluzione VMware di Azure con server di Backup di Azure
description: Configurare l'ambiente della soluzione VMware di Azure per eseguire il backup di macchine virtuali usando server di Backup di Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: b8b5236a8da165efbb8e479e25b58872c4a735ee
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893017"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Eseguire il backup di macchine virtuali della soluzione VMware di Azure con server di Backup di Azure

In questo articolo vengono illustrate le procedure per eseguire il backup di macchine virtuali VMware in esecuzione in una soluzione VMware di Azure usando server di Backup di Azure. Prima di iniziare, assicurarsi di eseguire accuratamente [la configurazione di backup di Microsoft Azure server per la soluzione VMware di Azure](set-up-backup-server-for-azure-vmware-solution.md).

Quindi, verranno esaminate tutte le procedure necessarie per:

> [!div class="checklist"] 
> * Configurare un canale sicuro in modo che il server di Backup di Azure possa comunicare con i server VMware tramite HTTPS. 
> * Aggiungere le credenziali dell'account a server di Backup di Azure. 
> * Aggiungere vCenter a server di Backup di Azure. 
> * Configurare un gruppo protezione contenente le macchine virtuali VMware di cui si vuole eseguire il backup, specificare le impostazioni di backup e pianificare il backup.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creare una connessione sicura al server vCenter

Per impostazione predefinita, il server di Backup di Azure comunica con i server VMware tramite HTTPS. Per configurare la connessione HTTPS, scaricare il certificato dell'autorità di certificazione VMware (CA) e importarlo nel server di Backup di Azure.

### <a name="set-up-the-certificate"></a>Configurare il certificato

1. Nel computer server di Backup di Azure nel browser immettere l'URL del client Web vSphere.

   > [!NOTE] 
   > Se la pagina VMware **Introduzione** non viene visualizzata, verificare le impostazioni del proxy di connessione e del browser e riprovare.

1. Nella pagina **Introduzione** VMware selezionare **Scarica certificati CA radice attendibili**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Client Web vSphere":::

1. Salvare il file di **download.zip** nel computer server di backup di Azure, quindi estrarne il contenuto nella cartella **certs** , che contiene:

   - File del certificato radice con un'estensione che inizia con una sequenza numerata come. 0 e. 1.
   - File CRL con estensione che inizia con una sequenza come. r0 o. R1.

1. Nella cartella **certs** , fare clic con il pulsante destro del mouse sul file del certificato radice e selezionare **Rinomina** per modificare l'estensione in **CRT**.

   L'icona del file si trasforma nell'icona che rappresenta i certificati radice.

1. Fare clic con il pulsante destro del mouse sul certificato radice e scegliere **Installa certificato**.

1. Nell' **importazione guidata certificati**selezionare **computer locale** come destinazione per il certificato e fare clic su **Avanti**.

   ![Pagina iniziale della procedura guidata](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Se richiesto, confermare che si desidera consentire le modifiche al computer.

1. Selezionare **colloca tutti i certificati nel seguente archivio**e selezionare **Sfoglia** per scegliere l'archivio certificati.

   ![Archiviazione dei certificati](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Selezionare **autorità di certificazione radice attendibili** come cartella di destinazione e quindi fare clic su **OK**.

1. Verificare le impostazioni e selezionare **fine** per iniziare a importare il certificato.

   ![Verificare che i certificati si trovino nella cartella corretta](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Una volta confermata l'importazione del certificato, accedere al server vCenter per verificare che la connessione sia protetta.

### <a name="enable-tls-12-on-azure-backup-server"></a>Abilitare TLS 1,2 in server di Backup di Azure

In VMware 6,7 in poi è stato abilitato TLS come protocollo di comunicazione. 

1. Copiare le impostazioni del registro di sistema seguenti e incollarle nel blocco note. Quindi salvare il file come TLS. REG senza l'estensione txt.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Fare clic con il pulsante destro del mouse su TLS. REG file e selezionare **merge** o **Open** per aggiungere le impostazioni al registro di sistema.


## <a name="add-the-account-on-azure-backup-server"></a>Aggiungere l'account nel server di Backup di Azure

1. Aprire server di backup di Azure e nella console di server di backup di Azure selezionare **gestione**  >  **server di produzione**  >  **Gestisci VMware**.

   ![Console del server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. Nella finestra di dialogo **Gestisci credenziali** selezionare **Aggiungi**.

   ![Nella finestra di dialogo Gestisci credenziali selezionare Aggiungi.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. Nella finestra di dialogo **Aggiungi credenziali** immettere il nome e la descrizione delle nuove credenziali. Specificare il nome utente e la password definiti nel server VMware.

   > [!NOTE] 
   > Se il server VMware e il server di Backup di Azure non si trovano nello stesso dominio, specificare il dominio nella casella **nome utente** .

   ![Finestra di dialogo Aggiungi credenziali del server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Selezionare **Aggiungi** per aggiungere le nuove credenziali.

   ![Screenshot mostra la finestra di dialogo server di Backup di Azure Gestisci credenziali con le nuove credenziali visualizzate.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Aggiungere il server vCenter a server di Backup di Azure

1. Nella console di server di backup di Azure selezionare **gestione**  >  **server di produzione**  >  **Aggiungi**.

   ![Aprire l'Aggiunta guidata server di produzione](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Selezionare **server VMware**e fare clic su **Avanti**.

   ![Aggiunta guidata server di produzione](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Specificare l'indirizzo IP del vCenter.

   ![Specificare il server VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Nella casella **porta SSL** immettere la porta usata per comunicare con vCenter.

   > [!TIP] 
   > La porta 443 è la porta predefinita, ma è possibile modificarla se vCenter è in ascolto su una porta diversa.

1. Nella casella **specificare le credenziali** selezionare le credenziali create nella sezione precedente.

1. Selezionare **Aggiungi** per aggiungere il vCenter all'elenco dei server e fare clic su **Avanti**.

   ![Aggiungere il server VMware e le credenziali](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Nella pagina **Riepilogo** selezionare **Aggiungi** per aggiungere il vCenter a server di backup di Azure.

   Il nuovo server viene aggiunto immediatamente. vCenter non necessita di un agente.

   ![Aggiungere il server VMware al server di Backup di Azure](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Nella pagina **fine** esaminare le impostazioni e quindi fare clic su **Chiudi**.

   ![Pagina Fine](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Il server vCenter verrà elencato in server di **produzione** con il tipo come **server VMware** e **lo stato dell'agente** come **OK**. Se lo **stato dell'agente** è **sconosciuto**, selezionare **Aggiorna**.

## <a name="configure-a-protection-group"></a>Configurazione di un gruppo protezione dati

I gruppi protezione dati raccolgono più macchine virtuali e applicano le stesse impostazioni di backup e conservazione dei dati a tutte le macchine virtuali nel gruppo.

1. Nella console di server di backup di Azure selezionare **protezione**  >  **nuovo**.

   ![Aprire la procedura guidata Crea nuovo gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Nella pagina iniziale della procedura guidata **Crea nuovo gruppo protezione** dati fare clic su **Avanti**.

   ![Finestra di dialogo della procedura guidata Crea nuovo gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**, quindi **Avanti**. Verrà visualizzata la pagina **Seleziona membri del gruppo** .

1. Nella pagina **Seleziona membri del gruppo** selezionare le macchine virtuali (o le cartelle VM) di cui si vuole eseguire il backup, quindi fare clic su **Avanti**.

   > [!NOTE]
   > Quando si seleziona una cartella o macchine virtuali, vengono selezionate anche le cartelle all'interno della cartella per il backup. È possibile deselezionare le cartelle o le macchine virtuali da escludere dal backup. Se è già in corso il backup di una macchina virtuale o di una cartella, non è possibile selezionarla, per garantire che non vengano creati punti di ripristino duplicati per una macchina virtuale.

   ![Seleziona membri del gruppo](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Nella pagina **Selezione metodo protezione dati** immettere un nome per il gruppo protezione dati e le impostazioni di protezione. 

1. Impostare la protezione dati a breve termine su **disco**, abilitare la protezione online, quindi fare clic su **Avanti**.

   ![Seleziona metodo protezione dati](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Specificare per quanto tempo si desidera che i dati vengano sottoposti a backup su disco.

   - Periodo di **mantenimento**dati: numero di giorni per cui vengono conservati i punti di ripristino del disco.
   - **Backup completo rapido**: la frequenza con cui vengono eseguiti i punti di ripristino del disco. Per modificare l'ora o le date in cui si verificano backup a breve termine, selezionare **modifica**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Client Web vSphere":::

1. Nella pagina **Verifica allocazione archiviazione su disco** verificare lo spazio su disco fornito per i backup delle macchine virtuali.

   - Le allocazioni di dischi consigliate sono basate sull'intervallo di conservazione specificato, il tipo di carico di lavoro e le dimensioni dei dati protetti. Apportare le modifiche necessarie e quindi fare clic su **Avanti**.
   - **Dimensione dati:** la dimensione dei dati nel gruppo protezione dati.
   - **Spazio su disco:** Quantità di spazio su disco consigliata per il gruppo protezione dati. Se si vuole modificare questa impostazione, allocare uno spazio totale leggermente superiore rispetto alla quantità stimata per la crescita di ogni origine dati.
   - **Dettagli pool di archiviazione:** Mostra lo stato del pool di archiviazione, che include le dimensioni totali e rimanenti del disco.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Client Web vSphere":::

   > [!NOTE]
   > In alcuni scenari, le dimensioni dei dati segnalate sono maggiori delle dimensioni effettive della macchina virtuale. Il problema è attualmente in fase di analisi.

1. Nella pagina **scelta del metodo** per la creazione della replica indicare come si desidera eseguire il backup iniziale e selezionare **Avanti**.

   - Il valore predefinito è **Automaticamente tramite rete** e **Ora**. Se si usa il valore predefinito, specificare un'ora non di punta. Se si sceglie in **seguito**, specificare un giorno e un'ora.
   - Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline tramite supporti di memorizzazione rimovibili.

   ![Scelta del metodo per la creazione della replica](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. Per le **Opzioni di verifica coerenza**selezionare come e quando automatizzare le verifiche della coerenza e selezionare **Avanti**.

   - È possibile eseguire verifiche della coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.
   - Se non si desidera configurare le verifiche di coerenza automatiche, è possibile eseguire una verifica manuale facendo clic con il pulsante destro del mouse sul gruppo protezione dati **Esegui verifica coerenza**.

1. Nella pagina **specificare i dati per la protezione dati online** selezionare le macchine virtuali o le cartelle VM di cui si vuole eseguire il backup, quindi fare clic su **Avanti**. 

   > [!TIP]
   > È possibile selezionare singolarmente i membri oppure scegliere **Seleziona tutto** per scegliere tutti i membri.

   ![Specifica i dati da proteggere online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Nella pagina **specificare la pianificazione del backup online** indicare la frequenza con cui si vuole eseguire il backup dei dati dalla risorsa di archiviazione locale in Azure e quindi fare clic su **Avanti**. 

   - Punti di ripristino cloud per i dati da generare in base alla pianificazione. 
   - Dopo la generazione del punto di ripristino, questo viene trasferito nell'insieme di credenziali di servizi di ripristino in Azure.

   ![Specificare la pianificazione dei backup online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Nella pagina **specificare i criteri di conservazione online** indicare per quanto tempo si vogliono mantenere i punti di ripristino creati dai backup giornalieri, settimanali, mensili o annuali in Azure e quindi fare clic su **Avanti**.

   - Non esiste un limite di tempo per la conservazione dei dati in Azure.
   - L'unico limite è che non è possibile avere più di 9.999 punti di ripristino per ogni istanza protetta. In questo esempio l'istanza protetta è il server VMware.

   ![Specificare i criteri di mantenimento online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Crea gruppo**.

   ![Riepilogo delle impostazioni e dei membri del gruppo protezione dati](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorare con la console di server di Backup di Azure

Dopo aver configurato il gruppo protezione dati per eseguire il backup delle macchine virtuali della soluzione VMware di Azure, è possibile monitorare lo stato del processo di backup e l'avviso utilizzando la console di server di Backup di Azure. Ecco cosa è possibile monitorare.

- Nella scheda **avvisi** del riquadro **monitoraggio** è possibile monitorare errori, avvisi e informazioni generali per un gruppo protezione dati, per un computer protetto specifico o per gravità del messaggio. È possibile visualizzare gli avvisi attivi e inattivi e configurare le notifiche di posta elettronica.
- Nella scheda **processi** del riquadro **monitoraggio** è possibile visualizzare i processi avviati da server di backup di Azure per un'origine dati protetta specifica o un gruppo protezione dati. È possibile seguire lo stato di avanzamento del processo o controllare le risorse utilizzate dai processi.
- Nell'area attività **protezione** dati è possibile controllare lo stato dei volumi e delle condivisioni nel gruppo protezione dati. È anche possibile controllare le impostazioni di configurazione, ad esempio le impostazioni di ripristino, l'allocazione dei dischi e la pianificazione del backup.
- Nell'area attività **gestione** è possibile visualizzare le schede **dischi, online**e **agenti** per verificare lo stato dei dischi nel pool di archiviazione, la registrazione in Azure e lo stato dell'agente DPM distribuito.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Client Web vSphere":::

## <a name="restore-vmware-virtual-machines"></a>Ripristinare macchine virtuali VMware

Nella server di Backup di Azure Console di amministrazione sono disponibili due modi per trovare i dati ripristinabili. È possibile cercare o esplorare. Quando si ripristinano i dati, è possibile che si desideri o meno ripristinare i dati o una macchina virtuale nello stesso percorso. Per questo motivo, server di Backup di Azure supporta tre opzioni di ripristino per i backup di macchine virtuali VMware:

- **Ripristino del percorso originale (OLR)**: usare OLR per ripristinare una macchina virtuale protetta nel percorso originale. È possibile ripristinare una macchina virtuale nel percorso originale solo se non è stato aggiunto o eliminato alcun disco dopo che è stato eseguito il backup. Se i dischi sono stati aggiunti o eliminati, è necessario utilizzare il ripristino del percorso alternativo.
- **Ripristino in un percorso alternativo (ALR)**: quando la macchina virtuale originale non è presente o non si vuole disturbare la VM originale, ripristinare la macchina virtuale in un percorso alternativo. Per ripristinare una macchina virtuale in un percorso alternativo, è necessario specificare il percorso di un host ESXi, il pool di risorse, la cartella, nonché l'archivio dati e il percorso di archiviazione. Per distinguere la macchina virtuale ripristinata dalla VM originale, server di Backup di Azure aggiunge "-recovered" al nome della macchina virtuale.
- **Ripristino del percorso dei singoli file (ILR)**: se la macchina virtuale protetta è una macchina virtuale Windows Server, è possibile ripristinare singoli file o cartelle all'interno della macchina virtuale usando la funzionalità ILR di server di backup di Azure. Per ripristinare singoli file, vedere la procedura più avanti in questo articolo. Il ripristino di un singolo file da una macchina virtuale è disponibile solo per i punti di ripristino di macchine virtuali e dischi Windows.

### <a name="restore-a-recovery-point"></a>Ripristinare un punto di ripristino

1. Nella Console di amministrazione server di Backup di Azure selezionare la visualizzazione **ripristino** . 

1. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro **punti di ripristino** vengono visualizzati i punti di ripristino disponibili.

   ![Punti di ripristino disponibili](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. Nel riquadro **punti di ripristino per** usare il calendario e i menu a discesa per selezionare una data in cui è stato effettuato un punto di ripristino. Per le date visualizzate in grassetto nel calendario sono disponibili punti di ripristino. In alternativa, è possibile fare clic con il pulsante destro del mouse sulla macchina virtuale e selezionare **Mostra tutti i punti di ripristino** , quindi selezionare il punto di ripristino dall'elenco.

   > [!NOTE] 
   > Per la protezione dati a breve termine, selezionare un punto di ripristino su disco per un ripristino più veloce. Dopo che i punti di ripristino a breve termine scadono, vengono visualizzati solo i punti di ripristino **online** da ripristinare.

1. Prima di eseguire il ripristino da un punto di ripristino online, verificare che il percorso di gestione temporanea includa spazio libero sufficiente per ospitare le dimensioni complete non compresse della macchina virtuale da ripristinare. È possibile visualizzare o modificare il percorso di gestione temporanea eseguendo la **Configurazione guidata delle impostazioni di sottoscrizione**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Client Web vSphere":::

1. Selezionare **Ripristina** per aprire il **Ripristino guidato**.

   ![Ripristino guidato, pagina Verifica selezione per ripristino](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Selezionare **Avanti** per passare alla schermata **Specifica opzioni di ripristino** . Fare di nuovo clic su **Avanti** per passare alla schermata **Selezione tipo di ripristino** . 

   > [!NOTE]
   > I carichi di lavoro VMware non supportano l'abilitazione della limitazione della larghezza di banda.

1. Nella pagina **Selezione tipo di ripristino** scegliere se eseguire il ripristino nell'istanza originale o in una nuova posizione, quindi selezionare **Avanti**.

   - Se si sceglie **Ripristina nell'istanza originale** non è necessario effettuare altre selezioni nella procedura guidata. Vengono usati i dati per l'istanza originale.
   - Se si sceglie **Ripristina come macchina virtuale in qualsiasi host**, nella schermata **Specifica destinazione** specificare le informazioni per **Host ESXi**, **Pool di risorse**, **Cartella** e **Percorso**.

   ![Selezione tipo di ripristino - pagina](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Nella pagina **Riepilogo** verificare le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. 

   Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

### <a name="restore-an-individual-file-from-a-vm"></a>Ripristinare un singolo file da una macchina virtuale

È possibile ripristinare singoli file da un punto di ripristino di una macchina virtuale protetta. Questa funzionalità è disponibile solo per le macchine virtuali Windows Server. Il ripristino di singoli file è simile al ripristino dell'intera macchina virtuale, con la differenza che si accede al VMDK e si trovano i file desiderati prima di avviare il processo di ripristino. 

> [!NOTE]
> Il ripristino di un singolo file da una macchina virtuale è disponibile solo per i punti di ripristino di macchine virtuali e dischi Windows.

1. Nella Console di amministrazione server di Backup di Azure selezionare la visualizzazione **ripristino** .

1. Usare il riquadro **Sfoglia** per cercare la macchina virtuale da ripristinare o applicare un filtro per trovarla. Dopo aver selezionato una macchina virtuale o una cartella, nel riquadro **punti di ripristino** vengono visualizzati i punti di ripristino disponibili.

   ![Punti di ripristino disponibili](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. Nel riquadro **punti di ripristino per** usare il calendario per selezionare la data che contiene i punti di ripristino desiderati. A seconda di come è stato configurato il criterio di backup, le date possono avere più di un punto di ripristino. 

1. Dopo aver selezionato il giorno in cui è stato creato il punto di ripristino, assicurarsi di scegliere l' **ora di ripristino**corretta. 

   > [!NOTE]
   > Se per la data selezionata esistono più punti di ripristino, scegliere il punto di ripristino selezionandolo nel menu a discesa **Ora ripristino**. 

   Dopo aver scelto il punto di ripristino, l'elenco degli elementi ripristinabili viene visualizzato nel riquadro **percorso** .

1. Per trovare i file che si desidera ripristinare, nel riquadro **percorso** fare doppio clic sull'elemento nella colonna **elemento ripristinabile** per aprirlo. Selezionare quindi il file o le cartelle che si desidera ripristinare. Per selezionare più elementi, selezionare il tasto **CTRL** mentre si seleziona ogni elemento. Utilizzare il riquadro **percorso** per eseguire una ricerca nell'elenco di file o cartelle visualizzati nella colonna **elemento ripristinabile** .
    
   > [!NOTE]
   > L' **elenco di ricerca seguente** non esegue la ricerca nelle sottocartelle. Per eseguire la ricerca nelle sottocartelle, fare doppio clic sulla cartella. Usare il pulsante **Su** per spostarsi da una cartella figlio alla cartella padre. È possibile selezionare più elementi (file e cartelle), ma devono essere nella stessa cartella padre. Non è possibile ripristinare gli elementi da più cartelle nello stesso processo di ripristino.

   ![Verifica selezione per ripristino](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Dopo aver selezionato gli elementi per il ripristino, nella barra multifunzione dello strumento Console di amministrazione selezionare **Ripristina** per aprire il **Ripristino guidato**. Nel **Ripristino guidato**la schermata **Verifica selezione per ripristino** Mostra gli elementi selezionati da ripristinare.

1. Nella schermata **Specifica opzioni di ripristino** eseguire una delle operazioni seguenti:

   - Selezionare **modifica** per abilitare la limitazione della larghezza di banda di rete. Nella finestra di dialogo **limitazione** , selezionare **Abilita limitazione all'utilizzo della larghezza di banda di rete** per attivarla. Dopo aver abilitato questa opzione configurare **Impostazioni** e **Pianificazione del lavoro**.
   - Selezionare **Avanti** per lasciare disabilitata la limitazione di rete.

1. Nella schermata **Selezione tipo di ripristino** selezionare **Avanti**. È possibile ripristinare solo i file o le cartelle in una cartella di rete.

1. Nella schermata **specifica destinazione** selezionare **Sfoglia** per trovare un percorso di rete per i file o le cartelle. Server di Backup di Azure crea una cartella in cui vengono copiati tutti gli elementi ripristinati. Il nome della cartella ha il prefisso MABS_day-month-year. Quando si seleziona un percorso per i file o la cartella ripristinati, vengono forniti i dettagli relativi a tale percorso, ad esempio **destinazione**, **percorso di destinazione**e **spazio disponibile**.

   ![Specificare il percorso per il ripristino dei file](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Nella schermata **Specifica opzioni di ripristino** scegliere quale impostazione di sicurezza applicare. È possibile scegliere di modificare la limitazione per l'utilizzo della larghezza di banda di rete, ma la limitazione è disabilitata per impostazione predefinita. Inoltre, il **ripristino San** e la **notifica** non sono abilitati.

1. Nella schermata **Riepilogo** , rivedere le impostazioni e selezionare **Ripristina** per avviare il processo di ripristino. Nella schermata **Stato ripristino** viene visualizzato l'avanzamento dell'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi durante la configurazione dei backup, vedere Risolvere i problemi del server di Backup di Azure.

> [!div class="nextstepaction"]
> [Guida alla risoluzione dei problemi per server di Backup di Azure](../backup/backup-azure-mabs-troubleshoot.md)
