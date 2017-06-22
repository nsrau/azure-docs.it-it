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
ms.date: 04/20/2017
ms.author: markgal;
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: faa3c9d5de759e4d8c0589153ea93e83a580f983
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Backup del server VMware in Azure

In questo articolo viene illustrato come configurare un server di Backup di Azure per proteggere il carico di lavoro del server VMware. In questo articolo si presuppone che sia stato già installato il server di Backup di Azure. Se il server di Backup di Azure non è installato, vedere l'articolo [Preparazione del backup dei carichi di lavoro con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).

Il server di backup di Azure è in grado di eseguire Backup o proteggere i server VMware vCenter versioni 6.0 e 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creazione di una connessione protetta al server vCenter

Per impostazione predefinita, il server di Backup di Azure comunica con i server vCenter tramite un canale HTTPS. Per abilitare la comunicazione protetta, è consigliabile installare il certificato dell’autorità di certificazione (CA, Certificate Authority) di VMware sul server di Backup di Azure. Qualora non sia richiesta una comunicazione protetta e si preferisca disabilitare il requisito HTTPS, vedere la sezione [Disabilitazione del protocollo di comunicazione sicura](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Per creare una connessione sicura tra il server di Backup di Azure e il server vCenter, importare il certificato attendibile sul server di Backup di Azure.

Di norma, si utilizza un browser sul computer del server di Backup di Azure per connettersi al server vCenter tramite client Web vSphere. La prima volta che si utilizza il browser del server di Backup di Azure per connettersi al server vCenter, la connessione non è protetta. L'immagine seguente mostra la connessione non protetta.

![esempio di connessione non protetta al server VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Per risolvere il problema e creare una connessione protetta, scaricare i certificati CA radice attendibili.

1. Nel browser sul server di Backup di Azure, digitare l'URL per il client Web vSphere.

  Viene visualizzata la pagina di accesso del client Web vSphere.

  ![client Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

  Nella parte inferiore delle informazioni per amministratori e sviluppatori, si trova il collegamento **Scarica certificati CA radice attendibili**.

  ![collegamento per scaricare i certificati radice attendibili della CA](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Qualora fosse impossibile trovare la pagina di accesso del client Web vSphere, controllare le impostazioni del proxy del browser.

2. Fare clic su **Scarica certificati CA radice attendibili**.

  Il server vCenter scarica un file sul computer locale. Il nome del file è **download**. A seconda del browser, viene visualizzato un messaggio che chiede se aprire o salvare il file.

  ![messaggio di download quando vengono scaricati i certificati](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Salvare il file in una cartella sul server di Backup di Azure. Durante il salvataggio del file, aggiungere l'estensione al nome del file .zip.

  Il file è un file con estensione .zip che contiene le informazioni sui certificati. L’aggiunta dell'estensione. zip consente di utilizzare gli strumenti di estrazione.

4. Fare clic con il pulsante destro del mouse su **download.zip** e scegliere Estrai tutto per estrarre il contenuto.

  Il file compresso estrae il contenuto in una cartella denominata **certs**. Nella cartella certs sono presenti due tipi di file. Il file del certificato radice ha un'estensione simile a: .0, .1 o .*numero*. Il file CRL ha un'estensione che inizia con .r0, .r1 e così via. Il file CRL è associato a un certificato.

  ![file di download estratto localmente ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Nella cartella **certs**, fare clic con il pulsante destro del mouse sul file del certificato radice, quindi su **Rinomina**.

  ![ridenominazione del certificato radice ](./media/backup-azure-backup-server-vmware/rename-cert.png)

  Modificare l'estensione del certificato radice in .crt. Quando viene chiesto di confermare la modifica dell'estensione, in quanto si potrebbe modificare la funzione del file, fare clic su Sì oppure OK. L’icona per il file cambia in icona per un certificato radice.

6. Fare clic con il pulsante destro del mouse sul certificato radice e nel menu a comparsa, selezionare **Installa certificato**.

  Viene visualizzata Importazione guidata certificati.

7. In Importazione guidata certificati selezionare **Computer locale** come destinazione per il certificato, quindi fare clic su **Avanti** per continuare.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

  Se viene chiesto di confermare le modifiche al computer, fare clic su Sì oppure OK, per tutte le modifiche.

8. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi fare clic su **Sfoglia**, per scegliere l’archivio dei certificati.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  Verrà visualizzata la finestra di dialogo Selezione archivio certificati.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Selezionare **Autorità di certificazione radice attendibili** come cartella di destinazione per i certificati e fare clic su **OK**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

  L'archivio certificati scelto compare in **Importazione guidata certificati**. Fare clic su **Avanti**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Nella schermata Completamento dell'Importazione guidata certificati verificare che il certificato si trovi nella cartella desiderata e fare clic su **Fine** per completare la procedura guidata.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Viene visualizzata una finestra di dialogo indicante il completamento dell'importazione.

11. Accedere al server vCenter per verificare che la connessione sia protetta.

  In caso di problemi durante l'importazione del certificato e qualora non sia possibile stabilire una connessione sicura, consultare la documentazione di VMware vSphere relativa al [Recupero dei certificati server](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Se sono previsti limiti di protezione all'interno dell'organizzazione e non si vuole abilitare il protocollo HTTPs, adottare la seguente procedura per disabilitare le comunicazioni sicure.

### <a name="disable-secure-communication-protocol"></a>Disabilitazione del protocollo di comunicazione sicura

Se l'organizzazione non richiede il protocollo di comunicazione protetta (HTTPS), adottare la procedura seguente per disabilitare il protocollo HTTPS. Per disabilitare il comportamento predefinito, creare una chiave di registro che ignori il comportamento predefinito.

1. Copiare e incollare il seguente testo in un file .txt.

  ```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```
2. Salvare il file con il nome **DisableSecureAuthentication.reg** sul server di Backup di Azure.

3. Fare doppio clic sul file per attivare la voce del registro.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Creare un ruolo e un account utente sul server vCenter

Sul server vCenter, un ruolo è un set predefinito di privilegi. Un amministratore del server sul server vCenter crea i ruoli e associa gli account utente ai ruoli per assegnare le autorizzazioni. Per definire le credenziali utente necessarie per eseguire il backup del server vCenter, creare un ruolo con privilegi specifici e associare l'account utente al ruolo.

Il server di Backup di Azure utilizza un nome utente e una password per l'autenticazione sul server vCenter. Il server di Backup di Azure sfrutta tali credenziali come autenticazione per ogni operazione di backup.

Per aggiungere un ruolo e dei privilegi del server vCenter per un amministratore di backup:

1. Accedere al server vCenter e nello **Strumento di spostamento**, fare clic su **Amministrazione**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Nella sezione **Amministrazione** selezionare **Ruoli** e nel pannello **Ruoli** fare clic sull'icona di aggiunta di ruoli contrassegnata dal simbolo +.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  Verrà visualizzata la finestra di dialogo **Crea ruolo**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Nella finestra di dialogo **Crea ruolo** nel campo **Nome ruolo**, digitare *BackupAdminRole*. È possibile scegliere un nome qualsiasi, ma deve essere riconoscibile per lo scopo del ruolo.

4. Selezionare i privilegi per la versione appropriata di vCenter, quindi scegliere **OK**. La tabella seguente identifica i privilegi richiesti per vCenter 6.0 e vCenter 5.5.

  Quando si selezionano i privilegi, fare clic sulla freccia di espansione nell'etichetta padre per espandere l'elemento padre e visualizzare i privilegi figlio. La selezione dei privilegi necessari VirtualMachine richiede diversi 'livelli' di profondità. Non è necessario selezionare tutti i privilegi figlio all'interno di un privilegio padre.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Dopo aver fatto clic su **OK**, il nuovo ruolo viene visualizzato nell'elenco nel pannello dei ruoli.

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



## <a name="create-vcenter-server-user-account-and-permissions"></a>Creazione di account utente e autorizzazioni del server vCenter

Una volta creato il ruolo con privilegi, creare un account utente. L'account utente contiene la coppia nome utente e password usata come credenziali usate per l'autenticazione.

1. Per creare un account utente, nello strumento di spostamento del server vCenter, fare clic su **Utenti e gruppi**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Fare clic sulla scheda Utenti e gruppi.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Nel pannello Utenti e gruppi di vCenter, selezionare la scheda **Utenti**, quindi fare clic sull’icona di aggiunta utenti contrassegnata dal simbolo +.

  Verrà visualizzata la finestra di dialogo Nuovo utente.

3. Nella finestra di dialogo Nuovo utente compilare i campi e fare clic su **OK**. Per questo esempio, digitare **BackupAdmin** come nome utente. La password deve essere una password complessa.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  Il nuovo account utente viene visualizzato nell'elenco.

4. Per associare l'account utente al ruolo, nello Strumento di spostamento, fare clic su **Autorizzazioni globali**. Nel riquadro delle autorizzazioni globali, selezionare la scheda **Gestisci**, quindi l'icona di aggiunta (simbolo +).

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  Viene aperta la finestra di dialogo Global Permissions Root - Add Permission (Radice autorizzazioni globali - Aggiungi autorizzazione).

5. In questa finestra di dialogo ****  fare clic su **Aggiungi** per scegliere l'utente o il gruppo.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  Verrà visualizzata la finestra di dialogo Seleziona utenti/gruppi.

6. Nella finestra di dialogo **Seleziona utenti/gruppi**, scegliere **BackupAdmin** e fare clic su **Aggiungi**.

  L’account utente nel campo Utenti ha il formato *dominio*`\`*nome utente*. Per utilizzare un dominio diverso, selezionarlo dall'elenco di domini.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Fare clic su **OK** per aggiungere gli utenti selezionati nella finestra di dialogo Aggiungi autorizzazione.

7. Dopo avere identificato l'utente, assegnare l'utente al ruolo. Nell'area Ruolo assegnato, selezionare **BackupAdminRole** e fare clic su **OK**.

  ![finestra di dialogo relativa al certificato con errore ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Il nuovo account utente e il ruolo associato vengono visualizzati nell'elenco nella scheda Gestione delle autorizzazioni globali.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Definizione di credenziali del server vCenter sul server di Backup di Azure

Prima di aggiungere il server VMware al server di Backup di Azure, installare l'[Aggiornamento 1 per il server di Backup di Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Per aprire il server di Backup di Azure, fare doppio clic sull'icona sul desktop del server di Backup di Azure.

  ![Icona del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Se l'icona non è presente sul desktop, aprire il server di Backup di Azure nell'elenco delle app installate. Il nome dell’app del server di Backup di Azure è 	Backup di Microsoft Azure.

2. Nella console del server di Backup di Azure fare clic su **Gestione**, quindi su **Server di produzione**, infine nella barra multifunzione fare clic su **Gestisci VMware**.

  ![Console del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  Viene visualizzata la finestra di dialogo Gestisci credenziali.

  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Nella finestra di dialogo Gestisci credenziali fare clic su **Aggiungi** per aprire la finestra di dialogo Aggiungi credenziali.

4. Nella finestra di dialogo Aggiungi credenziali, digitare un nome e una descrizione per le nuove credenziali, quindi specificare nome utente e password. Il nome delle credenziali, *Credenziali Vcenter Contoso* nell'esempio, indica come sono identificate le credenziali nella seguente procedura. Utilizzare gli stessi nome utente e password utilizzati sul server vCenter. Se il server vCenter e il server di Backup di Azure non sono nello stesso dominio, specificare il dominio nel nome utente.

  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

  Fare clic su **Aggiungi** per aggiungere le nuove credenziali al server di Backup di Azure. Le nuove credenziali vengono visualizzate nell'elenco nella finestra di dialogo Gestisci credenziali.
  ![Finestra di dialogo Gestisci credenziali del server di Backup di Microsoft Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Fare clic sulla **X** nell'angolo superiore destro per chiudere la finestra di dialogo Gestisci credenziali.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Aggiunta del server VMware al server di Backup di Azure

Per aprire la procedura guidata Aggiunta del server di produzione:

1. Nella console del server di Backup di Azure fare clic su **Gestione**, quindi su **Server di produzione**, e infine su **Aggiungi**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  Viene visualizzata Aggiunta guidata server di produzione.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Nella schermata Selezionare il tipo di server di produzione selezionare Server e fare clic su **Avanti**.

3. Nell'indirizzo IP/nome server specificare il nome di dominio completo (FQDN) o l'indirizzo IP del server VMware. Se tutti i server ESXi sono gestiti dallo stesso vCenter, è possibile utilizzare il nome vCenter.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Nella finestra di dialogo **Porta SSL** immettere la porta usata per comunicare con il server VMware. Usare la porta 443, ovvero la porta predefinita, a meno che non sia necessaria una porta diversa.

5. Nella finestra di dialogo **Specifica credenziali**, selezionare le credenziali create.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Fare clic su **Aggiungi** per aggiungere il server VMware all'elenco di **Server VMware aggiunti** e fare clic su **Avanti** per passare alla schermata successiva della procedura guidata.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Nella schermata **Attività** fare clic su **Aggiungi** per aggiungere il server VMware specificato al server di Backup di Azure.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Poiché il backup del server VMware è un backup senza agente, l'aggiunta del nuovo server avviene in pochi secondi. La schermata Fine mostra i risultati.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Per aggiungere più server vCenter al server di Backup di Azure, ripetere i passaggi precedenti in questa sezione.

Dopo avere aggiunto un server vCenter al server di Backup di Azure, il passaggio successivo consiste nel creare un gruppo protezione dati. Il gruppo protezione dati specifica i vari dettagli per la conservazione a lungo o a breve termine e consente di definire e applicare i criteri di backup. I criteri di backup rappresentano la pianificazione relativa al momento in cui vengono eseguiti i backup e ciò di cui viene eseguito il backup.


## <a name="configure-a-protection-group"></a>Configurazione di un gruppo protezione dati

Se non è stato usato in precedenza System Center Data Protection Manager o il server di Backup di Azure, vedere l'argomento [Pianificare i backup su disco](https://technet.microsoft.com/library/hh758026.aspx), per preparare l'ambiente hardware. Dopo avere verificato la disponibilità di una risorsa di archiviazione appropriata, adottare la procedura guidata Crea nuovo gruppo protezione dati per aggiungere le macchine virtuali VMware.

1. Nella console del server di Backup di Azure fare clic su **Protezione** e quindi fare clic su **Nuovo** nella barra multifunzione per aprire la procedura guidata Crea nuovo gruppo protezione dati.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  Verrà visualizzata la procedura guidata Crea nuovo gruppo protezione dati.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Fare clic su **Avanti** nella schermata **Selezione tipo di gruppo protezione dati**.

2. Nella schermata Selezione tipo di gruppo protezione dati selezionare **Server** e fare clic su **Avanti**.

3. Nella schermata Seleziona membri del gruppo è possibile visualizzare i membri disponibili e quelli selezionati. Selezionare i membri da proteggere e fare clic su **Avanti**.

  ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

  Quando si seleziona un membro, se si seleziona una cartella che contiene altre cartelle o VM, vengono selezionate anche queste cartelle e VM. L'inclusione di cartelle e VM nella cartella padre è denominata protezione a livello di cartella. Deselezionando la casella di controllo è possibile escludere qualsiasi cartella o VM.

  Se una VM o una cartella che contiene una VM, è già protetta in Azure, non è possibile selezionarla di nuovo. Quando una VM è protetta in Azure, non è possibile applicare di nuovo la protezione per evitare di creare punti di recupero duplicati per una VM. Se si vuole visualizzare il server di Backup di Azure che protegge già un membro, posizionare il mouse sopra il membro per visualizzare il nome del server di protezione.

4. Nella schermata Seleziona metodo protezione dati digitare un nome per il gruppo protezione dati. Sono selezionate la protezione a breve termine (su disco) e la protezione online. Per utilizzare la protezione online (su Azure), è necessario utilizzare la protezione a breve termine su disco. Fare clic su **Avanti** per procedere con l'intervallo di protezione a breve termine.

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

  Una volta ottenuta l’allocazione di spazio adeguata, fare clic su **Avanti**.

7. Nella schermata Scelta del metodo per la creazione della replica specificare come si vuole generare la copia iniziale o replica dei dati protetti nel server di Backup di Azure.

  Il valore predefinito è **Automaticamente tramite rete** e **Ora**. Se si usa il valore predefinito, è consigliabile specificare un'ora non di punta. Scegliere **In seguito** e specificare un giorno e un'ora.

  Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline mediante supporti rimovibili.

  Dopo avere effettuato le scelte, fare clic su **Avanti**.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Nella schermata **Opzioni di verifica coerenza** selezionare come e quando automatizzare le verifiche coerenza. È possibile eseguire verifiche coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.

  Per non configurare il controllo di coerenza automatico, è possibile eseguire una verifica manuale. Nell'area di protezione della console del server di Backup di Azure, fare clic con il pulsante destro del mouse sul gruppo protezione dati e selezionare **Esegui verifica coerenza**.

  Fare clic su **Avanti** per passare alla schermata successiva.

9. Nella schermata **Specificare i dati da proteggere online** selezionare le origini dati da proteggere. È possibile selezionare i membri singolarmente o fare clic su **Seleziona tutto** per scegliere tutti i membri. Dopo avere scelto i membri, fare clic su **Avanti**.

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

