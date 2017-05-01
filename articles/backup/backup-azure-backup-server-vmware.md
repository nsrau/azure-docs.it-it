---
title: Usare il server di Backup di Azure per proteggere i VMware Server | Documentazione Microsoft
description: Eseguire il backup di un server VMware in Azure o su disco con il server di Backup di Azure. Usare questo articolo per proteggere il carico di lavoro VMware.
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
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Backup del server VMware in Azure

Questo articolo illustra come connettere un server VMware a un server di Backup di Azure in modo che sia possibile eseguire il backup del contenuto del server VMware nel cloud. In questo articolo si presuppone che sia stato già installato il server di Backup di Azure.

## <a name="create-secure-connection-to-vmware-server"></a>Creare una connessione protetta al server VMware

Per proteggere un server VMware, il server di Backup di Azure deve essere in grado di connettersi ad esso in modo sicuro. Per abilitare la connessione protetta, installare un certificato valido sul server VMware Server e sul server di Backup di Azure.

Quando si esegue la connessione al server VMware, se l'URL non è sicuro, è necessario esportare il certificato in modo che la connessione al sito sia protetta.
![esempio di connessione non protetta al server VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. Fare clic su https (barrato) e quindi nel menu a comparsa fare clic sul collegamento dei dettagli.

  A seconda del browser potrebbe essere necessario fare clic su **Impostazioni** > **Altri strumenti** > **Strumenti di sviluppo**, quindi selezionare la scheda della sicurezza.

  ![esempio di messaggio di errore di connessione non protetta](./media/backup-azure-backup-server-vmware/security-tab.png)

2. Nelle informazioni relative ai dettagli nella scheda Sicurezza fare clic su **Visualizza certificato**.

  ![esempio di messaggio di errore di connessione non protetta](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  Viene aperta la finestra di dialogo Certificato.

3. Selezionare la scheda Percorso certificazione nella finestra di dialogo Certificato.  

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  Il certificato selezionato non è attendibile perché nel caso di questo certificato non è stata trovata l'autorità emittente. Potrebbero esserci altri motivi per cui il certificato non è attendibile.

4. Per esportare il certificato nel computer locale, fare clic sulla scheda dettagli e quindi su Copia su file.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  Viene aperta Esportazione guidata certificati.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  Fare clic su **Avanti** per continuare con la procedura guidata.

5. Nella schermata Formato file di esportazione specificare il formato desiderato per il certificato. Se non si dispone di un formato preferito, accettare il formato di file predefinito per il certificato e fare clic su **Avanti**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. Nella schermata File da esportare assegnare un nome al certificato e quindi fare clic su Esplora per selezionare il percorso in cui archiviare il certificato nel computer locale. Salvare il certificato in un percorso in cui è possibile recuperarlo.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. Dopo avere esportato il certificato, passare al percorso in cui è stato salvato, fare clic con il pulsante destro del mouse sul certificato e nel menu scegliere **Installa certificato**.

  Viene visualizzata Importazione guidata certificati.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. In Importazione guidata certificati selezionare **Computer locale** come destinazione per il certificato, quindi fare clic su **Avanti** per continuare.

9. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi fare clic su **Sfoglia**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  Verrà visualizzata la finestra di dialogo Selezione archivio certificati.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-store.png)

  Scegliere la cartella di destinazione per i certificati e fare clic su **OK**. Se non si conosce la cartella da usare, scegliere Autorità di certificazione fonti attendibili. Verrà visualizzata la cartella di destinazione selezionata nella finestra di dialogo di archivio certificati. Fare clic su **Avanti** per importare il certificato.

10. Nella schermata Completamento dell'Importazione guidata certificati verificare che il certificato si trovi nella cartella desiderata e fare clic su Fine per completare la procedura guidata.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Viene visualizzata una finestra di dialogo indicante il completamento dell'importazione.

11. Accedere alla VM VMware per verificare che sia disponibile una connessione protetta al server VMware. Il server di Backup di Azure si connette al server VMware tramite un canale HTTPs sicuro. Se sono previsti limiti di protezione all'interno dell'organizzazione e non si vuole abilitare il protocollo HTTPs, disabilitare la comunicazione sicura tramite il registro. Tuttavia è consigliabile installare i certificati nel server di Backup di Azure e nel server VMware per consentire la comunicazione protetta.


## <a name="create-role-and-user-account-on-vmware-server"></a>Creare account utente e un ruolo nel server VMware

Il server di Backup di Azure comunica con un server VMware remoto mediante l'autenticazione delle credenziali dell'utente di VMware specificato. Il server di Backup di Azure autentica le credenziali dell'utente di VMware per tutte le operazioni di backup. Usare Aggiunta guidata server di produzione del server di Backup di Azure per abilitare il server di Backup di Azure per comunicare in modo sicuro con il server VMware. Esistono tre fasi per impostare una relazione tra il server di Backup di Azure e 

- Creare un ruolo utente con privilegi assegnati
- Creare un account utente con le credenziali: nome utente e password
- Aggiungere l'account utente del server VMware al server di Backup di Azure

, se si usa Aggiunta guidata server di produzione. La coppia nome utente e password viene archiviata come credenziale.


### <a name="create-user-role-and-add-privileges"></a>Creare un ruolo utente e aggiungere i privilegi
All'account utente VMware, che viene specificato nella credenziale del server di Backup di Azure, devono essere stati associati determinati privilegi. Tuttavia, i privilegi sono associati a un ruolo utente ed è necessario prima creare un ruolo utente, quindi aggiungere i privilegi specifici di questo ruolo. I privilegi associati al ruolo utente sono relativi a un amministratore di backup.

1. Per creare un nuovo ruolo utente VMware, accedere al server VMware e nel pannello dello **strumento di spostamento** fare clic su **Amministrazione**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Per creare un nuovo ruolo, nella sezione **Amministrazione** selezionare **Ruoli** e nel pannello **Ruoli** fare clic sull'icona di aggiunta di ruoli contrassegnata dal simbolo +.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  Verrà visualizzata la finestra di dialogo **Crea ruolo**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Nella finestra di dialogo **Crea ruolo** digitare un nome per il ruolo nel campo **Nome ruolo**. In questo esempio verrà usato il nome *BackupAdminRole*. È possibile scegliere un nome qualsiasi, ma deve essere identificativo del ruolo.

4. Selezionare i privilegi da applicare al ruolo utente. Selezionare i privilegi elencati di seguito. Quando si selezionano i privilegi, fare clic sulla freccia di espansione nell'etichetta padre per espandere l'elemento padre e visualizzare i privilegi figlio. Non è necessario selezionare tutti i privilegi figlio all'interno di un privilegio padre.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.labelr
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  Dopo avere selezionato i privilegi, fare clic su **OK**. Il nuovo ruolo viene visualizzato nell'elenco nel pannello dei ruoli.

### <a name="create-a-user-account-and-assign-permissions"></a>Creare un account utente e assegnare le autorizzazioni

Dopo avere definito il ruolo utente con privilegi, creare un account utente. Quando si crea l'account utente, lo si assegna a un ruolo utente specifico che concede all'account i privilegi associati. L'account utente contiene la coppia nome utente e password usata come credenziali usate per l'autenticazione.

1. Per creare un nuovo account utente, nel pannello dello strumento di spostamento del server VMware fare clic su **Utenti e gruppi**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Fare clic sulla scheda Utenti e gruppi.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Nel pannello Utenti e gruppi di VMware fare clic sull'icona di aggiunta di utenti, contrassegnata dal simbolo + nella scheda Utenti.

  Verrà visualizzata la finestra di dialogo Nuovo utente.

3. L'account utente che si crea contiene la coppia nome utente e password usata come credenziali. Nella finestra di dialogo Nuovo utente compilare i campi e fare clic su **OK**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  Il nuovo account utente viene visualizzato nell'elenco.

4. Ora che è stato creato l'account utente, associarlo al ruolo utente che ha le autorizzazioni desiderate. Nel pannello dello strumento di spostamento fare clic su **Autorizzazioni globali**. Nel riquadro delle autorizzazioni globali fare clic sulla scheda **Gestisci** e quindi sull'icona di aggiunta (simbolo +).

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  Viene aperta la finestra di dialogo Global Permissions Root - Add Permission (Radice autorizzazioni globali - Aggiungi autorizzazione).

5. In questa finestra di dialogo ****  fare clic su **Aggiungi** per scegliere l'utente o il gruppo.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  Verrà visualizzata la finestra di dialogo Seleziona utenti/gruppi.

6. Nella finestra di dialogo **Seleziona utenti/gruppi** selezionare l'account utente creato e fare clic su **Aggiungi**. L'account utente selezionato viene visualizzato nel campo Utenti. Il nome utente viene visualizzato nel campo Utenti nel formato *dominio*`\`*nome utente*.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Fare clic su **OK** per aggiungere gli utenti selezionati nella finestra di dialogo Aggiungi autorizzazione.

7. Dopo avere identificato l'utente, assegnare l'utente al ruolo. Nell'area Ruolo assegnato, selezionare il ruolo e fare clic su **OK** dal menu a discesa.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Il nuovo account utente e il ruolo associato vengono visualizzati nell'elenco nella scheda Gestione delle autorizzazioni globali.


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>Aggiungere le credenziali dell'account utente VMware are al server di Backup di Azure

Prima di aggiungere il server VMware al server di Backup di Azure, assicurarsi di avere installato l'[aggiornamento 1 per il server di Backup di Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Fare clic sull'icona seguente sul desktop del server per aprire la console del server di Backup di Azure.

  ![Icona del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Se l'icona non è presente sul desktop è possibile aprire il server di Backup di Azure nell'elenco delle app installate. Nell'elenco delle app installate, l'app Server di Backup di Azure è denominata Backup di Microsoft Azure.

2. Nella console del server di Backup di Azure fare clic su **Gestione**, quindi su **Server di produzione**, infine nella barra multifunzione fare clic su **Gestisci VMware**.

  ![Console del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  Viene visualizzata la finestra di dialogo Gestisci credenziali.

  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Nella finestra di dialogo Gestisci credenziali fare clic su **Aggiungi** per aprire la finestra di dialogo Aggiungi credenziali.

4. Nella finestra di dialogo Aggiungi credenziali digitare un nome e una descrizione per le nuove credenziali. Il nome utente e la password devono essere le stesse usate in fase di creazione dell'account utente nel server VMware.

  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  Fare clic su **Aggiungi** per aggiungere le nuove credenziali al server di Backup di Azure. Le nuove credenziali vengono visualizzate nell'elenco nella finestra di dialogo Gestisci credenziali.
  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Fare clic su **X** nell'angolo superiore destro per chiudere la finestra di dialogo Gestisci credenziali.


## <a name="add-vmware-server-to-azure-backup-server"></a>Aggiungere il server VMware al server di Backup di Azure

per aprire Aggiunta guidata server di produzione

1. Nella console del server di Backup di Azure fare clic su **Gestione**, quindi su **Server di produzione**, e infine su **Aggiungi**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  Viene visualizzata Aggiunta guidata server di produzione.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Nella schermata Selezionare il tipo di server di produzione selezionare Server e fare clic su **Avanti**.

3. Nell'indirizzo IP/nome server specificare il nome di dominio completo (FQDN) o l'indirizzo IP del server VMware. Se tutti i server ESXi sono gestiti dallo stesso vCenter, è possibile immettere il nome VMware.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Nella finestra di dialogo **Porta SSL** immettere la porta usata per comunicare con il server VMware. Usare la porta 443, ovvero la porta predefinita, a meno che non sia necessaria una porta diversa.

5. Nella finestra di dialogo **Specifica credenziale** è possibile creare una nuova credenziale o selezionare la credenziale esistente. Nella sezione precedente è creata una credenziale. Selezionare questa credenziale.

  Se non è disponibile una credenziale o se è necessario creare una nuova credenziale, fare clic su **Aggiungi nuova credenziale**, creare la nuova credenziale e fare clic su **OK**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. Fare clic su **Aggiungi** per aggiungere il server VMware all'elenco di **server VMware aggiunti** e fare clic su **Avanti per passare alla schermata successiva della procedura guidata.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Nella schermata **Attività** fare clic su **Aggiungi** per aggiungere il server VMware specificato al server di Backup di Azure.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Poiché il backup del server VMware è un backup senza agente, l'aggiunta del nuovo server avviene in pochi secondi. È possibile aggiungere più server VMware al server di Backup di Azure ripetendo i passaggi precedenti in questa sezione.

Dopo avere aggiunto un server VMware al server di Backup di Azure, il passaggio successivo consiste nel creare un gruppo protezione dati. Il gruppo protezione dati specifica i vari dettagli per la conservazione a lungo o a breve termine e consente di definire e applicare i criteri di backup. I criteri di backup rappresentano la pianificazione relativa al momento in cui vengono eseguiti i backup e ciò di cui viene eseguito il backup.


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>Configurare un gruppo protezione dati per eseguire il backup del server VMware

Se non è stato usato in precedenza System Center Data Protection Manager o il server di Backup di Azure, vedere l'argomento [Pianificare i backup su disco](https://technet.microsoft.com/library/hh758026.aspx), per preparare l'ambiente hardware. Dopo avere verificato che si dispone della risorsa di archiviazione appropriata, usare la procedura guidata Crea nuovo gruppo protezione dati per aggiungere le VM specifiche.

1. Nella console del server di Backup di Azure fare clic su **Protezione** e quindi fare clic su **Nuovo** nella barra multifunzione per aprire la procedura guidata Crea nuovo gruppo protezione dati.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  Verrà visualizzata la procedura guidata Crea nuovo gruppo protezione dati.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Fare clic su **Avanti** nella schermata **Selezione tipo di gruppo protezione dati**.

2. Nella schermata Selezione tipo di gruppo protezione dati selezionare **Server** e fare clic su **Avanti**.

3. Nella schermata Seleziona membri del gruppo è possibile visualizzare i membri disponibili e quelli selezionati. Selezionare i membri da proteggere e fare clic su **Avanti**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  Quando si seleziona un membro, se si seleziona una cartella che contiene altre cartelle o VM, vengono selezionate anche queste cartelle e VM. L'inclusione di cartelle e VM nella cartella padre è denominata protezione a livello di cartella. Deselezionando la casella di controllo è possibile escludere qualsiasi cartella o VM.

  Se una VM o una cartella che contiene una VM, è già protetta in Azure, non è possibile selezionarla di nuovo. Quando una VM è protetta in Azure, non è possibile applicare di nuovo la protezione per evitare di creare punti di recupero duplicati per una VM. Se si vuole visualizzare il server di Backup di Azure che protegge già un membro, posizionare il mouse sopra il membro per visualizzare il nome del server di protezione.

4. Nella schermata Seleziona metodo protezione dati digitare un nome per il gruppo protezione dati. Per **Metodo di protezione** selezionare il percorso in cui si intende eseguire il backup dei dati. Il backup di dati viene eseguito su disco per la protezione a breve termine. Il backup di dati viene eseguito nel cloud (Azure) per la protezione a lungo termine. Fare clic su **Avanti** per procedere con l'intervallo di protezione a breve termine.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Nella schermata Specifica obiettivi a breve termine per **Intervallo di conservazione** specificare il numero di giorni per cui si vuole mantenere i punti di recupero *archiviati su disco*. Se si vuole modificare l'ora e i giorni in cui vengono eseguiti i punti di recupero, fare clic su **Modifica**. I punti di recupero a breve termine sono backup completi e non backup incrementali. Quando si è soddisfatti degli obiettivi a breve termine, fare clic su **Avanti**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Nella schermata Verifica allocazione dischi esaminare e, se necessario, modificare lo spazio su disco per le VM. Le allocazioni di dischi consigliate sono basate sull'intervallo di conservazione specificato nella schermata precedente, il tipo di carico di lavoro e le dimensioni dei dati protetti (identificati nel passaggio 3).  

  - Dimensioni dati: le dimensioni dei dati del gruppo protezione dati.
  - Spazio su disco: la quantità di spazio su disco consigliato per il gruppo protezione dati. Se si vuole modificare questa impostazione, è consigliabile allocare uno spazio totale leggermente superiore rispetto alla quantità stimata per la crescita di ogni origine dati.
  - Condividi percorso dati: se si abilita la condivisione del percorso dati, è possibile eseguire il mapping di più origini dati nella protezione a una singola replica e volume del punto di recupero. La condivisione del percorso non è supportata per tutti i carichi di lavoro.
  - Aumenta automaticamente: se si abilita questa impostazione, se i dati del gruppo protezione dati superano l'allocazione iniziale, DPM tenta di aumentare le dimensioni del disco del 25%.
  - Dettagli pool di archiviazione: mostra lo stato corrente del pool di archiviazione, inclusi dimensione totale e dimensioni del disco rimanenti.

    ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  Al termine, fare clic su **Avanti**.

7. Nella schermata Scelta del metodo per la creazione della replica specificare come si vuole generare la copia iniziale o replica dei dati protetti nel server di Backup di Azure.

  Il valore predefinito è **Automaticamente tramite rete** e **Ora**. Se si usa il valore predefinito, è consigliabile specificare un'ora non di punta. Scegliere **In seguito** e specificare un giorno e un'ora.

  Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline mediante supporti rimovibili.

  Dopo avere effettuato le scelte, fare clic su **Avanti**.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Nella schermata **Opzioni di verifica coerenza** selezionare come e quando automatizzare le verifiche coerenza. È possibile eseguire verifiche coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.

  Se non si vuole configurare il controllo automatico di coerenza, è possibile eseguire una verifica manuale in qualsiasi momento facendo clic sul gruppo protezione dati nell'area di protezione della console del server di Backup di Azure e selezionare Esegui verifica coerenza.

  Fare clic su **Avanti** per passare alla schermata successiva.

9. Nella schermata **Specificare i dati da proteggere online** selezionare le origini dati da proteggere. È possibile selezionare i membri singolarmente o fare clic su **Seleziona tutto** per scegliere tutti i membri. Dopo avere scelto i membri, fare clic su **Avanti*.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Nella schermata **Specificare la pianificazione dei backup online** specificare la pianificazione per la generazione di punti di recupero dal backup del disco. Una volta generato il punto di recupero, questo viene trasferito all'insieme di credenziali di Servizi di ripristino in Azure. Quando si è soddisfatti della pianificazione del backup online, fare clic su **Avanti**.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Nella schermata Specificare i criteri di conservazione online indicare per quanto tempo si vuole conservare i dati di backup in Azure. Dopo avere definito i criteri, fare clic su **Avanti**.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/retention-policy.png)

  Non esiste un limite di tempo per la conservazione dei dati in Azure. Quando si archiviano i dati dei punti di recupero in Azure, l'unico limite è che non è possibile avere più di 9.999 punti di recupero per ogni istanza protetta. In questo esempio l'istanza protetta è il server VMware.

12. Nella schermata di riepilogo esaminare i dettagli per il gruppo protezione dati. Annotare i membri del gruppo e le impostazioni. Quando si è soddisfatti con le impostazioni, fare clic su **Crea gruppo**.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Passaggi successivi
Se si usa il server di Backup di Azure per proteggere i carichi di lavoro VMware, è possibile essere interessati all'uso del server di Backup di Azure per proteggere il [server di Microsoft](./backup-azure-exchange-mabs.md), la [farm Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) o un [SQL Server](./backup-azure-sql-mabs.md).

Vedere [Risoluzione dei problemi del server di Backup di Azure](./backup-azure-mabs-troubleshoot.md) per informazioni sui problemi relativi alla registrazione dell'agente, la configurazione del gruppo protezione dati e i processi di backup.

