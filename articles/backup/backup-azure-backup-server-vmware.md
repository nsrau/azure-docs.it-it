---
title: Eseguire il backup di macchine virtuali VMware con il server di Backup di Azure
description: Questo articolo illustra come usare server di Backup di Azure per eseguire il backup di macchine virtuali VMware in esecuzione su un server VMware vCenter/ESXi.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: dacurwin
ms.openlocfilehash: df41907ee10b54ab3bfaeb548e085617f7d79084
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903237"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Eseguire il backup di macchine virtuali VMware con il server di Backup di Azure

Questo articolo illustra come eseguire il backup in Azure di macchine virtuali VMware in esecuzione in host VMware ESXi/server vCenter usando il server di Backup di Azure.

Questo articolo spiega come:

- Configurare un canale sicuro in modo che il server di Backup di Azure possa comunicare con i server VMware tramite HTTPS.
- Configurare un account VMware che il server di Backup di Azure userà per accedere al server VMware.
- Aggiungere le credenziali dell'account a Backup di Azure.
- Aggiungere il server VMware o ESXi al server di Backup di Azure.
- Configurare un gruppo protezione contenente le macchine virtuali VMware di cui si vuole eseguire il backup, specificare le impostazioni di backup e pianificare il backup.

## <a name="before-you-start"></a>Prima di iniziare

- Verificare che sia in esecuzione una versione di vCenter/ESXi supportata per il backup, ovvero la versione 6.5, 6.0 o 5.5.
- Assicurarsi di aver configurato il server di Backup di Azure. In caso contrario, [farlo](backup-azure-microsoft-azure-backup.md) prima di iniziare. È consigliabile eseguire il server di Backup di Azure con gli aggiornamenti più recenti.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Creazione di una connessione protetta al server vCenter

Per impostazione predefinita, il server di Backup di Azure comunica con i server VMware tramite HTTPS. Per configurare la connessione HTTPS, scaricare il certificato dell'autorità di certificazione (CA) di VMware e importarlo nel server di Backup di Azure.

### <a name="before-you-begin"></a>Prima di iniziare

