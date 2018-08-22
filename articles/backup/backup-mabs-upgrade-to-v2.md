---
title: Installare il server di Backup di Azure v2
description: Il server di Backup di Azure v2 offre funzionalità avanzate di backup per la protezione di VM, file e cartelle, carichi di lavoro e altro ancora. Informazioni su come installare o eseguire l'aggiornamento del server di Backup di Azure v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: a458a46f3775a593f369d5acb967fc90d61efde8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628342"
---
# <a name="install-azure-backup-server-v2"></a>Installare il server di Backup di Azure v2

Il server di Backup di Azure consente di proteggere macchine virtuali (VM), carichi di lavoro, file e cartelle, e altro ancora. Il server di Backup di Azure v2 si basa sul server di Backup di Azure v1 e offre nuove funzionalità che non sono disponibili nella v1. Per un confronto tra le funzionalità delle versioni v1 e v2, vedere [Matrice di protezione del server di Backup di Azure](backup-mabs-protection-matrix.md). 

Le funzionalità aggiuntive nel server di backup v2 rappresentano un aggiornamento dal server di backup v1. Tuttavia, il server di backup v1 non è un prerequisito per l'installazione del server di backup v2. Se si desidera eseguire l'aggiornamento dal server di backup v1 a al server di backup v2, installare il server di backup v2 nel server di protezione del server di backup. Le impostazioni del server di backup esistenti rimangono invariate.

È possibile installare il server di Backup v2 in Windows Server 2016 o in Windows Server 2012 R2. Per sfruttare i vantaggi delle nuove funzionalità, come Modern Backup Storage di System Center 2016 Data Protection Manager, è necessario installare il server di backup v2 in Windows Server 2016. Prima di eseguire l'aggiornamento a di installare il server di backup v2, leggere i [prerequisiti di installazione](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Il server di Backup di Azure ha la stessa base di codice di System Center Data Protection Manager. Il server di Backup v1 corrisponde a Data Protection Manager 2012 R2. Il server di Backup v2 corrisponde a Data Protection Manager 2016. Questo articolo fa riferimento in alcuni casi alla documentazione di Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Aggiornare il server di Backup di Azure alla v2
Per eseguire l'aggiornamento dal server di backup v1 al server di backup v2, assicurarsi che l'installazione presenti gli aggiornamenti necessari:

- [Aggiornare gli agenti protezione](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) nei server protetti.
- Aggiornare Windows Server 2012 R2 a Windows Server 2016.
- Aggiornare Azure Backup Server Remote Administrator in tutti i server di produzione.
- Verificare che i backup siano configurati per continuare senza dover riavviare il server di produzione.


### <a name="upgrade-steps-for-backup-server-v2"></a>Passaggi di aggiornamento per il server di backup v2

