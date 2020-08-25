---
title: Backup non in linea tramite Azure Data Box
description: Informazioni su come usare Azure Data Box per inizializzare i dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di servizi di ripristino.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 6ad97ee60c3c7debea72357cf7fc8d483a3c1d46
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761560"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup offline di Backup di Azure tramite Azure Data Box

È possibile usare [Azure Data Box](../databox/data-box-overview.md) per eseguire il seeding dei backup iniziali di servizi di ripristino di Microsoft Azure (Mars) in modalità offline (senza usare la rete) in un insieme di credenziali di servizi di ripristino. Questo processo consente di risparmiare tempo e larghezza di banda di rete che altrimenti verrebbero usati per spostare grandi quantità di dati di backup online tramite una rete ad alta latenza. Questa funzionalità avanzata è attualmente in fase di anteprima. Il backup offline basato su Azure Data Box offre due vantaggi diversi rispetto al [backup offline basato sul servizio Importazione/Esportazione di Azure](./backup-azure-backup-import-export.md):

- Non è necessario acquistare dischi e connettori compatibili con Azure. Azure Data Box include i dischi associati allo [SKU Data Box](https://azure.microsoft.com/services/databox/data/) selezionato.
- Backup di Azure (agente MARS) può scrivere direttamente i dati di backup negli SKU supportati di Azure Data Box. Questa funzionalità elimina la necessità di effettuare il provisioning di una posizione di staging per i dati di backup iniziali. Non sono neppure necessarie utilità per formattare e copiare i dati sui dischi.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box con l'agente MARS

Questo articolo illustra come usare Azure Data Box per inizializzare i dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di servizi di ripristino.

## <a name="supported-platforms"></a>Piattaforme supportate

Il processo per eseguire il seeding dei dati dall'agente MARS usando Azure Data Box è supportato negli SKU di Windows seguenti.

| **Sistema operativo**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Workstation**                        |                                                              |
| Windows 10 a 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 a 64 bit                    | Enterprise, Pro                                             |
| Windows 8 a 64 bit                      | Enterprise, Pro                                             |
| Windows 7 a 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 a 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 a 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 a 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 a 64 bit            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 a 64 bit    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 a 64 bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 a 64 bit    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 a 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU Data Box supportati

| Dimensioni dei dati di backup (post-compressione da Marte) * per server | SKU Azure Data Box supportati                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Disco Azure Data Box](../databox/data-box-disk-overview.md) |
| >7,2 TB e <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* I normali tassi di compressione variano tra il 10% e il 20%. <br>
* * Se si prevede di avere più di 80 TB di dati di backup iniziali per un singolo server MARS, contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>I dati di backup iniziali da un singolo server devono essere contenuti in una singola istanza di Azure Data Box o Azure Data Box disco e non possono essere condivisi tra più dispositivi dello stesso o di SKU diversi. Un dispositivo Azure Data Box può tuttavia contenere backup iniziali da più server.

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarie

- Il processo richiede una sottoscrizione di Azure.
- Il processo richiede che l'utente designato per l'esecuzione dei criteri di backup non in linea sia un proprietario della sottoscrizione di Azure.
- Il processo Data Box e l'insieme di credenziali di servizi di ripristino (in cui è necessario eseguire il seeding dei dati) devono trovarsi nelle stesse sottoscrizioni.
- È consigliabile che l'account di archiviazione di destinazione associato al processo di Azure Data Box e l'insieme di credenziali di servizi di ripristino si trovino nella stessa area. Tuttavia, questo non è necessario.

### <a name="get-azure-powershell-370"></a>Ottenere Azure PowerShell 3.7.0

*Questo è il prerequisito più importante per il processo*. Prima di installare Azure PowerShell versione 3.7.0, eseguire i controlli seguenti.

#### <a name="step-1-check-the-powershell-version"></a>Passaggio 1: controllare la versione di PowerShell

1. Aprire Windows PowerShell ed eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Se l'output visualizza una versione superiore a 3.7.0, eseguire "Step 2." In caso contrario, andare al passaggio 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Passaggio 2: disinstallare la versione di PowerShell

Disinstallare la versione corrente di PowerShell.

1. Rimuovere i moduli dipendenti eseguendo il comando seguente in PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Per garantire la corretta eliminazione di tutti i moduli dipendenti, eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passaggio 3: installare la versione di PowerShell 3.7.0

Dopo aver verificato che non siano presenti moduli AzureRM, installare la versione 3.7.0 usando uno dei metodi seguenti:

