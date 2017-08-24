---
title: Eseguire il backup di server VMware con il server di Backup di Azure | Microsoft Docs
description: Usare il server di Backup di Azure per eseguire il backup di server VMware vCenter/ESXi in Azure o su disco. Questo articolo offre istruzioni dettagliate per il backup o la protezione dei carichi di lavoro VMware.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="back-up-a-vmware-server-to-azure"></a>Eseguire il backup di un server VMware in Azure

In questo articolo viene illustrato come configurare il server di Backup di Azure per proteggere i carichi di lavoro dei server VMware. In questo articolo si presuppone che sia stato già installato il server di Backup di Azure. Se il server di Backup di Azure non è installato, vedere [Preparazione del backup dei carichi di lavoro con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).

Il server di Backup di Azure può eseguire il backup o assicurare la protezione di server VMware vCenter versione 6.5, 6.0 e 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creazione di una connessione protetta al server vCenter

Per impostazione predefinita, il server di Backup di Azure comunica con ognuno dei server vCenter tramite un canale HTTPS. Per attivare la comunicazione protetta, è consigliabile installare il certificato dell'autorità di certificazione (CA, Certificate Authority) di VMware nel server di Backup di Azure. Se la comunicazione protetta non è necessaria e si preferisce disabilitare il requisito HTTPS, vedere [Disabilitazione del protocollo di comunicazione sicura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Per creare una connessione sicura tra il server di Backup di Azure e il server vCenter, importare il certificato trusted nel server di Backup di Azure.

Di solito, per connettersi al server vCenter tramite client Web vSphere si usa un browser nel computer server di Backup di Azure. La prima volta che si usa il browser del server di Backup di Azure per connettersi al server vCenter, la connessione non è protetta. L'immagine seguente mostra la connessione non protetta.