1. Nell'area download [scaricare il programma di installazione dell'aggiornamento](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Dopo aver estratto l'installazione guidata, assicurarsi che sia selezionata l'opzione **Esegui setup.exe**, quindi fare clic **Fine**.

  ![Programma di installazione - Eseguire il programma di installazione](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. Nella procedura guidata del server di Backup di Microsoft Azure, sotto **Installare** selezionare **Server di Backup di Microsoft Azure**.

  ![Programma di installazione - Selezionare gli elementi da installare](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Nella pagina **Benvenuti** esaminare gli avvisi e quindi selezionare **Avanti**.

  ![Programma di installazione - Pagina di benvenuto](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. L'installazione guidata esegue i controlli dei prerequisiti per verificare che sia possibile aggiornare l'ambiente. Nella pagina **Controlli dei prerequisiti** selezionare **Controlla**.

  ![Programma di installazione - Pagina del controllo dei prerequisiti](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. L'ambiente deve superare i controlli dei prerequisiti. Se l'ambiente non supera i controlli, prendere nota dei problemi e correggerli. Selezionare quindi **Controlla di nuovo**. Dopo avere superato i controlli dei prerequisiti, selezionare **Avanti**.

  ![Programma di installazione - Pulsante per ripetere il controllo](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Nella pagina **Impostazioni SQL** selezionare l'opzione appropriata per l'installazione di SQL, quindi selezionare **Controlla e installa**.

  ![Programma di installazione - Pagina delle impostazioni di SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  I controlli potrebbero richiedere alcuni minuti. Al termine dell'operazione selezionare **Avanti**.

  ![Programma di installazione - Controllo delle impostazioni di SQL e pulsante di installazione](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. Nella pagina **Impostazioni di installazione** apportare le modifiche necessarie al percorso in cui è installato il server di Backup o al percorso dei file temporanei. Selezionare **Avanti**.

  ![Programma di installazione - Pagina delle impostazioni di installazione](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Per completare l'installazione guidata selezionare **Fine**.

  ![Programma di installazione - Fine](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Aggiungere spazio di archiviazione per Modern Backup Storage

Per migliorare l'efficienza dell'archiviazione di backup, il server di backup v2 aggiunge il supporto per i volumi. Il server di Backup v1 e il server di Backup v2 supportano entrambi i dischi.

### <a name="add-volumes-and-disks"></a>Aggiungere volumi e dischi

Se si esegue il server di backup v2 in Windows Server 2016, è possibile usare i volumi per archiviare i dati di backup. I volumi consentono di risparmiare spazio di archiviazione e offrono backup più veloci. Poiché i volumi sono una nuova funzione del server di backup, è necessario aggiungerli. 

Quando si aggiunge un volume al server di backup, è possibile assegnare al volume un nome descrittivo. Selezionare la colonna **Nome descrittivo** del volume cui si vuole assegnare un nome. Il nome può essere modificato in un secondo momento, se necessario. È possibile usare PowerShell per aggiungere o modificare i nomi descrittivi per i volumi.

Per aggiungere un volume nella Console di amministrazione:

1. Nella Console di amministrazione del server di Backup di Azure, selezionare **Gestione** > **Spazio di archiviazione su disco** > **Aggiungi**.

  ![Aprire la procedura guidata Aggiungi spazio di archiviazione su disco](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  Viene avviata la procedura guidata Aggiungi spazio di archiviazione su disco.

2. Nella pagina **Aggiungi spazio di archiviazione su disco**, nella casella **Volumi disponibili**, selezionare un volume e quindi selezionare **Aggiungi**.

3. Nella casella **Volumi selezionati** immettere un nome descrittivo per il volume e quindi selezionare **OK**.

  ![Procedura guidata Aggiungi spazio di archiviazione su disco - Aggiungi volume](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Se si desidera aggiungere un disco, il disco deve appartenere a un gruppo di protezione dati che abbia spazio di archiviazione legacy. I dischi possono essere usati solo per questi gruppi protezione dati. Se il server di backup non dispone di origini con protezione legacy, il disco non è elencato.

  Per altre informazioni sull'aggiunta di dischi, vedere [Aggiungere dischi per aumentare lo spazio di archiviazione legacy](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). È possibile assegnare un nome descrittivo al disco.


### <a name="assign-workloads-to-volumes"></a>Assegnare carichi di lavoro ai volumi

Nel server di backup specificare quali carichi di lavoro sono assegnati ai volumi. Ad esempio, è possibile configurare volumi costosi che supportano un numero elevato di operazioni di input/output al secondo (IOPS) per archiviare solo i carichi di lavoro che richiedono backup frequenti di volumi elevati. Un esempio è SQL Server con i log delle transazioni.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Per aggiornare le proprietà di un volume del pool di archiviazione nel server di Backup, usare il cmdlet di PowerShell **Update-DPMDiskStorage**.

Sintassi:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
```

Tutte le modifiche apportate tramite PowerShell si riflettono nell'interfaccia utente.


## <a name="protect-data-sources"></a>Proteggere le origini dati
Per iniziare a proteggere le origini dati, creare un gruppo protezione dati. I passaggi seguenti evidenziano le modifiche o aggiunte alla procedura guidata Crea nuovo gruppo protezione dati.

Per creare un gruppo di protezione:

1. Nella Console di amministrazione del server di backup selezionare **Protezione**.

2. Nella barra multifunzione dello strumento selezionare **Nuovo**.

  Verrà visualizzata la procedura guidata Crea nuovo gruppo protezione dati.

  ![Procedura guidata Crea nuovo gruppo protezione dati](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Nella pagina di **benvenuto** selezionare **Avanti**.

4. Nella pagina **Seleziona tipo di gruppo di protezione dati** selezionare il tipo di gruppo protezione dati che si desidera creare e quindi selezionare **Avanti**.

  ![Pagina Seleziona tipo di gruppo di protezione dati](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Nel riquadro **Membri disponibili** nella pagina **Selezione membri del gruppo** sono elencati i membri con agenti protezione. Per questo esempio, selezionare i volumi D:\ ed E:\, quindi aggiungerli in **Membri selezionati**. Selezionare **Avanti**.

  ![Pagina Seleziona membri del gruppo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Nella pagina **Seleziona metodo protezione dati** digitare il nome del **Gruppo protezione dati**, selezionare il metodo di protezione e fare clic su **Avanti**. Se si desidera una protezione a breve termine, selezionare il metodo di backup **Disco**.

  ![Pagina Seleziona metodo protezione dati](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Nella pagina **Specifica obiettivi a breve termine** selezionare i dettagli per **Intervallo di conservazione** e **Frequenza di sincronizzazione**. Quindi selezionare **Avanti**. Facoltativamente, per modificare la pianificazione di quando vengono eseguiti i punti di recupero, selezionare **Modifica**.

  ![Pagina Specifica obiettivi a breve termine](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Nella pagina **Verificare l'allocazione dello spazio di archiviazione su disco** esaminare i dettagli sulle origini dati selezionate, tra cui dimensioni dell'origine dati, valori per lo spazio di cui effettuare il provisioning e volume di archiviazione di destinazione.

  ![Pagina di verifica allocazione dell'archiviazione su disco](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  I volumi di archiviazione sono basati sull'allocazione dei volume del carico di lavoro (configurata tramite PowerShell) e sullo spazio di archiviazione disponibile. È possibile modificare i volumi di archiviazione selezionando altri volumi nel menu a discesa. Se si modifica il valore di **Archiviazione di destinazione**, il valore di **Spazio di archiviazione su disco disponibile** cambia dinamicamente in modo da riflettere i valori di **Spazio disponibile** e **Underprovisioned Space** (Spazio con provisioning insufficiente).

  Se le origini dati crescono come pianificato, il valore della colonna **Underprovisioned Space** (Spazio con provisioning insufficiente) in **Spazio di archiviazione su disco disponibile** riflette la quantità di spazio di archiviazione aggiuntivo necessaria. Usare questo valore per pianificare le esigenze di archiviazione in modo che non si verifichino problemi di backup. Se il valore è zero, non esistono problemi potenziali con l'archiviazione in un futuro prevedibile. Se il valore è un numero diverso da zero, lo spazio di archiviazione allocato non è sufficiente (in base ai criteri di protezione e alle dimensioni dei dati dei membri protetti).

  ![Spazio di archiviazione su disco sottoallocato](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  Per completare la creazione del gruppo di protezione, completare la procedura guidata.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrare lo spazio di archiviazione legacy a Modern Backup Storage
Dopo l'aggiornamento o l'istallazione del server di Backup v2 e l'aggiornamento del sistema operativo a Windows Server 2016, aggiornare i gruppi protezione dati per l'uso di Modern Backup Storage. Per impostazione predefinita, i gruppi protezione dati non vengono modificati. Continuano a funzionare nel modo in cui erano stati configurati inizialmente. 

L'aggiornamento dei gruppi protezione dati per l'uso di Modern Backup Storage è facoltativo. Per aggiornare il gruppo protezione dati, arrestare la protezione di tutte le origini dati usando l'opzione di conservazione dei dati. Quindi aggiungere le origini dati a un nuovo gruppo protezione dati.

1. Nella Console amministrazione DPM di System Center 2016 selezionare la funzione **Protezione**. Nell'elenco **Membro del gruppo protezione dati** fare clic con il pulsante destro del mouse sul membro e quindi scegliere **Arresta protezione dati del membro**.

  ![Arresto della protezione del membro](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Nella finestra di dialogo **Rimuovi da gruppo** esaminare lo spazio su disco utilizzato e lo spazio disponibile per il pool di archiviazione. L'impostazione predefinita è lasciare i punti di recupero sul disco e consentire che scadano in base ai criteri di conservazione associati. Selezionare **OK**.

  Se si desidera restituire immediatamente lo spazio su disco utilizzato al pool di archiviazione disponibile, selezionare la casella di controllo **Elimina replica su disco** per eliminare i dati di backup (e i punti di recupero) associati a tale membro.

  ![Rimozione dalla finestra di dialogo del gruppo](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Creare un gruppo protezione dati che usa Modern Backup Storage. Includere le origini dati non protette.


## <a name="add-disks-to-increase-legacy-storage"></a>Aggiungere dischi per aumentare lo spazio di archiviazione legacy

Se si desidera usare lo spazio di archiviazione legacy con il server di backup, potrebbe essere necessario aggiungere dischi per aumentare lo spazio di archiviazione legacy. 

Per aggiungere spazio di archiviazione su disco:

1. Nella Console amministrazione DPM di System Center 2016 selezionare **Gestione** > **Spazio di archiviazione su disco** > **Aggiungi**.

  ![Finestra di dialogo Aggiungi spazio di archiviazione su disco](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. Nella finestra di dialogo **Aggiungi spazio di archiviazione su disco** selezionare **Aggiungi dischi**.

3. Nell'elenco dei dischi disponibili selezionare i dischi da aggiungere. Selezionare **Aggiungi** e quindi **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Aggiornare l'agente protezione Data Protection Manager

Il server di backup usa l'agente protezione System Center Data Protection Manager per gli aggiornamenti. Se si aggiorna un agente protezione che non è connesso alla rete, non è possibile usare la Console di amministrazione DPM per completare l'aggiornamento di un agente connesso. È necessario aggiornare l'agente protezione in un ambiente di dominio non attivo. Fino a quando il computer client è connesso alla rete, la Console di amministrazione di Data Protection Manager mostra che l'aggiornamento dell'agente protezione è in sospeso.

Le sezioni seguenti descrivono come aggiornare agenti protezione per i computer client connessi e per i computer client non connessi.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Aggiornare un agente protezione per un computer client connesso

1. Nella Console di amministrazione del server di Backup selezionare **Gestione** > **Agenti**.

2. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.

  > [!NOTE]
  > La colonna **Aggiornamenti agente** indica quando un aggiornamento dell'agente protezione è disponibile per ogni computer protetto. Nel riquadro **Azioni** l'azione **Aggiornamento** è disponibile solo quando è selezionato un computer protetto e sono disponibili aggiornamenti.

3. Per installare gli agenti protezione aggiornati nei computer selezionati, nel riquadro **Azioni** selezionare **Aggiornamento**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Aggiornare un agente protezione in un computer client non connesso

1. Nella Console di amministrazione del server di Backup selezionare **Gestione** > **Agenti**.

2. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.

  > [!NOTE]
  > La colonna **Aggiornamenti agente** indica quando un aggiornamento dell'agente protezione è disponibile per ogni computer protetto. Nel riquadro **Azioni** l'azione **Aggiornamento** non è disponibile quando è selezionato un computer protetto, a meno che non siano disponibili aggiornamenti.

3. Per installare gli agenti protezione aggiornati nei computer selezionati, selezionare **Aggiornamento**.

4. Per un computer client che non è connesso alla rete e fino a quando il computer non viene connesso alla rete, la colonna **Stato agente** indica lo stato **Aggiornamento in sospeso**.

  Quando un computer client è connesso alla rete, la colonna **Aggiornamenti agente** per il computer client indica lo stato **Aggiornamento in corso**.
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>Spostare i gruppi protezione dati legacy della versione precedente e sincronizzare la nuova versione con Azure

Quando il server di Backup di Azure e il sistema operativo sono entrambi aggiornati, è possibile proteggere nuove origini dati tramite Modern Backup Storage. Le origini dati già protette continuano a essere protette come avviene nel server di Backup di Azure (legacy). Tutti i nuovi gruppi protezione dati usano Modern Backup Storage.

Per eseguire la migrazione di origini dati dalla modalità di protezione legacy a Modern Backup Storage:

1.  Aggiungere i nuovi volumi al pool di archiviazione Data Protection Manager. È anche possibile assegnare un nome descrittivo e selezionare tag di origine dati.

2. Per ogni origine dati in modalità legacy, arrestare la protezione delle origini dati. Selezionare quindi **Mantieni dati protetti**.  In questo modo, sarà possibile ripristinare i punti di recupero precedenti dopo la migrazione.

3. Creare un nuovo gruppo protezione dati. Selezionare quindi le origini dati che si vuole archiviare usando il nuovo formato.

  Data Protection Manager archivia una copia di replica dallo spazio di archiviazione di backup legacy al volume di Modern Backup Storage in locale.
    > [!NOTE] 
    > La creazione della copia appare come processo di operazione post-ripristino.

  Tutti i nuovi punti di sincronizzazione e recupero vengono quindi archiviati in Modern Backup Storage.

  I punti di recupero precedenti vengono esclusi man mano che scadono. Con l'eliminazione dei punti di recupero precedenti, viene liberato spazio su disco.

  Quando tutti i volumi legacy saranno stati eliminati dallo spazio di archiviazione precedente, è possibile rimuovere il disco da Backup di Azure. È quindi possibile rimuovere il disco dal sistema.

4. Eseguire un backup del database di Data Protection Manager.

  > [!IMPORTANT]
  > - Il nuovo server dovrà avere lo stesso nome dell'istanza del server di Backup di Azure originale. Non è possibile modificare il nome della nuova istanza del server di Backup di Azure se si vuole usare il pool di archiviazione e il database di Data Protection Manager precedenti per conservare i punti di recupero.
  > - È necessario avere un backup del database di Data Protection Manager. Sarà necessario ripristinare il database.

5. Arrestare l'istanza del server di Backup di Azure originale o portare il server offline.

6. Reimpostare l'account del computer in Active Directory.

7. Installare Windows Server 2016 in un nuovo computer. Per il nome del server, usare lo stesso nome di computer dell'istanza del server di Backup di Azure originale.

8. Eseguire l'aggiunta al dominio.

9. Installare il server di Backup di Azure v2. Rimuovere i dischi del pool di archiviazione Data Protection Manager dal server precedente e importarli nel nuovo server.

10. Ripristinare il database di Data Protection Manager creato nel passaggio 4.

11. Collegare l'archiviazione dal server di backup originale a quello nuovo.

12. In SQL Server ripristinare il database di Data Protection Manager.

13. Dalla riga di comando di amministratore nel nuovo server usare `cd` per passare al percorso di installazione e alla cartella bin di Backup di Microsoft Azure.  

  Esempio:  
  C:\windows\system32>cd "c:\Programmi\Microsoft Azure Backup\DPM\DPM\bin\ in Backup di Azure

14. Eseguire `DPMSYNC -SYNC`.
  
  > [!NOTE]
  > Se sono stati aggiunti *nuovi* dischi al pool di archiviazione Data Protection Manager invece di spostare quelli precedenti, eseguire `DPMSYNC -Reallocatereplica`.

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Nuovi cmdlet di PowerShell nel server di Backup di Azure v2

Quando si installa il server di Backup di Azure v2, sono disponibili due nuovi cmdlet: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro:
- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
- [Usare Modern Backup Storage con il server di backup](backup-mabs-add-storage.md)