- Se non si vuole usare HTTPS, è possibile [disabilitare la convalida del certificato HTTPS per tutti i server VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Di solito, per connettersi al server vCenter/ESXi tramite il client Web vSphere si usa un browser nel computer server di Backup di Azure. La prima volta che si esegue questa operazione, la connessione non è sicura e visualizza quanto segue.
- È importante comprendere il modo in cui il server di Backup di Azure gestisce i backup.
  - Come primo passaggio il server di Backup di Azure esegue il backup dei dati nell'archiviazione su disco locale. Il server di Backup di Azure usa un pool di archiviazione, un set di dischi e volumi in cui archivia i punti di ripristino del disco per i dati protetti. Il pool di archiviazione può essere un sistema di archiviazione diretta (DAS), una SAN Fibre Channel oppure una SAN o dispositivo di archiviazione iSCSI. È importante assicurarsi di disporre di spazio di archiviazione sufficiente per il backup locale dei dati della macchina virtuale VMware.
  - Il server di Backup di Azure esegue quindi il backup dallo spazio di archiviazione su disco locale ad Azure.
  - [Leggere questo articolo](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) per capire quanto spazio di archiviazione è necessario. Le informazioni sono per DPM ma possono essere usate per il server di Backup di Azure.

### <a name="set-up-the-certificate"></a>Configurare il certificato

Configurare un canale sicuro come indicato di seguito:

1. Nel browser del server di Backup di Azure immettere l'URL del client Web vSphere. Se non viene visualizzata la pagina di accesso, verificare le impostazioni proxy della connessione e del browser.

    ![Client Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Nella pagina di accesso del client Web vSphere, fare clic su **Download trusted root CA certificates** (Scarica certificati CA radice attendibili).

    ![Scaricare certificati CA radice attendibili](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Viene scaricato un file denominato **download**. A seconda del browser, viene visualizzato un messaggio che chiede se aprire o salvare il file.

    ![Scaricare il certificato della CA](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Salvare il file nel computer del server di Backup di Azure con l'estensione ZIP.

5. Fare clic con il pulsante destro del mouse su **download.zip** > **Estrai tutto**. Il contenuto del file ZIP viene estratto nella cartella **certs**, che contiene:
   - L'estensione del file del certificato radice inizia con una sequenza numerata, ad esempio .0 e .1.
   - L'estensione del file CRL inizia con una sequenza simile a .r0 o .r1. Il file CRL è associato a un certificato.

    ![Scaricare i certificati](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. Nella cartella **certs** fare clic con il pulsante destro del mouse sul file del certificato radice > **Rinomina**.

    ![Ridenominazione del certificato radice](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Modificare l'estensione del certificato radice in .crt e confermare. L'icona del file si trasforma nell'icona che rappresenta i certificati radice.

8. Fare clic con il pulsante destro del mouse sul certificato radice e nel menu a comparsa, selezionare **Installa certificato**.

9. In **Importazione guidata certificati** selezionare **Computer locale** come destinazione per il certificato, quindi fare clic su **Avanti**. Se viene visualizzata la richiesta di conferma delle modifiche al computer, confermare.

    ![Pagina iniziale della procedura guidata](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Nella pagina **Archivio certificati** selezionare **Colloca tutti i certificati nel seguente archivio** e quindi fare clic su **Sfoglia**per scegliere l'archivio certificati.

    ![Archiviazione dei certificati](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. In **Selezione archivio certificati** selezionare **Autorità di certificazione radice disponibile nell'elenco locale** come cartella di destinazione per i certificati e quindi fare clic su **OK**.

    ![Cartella di destinazione dei certificati](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. Nella pagina di **completamento dell'importazione guidata certificati** verificare la cartella e quindi fare clic su **Fine**.

    ![Verificare che i certificati si trovino nella cartella corretta](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Dopo la conferma dell'importazione del certificato, accedere al server vCenter per verificare che la connessione sia sicura.

### <a name="disable-https-certificate-validation"></a>Disabilitare la convalida del certificato HTTPS

Se si dispone di limiti di protezione all'interno dell'organizzazione e non si vuole usare il protocollo HTTPS tra i server VMware e il computer server di Backup di Azure, disabilitare HTTPS come indicato di seguito:

1. Copiare e incollare il seguente testo in un file .txt.

       ```text
      Editor del registro di sistema di Windows versione 5,00 [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare] "IgnoreCertificateValidation" = DWORD: 00000001
       ```

2. Salvare il file con il nome **DisableSecureAuthentication.reg** nel computer del server di Backup di Azure.

3. Fare doppio clic sul file per attivare la voce del registro.

## <a name="create-a-vmware-role"></a>Creare un ruolo VMware

Il server di Backup di Azure richiede un account utente con autorizzazioni di accesso al server vCenter o all'host ESXi. Creare un ruolo VMware con privilegi specifici e quindi associare un account utente al ruolo.

1. Accedere al server vCenter (o all'host ESXi se non si usa il server vCenter).
2. Nel pannello **Navigator** (Strumento di navigazione) fare clic su **Administration** (Amministrazione).

    ![amministrazione](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. In **Administration** > **Roles** (Amministrazione > Ruoli) fare clic sull'icona di aggiunta ruolo (il simbolo +).

    ![Aggiungi ruolo](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. In **Create Role** > **Role name** (Crea ruolo > Nome ruolo) immettere *BackupAdminRole*. È possibile scegliere un nome qualsiasi, ma deve essere riconoscibile per lo scopo del ruolo.

5. Selezionare i privilegi, come riepilogato nella tabella seguente e quindi fare clic su **OK**.  Il nuovo ruolo viene visualizzato nell'elenco nel pannello dei **ruoli**.
   - Fare clic sull'icona accanto all'etichetta padre per espandere l'elemento padre e visualizzare i privilegi figlio.
   - Per selezionare i privilegi VirtualMachine, è necessario scendere di diversi livelli nella gerarchia padre-figlio.
   - Non è necessario selezionare tutti i privilegi figlio all'interno di un privilegio padre.

    ![Gerarchia di privilegi padre-figlio](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Autorizzazioni ruoli

**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot

## <a name="create-a-vmware-account"></a>Configurare un account VMware

1. Nel pannello **Navigator** (Strumento di navigazione) del server vCenter fare clic su **Users and Groups** (Utenti e gruppi). Se non si usa il server vCenter, creare l'account nell'host ESXi appropriato.

    ![Opzione Users and Groups (Utenti e gruppi)](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Viene visualizzato il pannello **utenti e gruppi di vCenter** .

2. Nel pannello **vCenter Users and Groups** (Utenti e gruppi di vCenter) selezionare la scheda **Users** (Utenti) e quindi fare clic sull'icona Aggiungi utenti (il simbolo +).

    ![Pannello vCenter Users and Groups (Utenti e gruppi di vCenter)](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Nella finestra di dialogo **New User** (Nuovo utente) aggiungere le informazioni dell'utente > **OK**. In questa procedura il nome utente è BackupAdmin.

    ![Finestra di dialogo New User (Nuovo utente)](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Per associare l'account utente al ruolo, nel pannello **Navigator** (Strumento di navigazione) fare clic su **Global Permissions** (Autorizzazioni globali). Nel pannello **Global Permissions** (Autorizzazioni globali) selezionare la scheda **Manage** (Gestisci) e quindi fare clic sull'icona Aggiungi (il simbolo +).

    ![Pannello Global Permissions (Autorizzazioni globali)](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. Nella finestra di dialogo **Global Permission Root - Add Permission** (Radice autorizzazioni globali - Aggiungi autorizzazione) fare clic su **Add** (Aggiungi) per scegliere l'utente o il gruppo.

    ![Scegliere un utente o un gruppo](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. In **Select Users/Groups** (Seleziona utenti/gruppi) scegliere **BackupAdmin** > **Add** (Aggiungi). In **Users** (Utenti) per l'account utente viene usato il formato *dominio\nomeutente*. Per usare un dominio diverso, sceglierlo dall'elenco **Domain** (Domini). Fare clic su **OK** per aggiungere gli utenti selezionati alla finestra di dialogo **Add Permission** (Aggiungi autorizzazione).

    ![Aggiungere l'utente BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. In **Assigned Role** (Ruolo assegnato), nell'elenco selezionare **BackupAdminRole** > **OK**.

    ![Assegnare l'utente a un ruolo](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Il nuovo account utente e il ruolo associato vengono visualizzati nell'elenco nella scheda **Manage** (Gestisci) del pannello **Global Permissions** (Autorizzazioni globali).

## <a name="add-the-account-on-azure-backup-server"></a>Aggiungere l'account nel server di Backup di Azure

1. Aprire il server di Backup di Azure. Se l'icona non è presente sul desktop, aprire il Backup di Microsoft Azure dall'elenco delle app.

    ![Icona del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Nella console del server di Backup di Azure fare clic su **Gestione** >  **Server di produzione** > **Gestisci VMware**.

    ![Console del server di Backup di Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. Nella finestra di dialogo **Gestisci credenziali** fare clic su **Aggiungi**.

    ![Finestra di dialogo Gestisci credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. In **Aggiungi credenziale**immettere un nome e una descrizione per la nuova credenziale e specificare il nome utente e la password definiti nel server VMware. Per identificare le credenziali, in questa procedura viene usato il nome *Contoso Vcenter credential*. Se il server VMware e il server di Backup di Azure non sono nello stesso dominio, specificare il dominio nel nome utente.

    ![Finestra di dialogo Aggiungi credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Fare clic su **Aggiungi** per aggiungere le nuove credenziali.

    ![Finestra di dialogo Gestisci credenziali del server di Backup di Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Aggiungere il server vCenter

Aggiungere il server vCenter al server di Backup di Azure.

1. Nella console del server di Backup di Azure fare clic su **Gestione** > **Server di produzione** > **Aggiungi**.

    ![Aprire l'Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Nella pagina **Aggiunta guidata server di produzione** > **Selezionare il tipo di server di produzione** scegliere **Server VMware** e quindi fare clic su **Avanti**.

    ![Aggiunta guidata server di produzione](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. In **Selezione computer** **Nome del server/Indirizzo IP** specificare il nome di dominio completo o l'indirizzo IP del server VMware. Se tutti i server ESXi sono gestiti dallo stesso vCenter, specificare il nome vCenter. In caso contrario, aggiungere l'host ESXi.

    ![Specificare il server VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. In **Porta SSL** immettere la porta usata per comunicare con il server VMware. 443 è la porta predefinita, ma è possibile cambiarla se il server VMware è in ascolto su una porta diversa.

5. In **Specifica credenziale** selezionare le credenziali create in precedenza.

    ![Specifica credenziale](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Fare clic su **Aggiungi** per aggiungere il server VMware all'elenco dei server. Quindi fare clic su **Next**.

    ![Aggiungere il server VMware e le credenziali](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Nella pagina **Riepilogo** fare clic su **Aggiungi** per aggiungere il server VMware al server di Backup di Azure. Il nuovo server viene aggiunto immediatamente, non è necessario alcun agente nel server VMware.

    ![Aggiungere il server VMware al server di Backup di Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Verificare le impostazioni nella pagina **Fine**.

   ![Pagina Fine](./media/backup-azure-backup-server-vmware/summary-screen.png)

Se si hanno più host ESXi non gestiti dal server vCenter o si hanno più istanze del server vCenter, è necessario ripetere la procedura guidata per aggiungere i server.

## <a name="configure-a-protection-group"></a>Configurazione di un gruppo protezione dati

Aggiungere le macchine virtuali VMware per il backup. I gruppi protezione dati raccolgono più macchine virtuali e applicano le stesse impostazioni di backup e conservazione dei dati a tutte le macchine virtuali nel gruppo.

1. Nella console del server di Backup di Azure fare clic su **Protezione** > **Nuovo**.

    ![Aprire la procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Nella pagina iniziale della procedura guidata **Crea nuovo gruppo protezione dati** fare clic su **Avanti**.

    ![Finestra di dialogo della procedura guidata Crea nuovo gruppo protezione dati](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server** e quindi fare clic su **Avanti**. Verrà visualizzata la pagina **Selezione membri del gruppo**.

1. In **Seleziona membri del gruppo**selezionare le macchine virtuali (o cartelle VM) di cui si vuole eseguire il backup. Quindi fare clic su **Next**.

    - Quando si seleziona una cartella, le macchine virtuali o le cartelle al suo interno vengono selezionate per il backup. È possibile deselezionare le cartelle o le macchine virtuali da escludere dal backup.
1. Se una macchina virtuale o una cartella è già sottoposta a backup, non è possibile selezionarla. In questo modo si garantisce che non vengano creati punti di ripristino duplicati per una macchina virtuale.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Nella pagina **Seleziona metodo protezione dati** immettere un nome per il gruppo protezione dati e le impostazioni di protezione. Per eseguire il backup in Azure, impostare la protezione a breve termine su **Disco** e abilitare la protezione dati online. Quindi fare clic su **Next**.

    ![Seleziona metodo protezione dati](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. In **Specifica obiettivi a breve termine** specificare per quanto tempo si vogliono mantenere i dati sottoposti a backup su disco.
   - In **Intervallo conservazione** specificare il numero di giorni per cui si vogliono mantenere i punti di recupero del disco.
   - In **Frequenza di sincronizzazione** specificare quanto spesso acquisire i punti di recupero del disco.
       - Se non si vuole impostare un intervallo di backup, è possibile controllare **immediatamente prima di un punto di ripristino** in modo che un backup venga eseguito immediatamente prima di ogni punto di ripristino pianificato.
       - I backup a breve termine sono backup completi e non backup incrementali.
       - Fare clic su **Modifica** per modificare gli orari o le date di esecuzione dei backup a breve termine.

         ![Specifica obiettivi a breve termine](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. In **Verifica allocazione dischi** esaminare lo spazio su disco fornito per i backup delle macchine virtuali.

   - Le allocazioni di dischi consigliate sono basate sull'intervallo di conservazione specificato, il tipo di carico di lavoro e le dimensioni dei dati protetti. Apportare eventuali modifiche necessarie e quindi fare clic su **Avanti**.
   - **Dimensione dati:** la dimensione dei dati nel gruppo protezione dati.
   - **Spazio su disco:** la quantità di spazio su disco consigliata per il gruppo protezione dati. Se si vuole modificare questa impostazione, è consigliabile allocare uno spazio totale leggermente superiore rispetto alla quantità stimata per la crescita di ogni origine dati.
   - **Condividi percorso dati:** se si attiva la condivisione del percorso dati, è possibile eseguire il mapping di più origini dati nella protezione a un'unica replica e a un unico volume del punto di recupero. La condivisione del percorso non è supportata per tutti i carichi di lavoro.
   - **Aumento automatico:** Se si attiva questa impostazione, se i dati nel gruppo protetto superano l'allocazione iniziale, server di Backup di Azure tenta di aumentare le dimensioni del disco del 25%.
   - **Dettagli pool di archiviazione:** visualizza lo stato del pool di archiviazione, incluse dimensione totale e dimensione rimanente dei dischi.

    ![Rivedere l'allocazione dei dischi](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Nella pagina **Scelta del metodo per la creazione della replica** specificare come si vuole eseguire il backup iniziale e quindi fare clic su **Avanti**.
   - Il valore predefinito è **Automaticamente tramite rete** e **Ora**.
   - Se si usa il valore predefinito, è consigliabile specificare un'ora non di punta. Scegliere **In seguito** e specificare un giorno e un'ora.
   - Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline tramite supporti di memorizzazione rimovibili.

    ![Scelta del metodo per la creazione della replica](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. In **Opzioni di verifica coerenza** selezionare come e quando automatizzare le verifiche della coerenza. Quindi fare clic su **Next**.
      - È possibile eseguire verifiche della coerenza quando i dati di replica diventano incoerenti o in base a una pianificazione impostata.
      - Per non configurare la verifica automatica della coerenza, è possibile eseguire una verifica manuale. A questo scopo, fare clic con il pulsante destro del mouse sul gruppo protezione dati > **Esegui verifica coerenza**.

1. Nella pagina **Specifica i dati da proteggere online** selezionare le macchine virtuali o cartelle di macchine virtuali di cui eseguire il backup. È possibile selezionare i membri singolarmente o fare clic su **Seleziona tutto** per scegliere tutti i membri. Quindi fare clic su **Next**.

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Nella pagina **Specificare la pianificazione dei backup online** specificare la frequenza con cui si vuole eseguire il backup dei dati dall'archiviazione locale ad Azure.

    - I punti di recupero cloud per i dati verranno generati in base alla pianificazione. Quindi fare clic su **Next**.
    - Dopo la generazione, il punto di recupero viene trasferito nell'insieme di credenziali di Servizi di ripristino di Azure.

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Nella pagina **Specificare i criteri di conservazione online** indicare quanto a lungo si vogliono conservare i punti di recupero creati dai backup giornalieri, settimanali, mensili e annuali in Azure. Quindi fare clic su **Avanti**.

    - Non esiste un limite di tempo per la conservazione dei dati in Azure.
    - L'unico limite è un massimo di 9999 punti di recupero per ogni istanza protetta. In questo esempio l'istanza protetta è il server VMware.

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Verificare le impostazioni nella pagina **Riepilogo** e quindi fare clic su **Crea gruppo**.

    ![Riepilogo delle impostazioni e dei membri del gruppo protezione dati](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

Per eseguire il backup di vSphere 6,7, seguire questa procedura:

- Abilitare TLS 1,2 nel server DPM
  >[!Note]
  >VMWare 6,7 in poi ha abilitato TLS come protocollo di comunicazione.

- Impostare le chiavi del registro di sistema come segue:

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

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi durante la configurazione dei backup, vedere [Risolvere i problemi del server di Backup di Azure](./backup-azure-mabs-troubleshoot.md).