- Da GitHub usare [questo collegamento](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

In alternativa, è possibile:

- Eseguire il comando seguente nella finestra di PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell potrebbe essere stato installato anche usando un file MSI. Per rimuoverlo, disinstallarlo usando l'opzione **Disinstalla programmi** nel pannello di controllo.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Il processo di backup offline che usa MARS e Azure Data Box richiede che i dispositivi Data Box siano in uno stato recapitato prima di attivare il backup offline usando l'agente MARS. Per ordinare lo SKU più adatto ai propri requisiti, vedere [dimensioni dei dati di backup e sku data box supportati](#backup-data-size-and-supported-data-box-skus). Seguire i passaggi in [esercitazione: ordinare un disco Azure Data Box](../databox/data-box-disk-deploy-ordered.md) per ordinare e ricevere i dispositivi data box.

> [!IMPORTANT]
> Non selezionare *BlobStorage* per il **tipo di account**. Per l'agente MARS è necessario un account che supporti i BLOB di pagine, che non è supportato quando si seleziona *BlobStorage* . Selezionare **storage V2 (utilizzo generico v2)** come **tipo di account** quando si crea l'account di archiviazione di destinazione per il processo di Azure Data Box.

![Scegliere il tipo di account nei dettagli dell'istanza](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installare e configurare l'agente MARS

1. Assicurarsi di disinstallare le installazioni precedenti dell'agente MARS.
1. Scaricare la versione più recente dell'agente MARS dal [sito Web](https://aka.ms/azurebackup_agent).
1. Eseguire *MARSAgentInstaller.exe*ed eseguire *solo* i passaggi per [installare e registrare l'agente](./install-mars-agent.md#install-and-register-the-agent) nell'insieme di credenziali dei servizi di ripristino in cui si desidera archiviare i backup.

   > [!NOTE]
   > L'insieme di credenziali di servizi di ripristino deve trovarsi nella stessa sottoscrizione del processo di Azure Data Box.

   Dopo che l'agente è stato registrato nell'insieme di credenziali di servizi di ripristino, seguire i passaggi nelle sezioni successive.

## <a name="set-up-azure-data-box-devices"></a>Configurare dispositivi Azure Data Box

A seconda del Azure Data Box SKU ordinato, eseguire i passaggi descritti nelle sezioni appropriate che seguono. I passaggi illustrano come configurare e preparare i dispositivi Data Box per l'agente MARS per identificare e trasferire i dati di backup iniziali.

### <a name="set-up-azure-data-box-disks"></a>Configurare Azure Data Box dischi

Se è stato ordinato uno o più dischi Azure Data Box (fino a 8 TB ciascuno), seguire i passaggi indicati qui per [decomprimere, connettere e sbloccare il disco data box](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>È possibile che il server con l'agente MARS non disponga di una porta USB. In tal caso, è possibile connettere il disco Azure Data Box a un altro server o client ed esporre la radice del dispositivo come una condivisione di rete.

### <a name="set-up-azure-data-box"></a>Configurare Azure Data Box

Se è stata ordinata un'istanza di Azure Data Box (fino a 100 TB), seguire la procedura descritta qui [per configurare l'istanza di data box](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montare l'istanza di Azure Data Box come sistema locale

L'agente MARS opera nel contesto del sistema locale, pertanto è necessario fornire lo stesso livello di privilegio da fornire al percorso di montaggio in cui è connessa l'istanza di Azure Data Box.

Per assicurarsi che sia possibile montare il dispositivo Data Box come sistema locale usando il protocollo NFS:

1. Abilitare il client per la funzionalità NFS in Windows Server in cui è installato l'agente MARS. Specificare il file WIM di origine alternativo *: D: \sources\install.wim: 4*.
1. Scaricare PsExec dalla pagina [Sysinternals](/sysinternals/downloads/psexec) al server in cui è installato l'agente Mars.
1. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory che contiene *PSExec.exe* come directory corrente.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   La finestra di comando visualizzata a causa del comando precedente è nel contesto del sistema locale. Usare questa finestra di comando per eseguire i passaggi per montare la condivisione BLOB di pagine di Azure come unità di rete in Windows Server.
1. Seguire i passaggi in [connettersi a data box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) per connettere il server con l'agente Mars al dispositivo data box tramite NFS. Eseguire il comando seguente nel prompt dei comandi di sistema locale per montare la condivisione BLOB di pagine di Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Dopo aver montato la condivisione, verificare se è possibile accedere a X: dal server. Se possibile, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferire i dati di backup iniziali a dispositivi Azure Data Box

1. Aprire l'applicazione **backup di Microsoft Azure** nel server.
1. Nel riquadro **azioni** selezionare **Pianifica backup**.

    ![Selezione pianificazione backup](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Seguire i passaggi della **pianificazione guidata backup**.

1. Aggiungere elementi selezionando il pulsante **Aggiungi elementi** . Consente di limitare la dimensione totale degli elementi entro i [limiti di dimensione supportati dalla Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) ordinata e ricevuta.

    ![Aggiungi elementi al backup](./media/offline-backup-azure-data-box/add-items.png)

1. Selezionare la pianificazione di backup e i criteri di conservazione appropriati per **file e cartelle** e **stato del sistema**. Lo stato del sistema è applicabile solo per i server Windows e non per i client Windows.
1. Nella pagina **scegliere il tipo di backup iniziale (file e cartelle)** della procedura guidata, selezionare l'opzione **trasferimento usando Microsoft Azure Data Box dischi** e quindi fare clic su **Avanti**.

    ![Scegliere il tipo di backup iniziale](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Accedere ad Azure quando richiesto usando le credenziali utente con accesso proprietario nella sottoscrizione di Azure. Una volta completata questa operazione, verrà visualizzata una pagina simile alla seguente.

    ![Creazione di risorse e applicazione delle autorizzazioni necessarie](./media/offline-backup-azure-data-box/creating-resources.png)

   L'agente MARS recupera quindi i processi di Data Box nella sottoscrizione che si trovano nello stato recapitato.

    ![Recuperare Data Box processi per l'ID sottoscrizione](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selezionare l'ordine di Data Box corretto per cui è stato decompresso, connesso e sbloccato il disco Data Box. Selezionare **Avanti**.

    ![Selezione ordini Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selezionare **rileva dispositivo** nella pagina di **rilevamento del dispositivo data box** . Questa azione consente di eseguire l'analisi dell'agente MARS per i dischi Azure Data Box collegati localmente e di rilevarli.

    ![Rilevamento del dispositivo Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se è stata connessa l'istanza di Azure Data Box come una condivisione di rete (a causa dell'indisponibilità di porte USB o perché è stato ordinato e montato il dispositivo Data Box TB), 100 il rilevamento non riesce. Si ha la possibilità di immettere il percorso di rete per il dispositivo Data Box.

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Specificare il percorso di rete della directory radice del disco Azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob*.
    >
    >![Directory radice del disco Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ad esempio, se il percorso del disco è `\\mydomain\myserver\disk1\` e *disk1* contiene una directory denominata *PageBlob*, il percorso immesso nella pagina della procedura guidata dell'agente Mars è `\\mydomain\myserver\disk1\` .
    >
    >Se si [configura un dispositivo Azure Data Box 100 TB](#set-up-azure-data-box-devices), immettere `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` come percorso di rete per il dispositivo.

1. Selezionare **Avanti**e fare clic su **fine** nella pagina successiva per salvare i criteri di backup e conservazione con la configurazione del backup offline usando Azure Data Box.

   La pagina seguente conferma che il criterio è stato salvato correttamente.

    ![Salvataggio dei criteri completato](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selezionare **Chiudi** nella pagina precedente.

1. Selezionare **Esegui backup ora** nel riquadro **Azioni** della console dell'agente Mars. Selezionare **backup** nella pagina della procedura guidata.

    ![Procedura guidata Esegui backup](./media/offline-backup-azure-data-box/backup-now.png)

L'agente MARS avvia il backup dei dati selezionati nel dispositivo Azure Data Box. Questo processo può richiedere da diverse ore ad alcuni giorni. La quantità di tempo dipende dal numero di file e dalla velocità di connessione tra il server con l'agente MARS e il disco Azure Data Box.

Al termine del backup dei dati, verrà visualizzata una pagina sull'agente MARS simile a questa.

![Stato di avanzamento del backup](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passaggi da eseguire dopo il backup

In questa sezione vengono illustrati i passaggi da eseguire dopo il completamento del backup dei dati nel Azure Data Box Disk.

- Seguire la procedura descritta in questo articolo per [distribuire il disco Azure Data Box ad Azure](../databox/data-box-disk-deploy-picked-up.md). Se è stato usato un dispositivo Azure Data Box 100 TB, seguire questa procedura per [inviare il dispositivo Azure Data Box ad Azure](../databox/data-box-deploy-picked-up.md).

- [Monitorare il processo Data Box](../databox/data-box-disk-deploy-upload-verify.md) nel portale di Azure. Al termine del processo di Azure Data Box, l'agente MARS sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali di servizi di ripristino al momento del backup pianificato successivo. Il processo di backup viene quindi contrassegnato come *processo completato* se un punto di ripristino viene creato correttamente.

    >[!NOTE]
    >L'agente MARS attiva i backup negli orari pianificati durante la creazione dei criteri. Questi processi contrassegnano "in attesa del completamento Azure Data Box processo" fino al termine del processo.

- Dopo che l'agente MARS ha creato un punto di ripristino corrispondente al backup iniziale, è possibile eliminare l'account di archiviazione o il contenuto specifico associato al processo di Azure Data Box.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'agente di Servizi di ripristino di Microsoft Azure (MARS) crea automaticamente un'applicazione Azure Active Directory (Azure AD) nel tenant. Questa applicazione richiede un certificato per l'autenticazione creato e caricato quando si configura un criterio di seeding offline. Si usa Azure PowerShell per creare e caricare il certificato nell'applicazione Azure AD.

### <a name="problem"></a>Problema

Quando si configura il backup offline, potrebbe verificarsi un problema a causa di un bug nel cmdlet Azure PowerShell. Potrebbe non essere possibile aggiungere più certificati alla stessa applicazione Azure AD creata dall'agente MAB. Questo problema influirà sull'utente se è stato configurato un criterio di seeding offline per lo stesso server o per un server diverso.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verificare se il problema è causato da questa specifica causa radice

Per verificare se il problema è identico a quello descritto in precedenza, eseguire una delle operazioni seguenti.

#### <a name="step-1-of-verification"></a>Passaggio 1 della verifica

Verificare se il messaggio di errore seguente viene visualizzato nella console MAB quando è stato configurato il backup offline.

![Non è possibile creare i criteri di backup offline per l'account Azure corrente](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Passaggio 2 della verifica

1. Aprire la cartella **Temp** nel percorso di installazione. Il percorso predefinito della cartella temporanea è *C:\Programmi\Microsoft Azure Recovery Services Agent\Temp*. Cercare il file *CBUICurr* e aprire il file.

1. Nel file *CBUICurr* scorrere fino all'ultima riga e verificare se il problema è identico a quello di questo messaggio di errore: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Soluzione alternativa

Per risolvere il problema, eseguire la procedura seguente e riprovare la configurazione dei criteri.

#### <a name="step-1-of-workaround"></a>Passaggio 1 della soluzione alternativa

Accedere a PowerShell che viene visualizzato nell'interfaccia utente di MAB usando un account diverso con accesso amministrativo alla sottoscrizione in cui verrà creato il processo di importazione o esportazione.

#### <a name="step-2-of-workaround"></a>Passaggio 2 della soluzione alternativa

Se nessun altro server dispone di un seeding offline configurato e nessun altro server dipende dall' `AzureOfflineBackup_<Azure User Id>` applicazione, eliminare questa applicazione. Selezionare **portale di Azure**  >  **Azure Active Directory**  >  **registrazioni app**Azure Active Directory.

>[!NOTE]
> Verificare se nell' `AzureOfflineBackup_<Azure User Id>` applicazione non sono configurati altri seeding offline e anche se nessun altro server dipende da questa applicazione. Passare a **Impostazioni**  >  **chiavi** nella sezione **chiavi pubbliche** . Non devono essere aggiunte altre chiavi pubbliche. Per informazioni di riferimento, vedere la schermata seguente.
>
>![Chiavi pubbliche](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Passaggio 3

Dal server che si sta tentando di configurare per il backup offline, eseguire le azioni seguenti.

1. Passare alla scheda **Gestisci applicazione certificato computer**  >  **personale** e cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Selezionare il certificato, fare clic con il pulsante destro del mouse su **tutte le attività**e selezionare **Esporta** senza una chiave privata nel formato CER.

3. Passare all'applicazione di backup offline di Azure citata nel passaggio 2. Selezionare **Impostazioni**  >  **chiavi**  >  **carica chiave pubblica**. Caricare il certificato esportato nel passaggio precedente.

    ![Carica chiave pubblica](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Nel server aprire il registro di sistema immettendo **Regedit** nella finestra Esegui.

5. Accedere al computer del registro di sistema *\ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Fare clic con il pulsante destro del mouse su **CloudBackupProvider**e aggiungere un nuovo valore stringa con il nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una di queste azioni:
    >
    >- In PowerShell connesso ad Azure eseguire il comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`.
    > - Passare al percorso del registro `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` di sistema con il nome *CurrentUserId*.

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel passaggio 2. Selezionare **OK**.

7. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato. Selezionare la scheda **Dettagli** e scorrere verso il basso fino a visualizzare il campo identificazione personale. Selezionare **identificazione personale**e copiare il valore.

    ![Campo identificazione personale del certificato](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Domande

Per eventuali domande o chiarimenti sui problemi riscontrati, contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