![Esempio di connessione non protetta al server VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Per risolvere il problema e creare una connessione protetta, scaricare i certificati CA radice attendibili.

1. Nel browser del server di Backup di Azure immettere l'URL del client Web vSphere. Viene visualizzata la pagina di accesso del client Web vSphere.

    ![Client Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Nella parte inferiore delle informazioni per amministratori e sviluppatori individuare il collegamento **Download trusted root CA certificates** (Scarica certificati CA radice trusted).

    ![Collegamento al download dei certificati CA radice trusted](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Qualora fosse impossibile trovare la pagina di accesso del client Web vSphere, controllare le impostazioni del proxy del browser.

2. Fare clic su **Scarica certificati CA radice attendibili**.

    Il server vCenter scarica un file nel computer locale. Il nome del file è **download**. A seconda del browser, viene visualizzato un messaggio che chiede se aprire o salvare il file.

    ![messaggio di download quando vengono scaricati i certificati](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Salvare il file in un percorso nel server di Backup di Azure. Quando si salva il file, aggiungere l'estensione zip al nome del file.

    Il file è un file con estensione .zip che contiene le informazioni sui certificati. L'estensione zip consente di usare gli strumenti di estrazione.

4. Fare clic con il pulsante destro del mouse su **download.zip** e quindi selezionare **Estrai tutto** per estrarre i contenuti.

    I contenuti del file con estensione zip vengono estratti in una cartella denominata **certs**. Nella cartella certs sono visualizzati due tipi di file. L'estensione del file del certificato radice inizia con una sequenza numerata, ad esempio .0 e .1.
    
    L'estensione del file CRL inizia con una sequenza simile a .r0 o .r1. Il file CRL è associato a un certificato.

    ![File di download estratto localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Nella cartella **certs** fare clic con il pulsante destro del mouse sul file del certificato radice e quindi fare clic su **Rinomina**.

    ![Ridenominazione del certificato radice ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Modificare l'estensione del certificato radice in .crt. Quando viene richiesta la conferma della modifica dell'estensione, fare clic su **Sì** o **OK**. In caso contrario, è possibile modificare manualmente la funzione del file prevista. L'icona del file si trasforma nell'icona che rappresenta i certificati radice.

6. Fare clic con il pulsante destro del mouse sul certificato radice e nel menu a comparsa, selezionare **Installa certificato**.

    Viene visualizzata la finestra di dialogo **Importazione guidata certificati**.

7. Nella finestra di dialogo **Importazione guidata certificati** selezionare **Computer locale** come destinazione del certificato e quindi fare clic su **Avanti** per continuare.

    ![Opzioni di destinazione di archiviazione del certificato ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Se viene visualizzata la richiesta di conferma delle modifiche al computer, fare clic su **Sì** o **OK** per tutte le modifiche.

8. Nella pagina **Archivio certificati** selezionare **Colloca tutti i certificati nel seguente archivio** e quindi fare clic su **Sfoglia**per scegliere l'archivio certificati.

    ![Inserire i certificati in un punto di archiviazione specifico](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    Verrà visualizzata la finestra di dialogo **Selezione archivio certificati**.

    ![Gerarchia di cartelle di archiviazione di certificati](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Selezionare **Autorità di certificazione radice attendibili** come cartella di destinazione per i certificati e quindi fare clic su **OK**.

    ![Cartella di destinazione dei certificati](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    La cartella **Autorità di certificazione radice attendibili** verrà confermata come archivio certificati. Fare clic su **Avanti**.

    ![Cartella archivio certificati](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Nella pagina **Completamento dell'Importazione guidata certificati** verificare che il certificato si trovi nella cartella desiderata e quindi fare clic su **Fine**.

    ![Verificare che i certificati si trovino nella cartella corretta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Viene visualizzata una finestra di dialogo con la conferma dell'esito positivo dell'importazione del certificato.

11. Accedere al server vCenter per verificare che la connessione sia sicura.

  Se l'importazione del certificato ha esito negativo e non è possibile stabilire una connessione sicura, consultare la documentazione di VMware vSphere relativa a [come ottenere certificati server](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Se sono previsti limiti di protezione all'interno dell'organizzazione e non si vuole attivare il protocollo HTTPS, eseguire la procedura seguente per disabilitare le comunicazioni sicure.

### <a name="disable-secure-communication-protocol"></a>Disabilitazione del protocollo di comunicazione sicura

Se l'organizzazione non richiede il protocollo HTTPS, eseguire la procedura seguente per disabilitare questo protocollo. Per disabilitare il comportamento predefinito, creare una chiave di registro che ignori il comportamento predefinito.

1. Copiare e incollare il seguente testo in un file .txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Salvare il file nel computer del server di Backup di Azure. Come nome del file usare DisableSecureAuthentication.reg.

3. Fare doppio clic sul file per attivare la voce del registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Creare un ruolo e un account utente sul server vCenter

Sul server vCenter, un ruolo è un set predefinito di privilegi. I ruoli vengono creati da un amministratore del server vCenter. Per assegnare le autorizzazioni, l'amministratore associa ogni account utente a un ruolo. Per definire le credenziali utente necessarie per eseguire il backup del computer del server vCenter, creare un ruolo con privilegi specifici e quindi associare l'account utente al ruolo.

Per l'autenticazione con il server vCenter, il server di Backup di Azure usa un nome utente e una password. Il server di Backup di Azure sfrutta tali credenziali come autenticazione per ogni operazione di backup.

Per aggiungere un ruolo del server vCenter e i relativi privilegi per un amministratore del backup:

1. Accedere al server vCenter e quindi nel pannello **Navigator** (Strumento di navigazione) del server vCenter fare clic su **Administration** (Amministrazione).

    ![Opzione Administration (Amministrazione) nel pannello Navigator (Strumento di navigazione) del server vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. In **Administration** (Amministrazione) selezionare **Roles** (Ruoli) e quindi nel pannello **Roles** (Ruoli) fare clic sull'icona Aggiungi ruolo (il simbolo +).

    ![Aggiungi ruolo](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    Verrà visualizzata la finestra di dialogo **Create Role** (Crea ruolo).

    ![Create role (Crea ruolo)](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Nella finestra di dialogo **Create Role** (Crea ruolo), nella casella **Role name** (Nome ruolo) immettere *BackupAdminRole*. È possibile scegliere un nome qualsiasi, ma deve essere riconoscibile per lo scopo del ruolo.

4. Selezionare i privilegi per la versione appropriata di vCenter e quindi fare clic su **OK**. La tabella seguente identifica i privilegi obbligatori per vCenter 6.0 e vCenter 5.5.

  Quando si selezionano i privilegi, fare clic sull'icona accanto all'etichetta padre per espandere l'elemento padre e visualizzare i privilegi figlio. Per selezionare i privilegi VirtualMachine, è necessario scendere di diversi livelli nella gerarchia padre-figlio. Non è necessario selezionare tutti i privilegi figlio all'interno di un privilegio padre.

  ![Gerarchia di privilegi padre-figlio](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Fare clic su **OK**. Il nuovo ruolo verrà visualizzato nell'elenco del pannello Roles (Ruoli).

|Privilegi per vCenter 6.0| Privilegi per vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Creare un account utente del server vCenter e le relative autorizzazioni

Dopo aver configurato il ruolo con privilegi, creare un account utente. L'account utente è dotato di un nome utente e di una password, che rappresentano le credenziali usate per l'autenticazione.

1. Per creare un account utente, nel pannello **Navigator** (Strumento di navigazione) del server vCenter fare clic su **Users and Groups** (Utenti e gruppi).

    ![Opzione Users and Groups (Utenti e gruppi)](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Verrà visualizzato il pannello **vCenter Users and Groups** (Utenti e gruppi di vCenter).

    ![Pannello vCenter Users and Groups (Utenti e gruppi di vCenter)](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Nel pannello **vCenter Users and Groups** (Utenti e gruppi di vCenter) selezionare la scheda **Users** (Utenti) e quindi fare clic sull'icona Aggiungi utenti (il simbolo +).

    Verrà visualizzata la finestra di dialogo **New User** (Nuovo utente).

3. Nella finestra di dialogo **New User** (Nuovo utente) aggiungere le informazioni dell'utente e quindi fare clic su **OK**. In questa procedura il nome utente è BackupAdmin.

    ![Finestra di dialogo New User (Nuovo utente)](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Il nuovo account utente viene visualizzato nell'elenco.

4. Per associare l'account utente al ruolo, nel pannello **Navigator** (Strumento di navigazione) fare clic su **Global Permissions** (Autorizzazioni globali). Nel pannello **Global Permissions** (Autorizzazioni globali) selezionare la scheda **Manage** (Gestisci) e quindi fare clic sull'icona Aggiungi (il simbolo +).

    ![Pannello Global Permissions (Autorizzazioni globali)](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    Verrà visualizzata la finestra di dialogo **Global Permissions Root - Add Permission** (Radice autorizzazioni globali - Aggiungi autorizzazione).

5. Nella finestra di dialogo **Global Permission Root - Add Permission** (Radice autorizzazioni globali - Aggiungi autorizzazione) fare clic su **Add** (Aggiungi) per scegliere l'utente o il gruppo.

    ![Scegliere un utente o un gruppo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    Verrà visualizzata la finestra di dialogo **Select Users/Groups** (Seleziona utenti/gruppi).

6. Nella finestra di dialogo **Select Users/Groups** (Seleziona utenti/gruppi) scegliere **BackupAdmin** e fare clic su **Add** (Aggiungi).

    In **Users** (Utenti) per l'account utente viene usato il formato *dominio\nomeutente*. Per usare un dominio diverso, sceglierlo dall'elenco **Domain** (Domini).

    ![Aggiungere l'utente BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Fare clic su **OK** per aggiungere gli utenti selezionati alla finestra di dialogo **Add Permission** (Aggiungi autorizzazione).

7. Dopo avere identificato l'utente, assegnare l'utente al ruolo. In **Assigned Role** (Ruolo assegnato) selezionare **BackupAdminRole** nell'elenco e quindi fare clic su **OK**.

    ![Assegnare l'utente a un ruolo](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Il nuovo account utente e il ruolo associato vengono visualizzati nell'elenco nella scheda **Manage** (Gestisci) del pannello **Global Permissions** (Autorizzazioni globali).


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Definizione di credenziali del server vCenter sul server di Backup di Azure

Prima di aggiungere il server VMware al server di Backup di Azure, installare l'[aggiornamento 1 per il server di Backup di Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Per aprire il server di Backup di Azure, fare doppio clic sull'icona sul desktop del server di Backup di Azure.

    ![Icona del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Se l'icona non è presente sul desktop, aprire il server di Backup di Azure nell'elenco delle app installate. Il nome dell'app del server di Backup di Azure è Backup di Microsoft Azure.

2. Nella console del server di Backup di Azure fare clic su **Gestione**, su **Server di produzione** e quindi sulla barra multifunzione fare clic su **Gestisci VMware**.

    ![Console del server di Backup di Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    Verrà visualizzata la finestra di dialogo **Gestisci credenziali**.

    ![Finestra di dialogo Gestisci credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Nella finestra di dialogo **Gestisci credenziali** fare clic su **Aggiungi** per aprire la finestra di dialogo **Aggiungi credenziali**.

4. Nella finestra di dialogo **Aggiungi credenziali** immettere il nome e la descrizione delle nuove credenziali. e quindi specificare il nome utente e la password. Il nome *Contoso Vcenter credential*, verrà usato per identificare le credenziali nella procedura successiva. Usare lo stesso nome utente e la stessa password usati per il server vCenter. Se il server vCenter e il server di Backup di Azure non sono nello stesso dominio, specificare il dominio in **Nome utente**.

    ![Finestra di dialogo Aggiungi credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Fare clic su **Aggiungi** per aggiungere le nuove credenziali al server di Backup di Azure. Le nuove credenziali vengono visualizzate nell'elenco della finestra di dialogo **Gestisci credenziali**.
    
    ![Finestra di dialogo Gestisci credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Per chiudere la finestra di dialogo **Gestisci credenziali**, fare clic sulla **X** nell'angolo superiore destro.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Aggiunta del server VMware al server di Backup di Azure

Per aggiungere il server vCenter al server di Backup di Azure, si usa l'Aggiunta guidata server di produzione.

Per aprire l'Aggiunta guidata server di produzione, completare la procedura seguente:

1. Nella console del server di Backup di Azure fare clic su **Gestione**, su **Server di produzione** e quindi su **Aggiungi**.

    ![Aprire l'Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    Verrà visualizzata la finestra di dialogo **Aggiunta guidata server di produzione**.

    ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Nella pagina **Selezionare il tipo di server di produzione** selezionare **Server VMware** e quindi fare clic su **Avanti**.

3. In **Nome del server/Indirizzo IP** specificare il nome di dominio completo (FQDN, Fully Qualified Domain Name) o l'indirizzo IP del server VMware. Se tutti i server ESXi sono gestiti dallo stesso vCenter, è possibile usare il nome vCenter.

    ![Specificare l'FQDN o l'indirizzo IP del server VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. In **Porta SSL** immettere la porta usata per comunicare con il server VMware. Usare la porta 443, ovvero la porta predefinita, a meno che non sia necessaria una porta diversa.

5. In **Specifica credenziale** selezionare le credenziali create in precedenza.

    ![Specifica credenziale](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Fare clic su **Aggiungi** per aggiungere il server VMware all'elenco **Server VMware aggiunti** e quindi fare clic su **Avanti** per passare alla pagina successiva della procedura guidata.

    ![Aggiungere il server VMware e le credenziali](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Nella pagina **Riepilogo** fare clic su **Aggiungi** per aggiungere il server VMware specificato al server di Backup di Azure.

    ![Aggiungere il server VMware al server di Backup di Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Il backup del server VMware è un backup senza agente. Il nuovo server viene quindi aggiunto immediatamente. Nella pagina **Fine** vengono visualizzati i risultati.

  ![Pagina Fine](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Per aggiungere più istanze del server vCenter al server di Backup di Azure, ripetere la procedura riportata in precedenza in questa sezione.

Dopo avere aggiunto un server vCenter al server di Backup di Azure, il passaggio successivo consiste nel creare un gruppo protezione dati. Il gruppo protezione dati specifica i vari dettagli per la conservazione a lungo o a breve termine e consente di definire e applicare i criteri di backup. I criteri di backup corrispondono alla pianificazione del momento in cui vengono eseguiti i backup e a ciò di cui viene eseguito il backup.


## <a name="configure-a-protection-group"></a>Configurazione di un gruppo protezione dati

Se non si è mai usato System Center Data Protection Manager o il server di Backup di Azure, vedere [Pianificare i backup su disco](https://technet.microsoft.com/library/hh758026.aspx) per preparare l'ambiente hardware. Dopo avere verificato la disponibilità di una risorsa di archiviazione appropriata, eseguire la procedura guidata Crea nuovo gruppo protezione dati per aggiungere macchine virtuali VMware.

1. Nella console del server di Backup di Azure fare clic su **Protezione** e quindi fare clic su **Nuovo** nella barra multifunzione per aprire la procedura guidata Crea nuovo gruppo protezione dati.

    ![Aprire la procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    Verrà visualizzata la finestra di dialogo della procedura guidata **Crea nuovo gruppo protezione dati**.

    ![Finestra di dialogo della procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Fare clic su **Avanti** per passare alla pagina **Selezione tipo di gruppo protezione dati**.

2. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server** e quindi fare clic su **Avanti**. Verrà visualizzata la pagina **Selezione membri del gruppo**.

3. Nella pagina **Selezione membri del gruppo** sono visualizzati i membri disponibili e quelli selezionati. Selezionare i membri da proteggere e quindi fare clic su **Avanti**.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Quando si seleziona un membro, se si seleziona una cartella che contiene altre cartelle o macchine virtuali, vengono selezionate anche le cartelle e le macchine virtuali contenute. L'inclusione di cartelle e VM nella cartella padre è denominata protezione a livello di cartella. Per rimuovere una cartella o una macchina virtuale, deselezionare la casella di controllo corrispondente.

    Se una VM o una cartella che contiene una VM, è già protetta in Azure, non è possibile selezionarla di nuovo. In altre parole, se una macchina virtuale è protetta in Azure, non è possibile applicare di nuovo la protezione per evitare di creare punti di recupero duplicati per la macchina virtuale stessa. Se si vuole visualizzare l'istanza del server di Backup di Azure che protegge già un membro, posizionare il mouse sopra il membro per visualizzare il nome del server di protezione.

4. Nella pagina **Seleziona metodo protezione dati** immettere un nome per il gruppo protezione dati. Sono selezionate la protezione a breve termine (su disco) e la protezione online. Per utilizzare la protezione online (su Azure), è necessario utilizzare la protezione a breve termine su disco. Fare clic su **Avanti** per procedere con l'intervallo di protezione a breve termine.

    ![Seleziona metodo protezione dati](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Nella pagina **Specifica obiettivi a breve termine** per **Intervallo di conservazione** specificare il numero di giorni per cui si vogliono mantenere i punti di recupero *archiviati su disco*. Se si vuole modificare l'ora e i giorni in cui vengono eseguiti i punti di recupero, fare clic su **Modifica**. I punti di recupero a breve termine sono backup completi e non backup incrementali. Quando si è soddisfatti degli obiettivi a breve termine, fare clic su **Avanti**.

    ![Specifica obiettivi a breve termine](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Nella pagina **Verifica allocazione dischi** esaminare e, se necessario, modificare lo spazio su disco per le macchine virtuali. Le allocazioni dischi consigliate sono basate sul periodo di mantenimento dati specificato nella pagina **Specifica obiettivi a breve termine**, sul tipo di carico di lavoro e sulle dimensioni dei dati protetti (identificati nel passaggio 3).  

  - **Dimensione dati:** la dimensione dei dati nel gruppo protezione dati.
  - **Spazio su disco:** la quantità di spazio su disco consigliata per il gruppo protezione dati. Se si vuole modificare questa impostazione, è consigliabile allocare uno spazio totale leggermente superiore rispetto alla quantità stimata per la crescita di ogni origine dati.
  - **Condividi percorso dati:** se si attiva la condivisione del percorso dati, è possibile eseguire il mapping di più origini dati nella protezione a un'unica replica e a un unico volume del punto di recupero. La condivisione del percorso non è supportata per tutti i carichi di lavoro.
  - **Aumenta automaticamente:** se si attiva questa impostazione e i dati del gruppo protezione superano l'allocazione iniziale, System Center Data Protection Manager tenta di aumentare le dimensioni del disco del 25%.
  - **Dettagli pool di archiviazione:** visualizza lo stato del pool di archiviazione, incluse dimensione totale e dimensione rimanente dei dischi.

    ![Rivedere l'allocazione dei dischi](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Una volta ottenuta l’allocazione di spazio adeguata, fare clic su **Avanti**.

7. Nella pagina **Scelta del metodo per la creazione della replica** specificare come si vuole generare la copia iniziale, o la replica, dei dati protetti nel server di Backup di Azure.

    Il valore predefinito è **Automaticamente tramite rete** e **Ora**. Se si usa il valore predefinito, è consigliabile specificare un'ora non di punta. Scegliere **In seguito** e specificare un giorno e un'ora.

    Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline tramite supporti di memorizzazione rimovibili.

    Dopo avere effettuato le scelte, fare clic su **Avanti**.

    ![Scelta del metodo per la creazione della replica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Nella pagina **Opzioni di verifica coerenza** selezionare come e quando automatizzare le verifiche della coerenza. È possibile eseguire verifiche della coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.

    Per non configurare la verifica automatica della coerenza, è possibile eseguire una verifica manuale. Nell'area di protezione della console del server di Backup di Azure fare clic con il pulsante destro del mouse sul gruppo protezione dati e quindi selezionare **Esegui verifica coerenza**.

    Fare clic su **Avanti** per passare alla pagina successiva.

9. Nella pagina **Specifica i dati da proteggere online** selezionare una o più origini dati da proteggere. È possibile selezionare i membri singolarmente o fare clic su **Seleziona tutto** per scegliere tutti i membri. Dopo avere scelto i membri, fare clic su **Avanti**.

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Nella pagina **Specificare la pianificazione dei backup online** specificare la pianificazione per la generazione di punti di recupero dal backup del disco. Dopo la generazione, il punto di recupero viene trasferito nell'insieme di credenziali di Servizi di ripristino di Azure. Quando si è soddisfatti della pianificazione del backup online, fare clic su **Avanti**.

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Nella pagina **Specificare i criteri di conservazione online** indicare per quanto tempo si vogliono mantenere i dati di backup in Azure. Dopo aver definito i criteri, fare clic su **Avanti**.

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Non esiste un limite di tempo per la conservazione dei dati in Azure. Quando si archiviano dati di punti di recupero in Azure, l'unico limite è un massimo di 9999 punti di recupero per ogni istanza protetta. In questo esempio l'istanza protetta è il server VMware.

12. Nella pagina **Riepilogo** rivedere i dettagli relativi ai membri e alle impostazioni del gruppo protezione dati e quindi fare clic su **Crea gruppo**.

    ![Riepilogo delle impostazioni e dei membri del gruppo protezione dati](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Passaggi successivi
Se si usa il server di Backup di Azure per proteggere i carichi di lavoro VMware, può essere utile usare il server di Backup di Azure per proteggere [server Microsoft Exchange](./backup-azure-exchange-mabs.md), [farm Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) o [database SQL Server](./backup-azure-sql-mabs.md).

Per informazioni sui problemi relativi alla registrazione dell'agente, alla configurazione del gruppo protezione dati e al backup dei processi, vedere [Risolvere i problemi del server di Backup di Azure](./backup-azure-mabs-troubleshoot.md) .

