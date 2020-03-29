---
title: Backup offline tramite Azure Data Box
description: Informazioni su come usare Azure Data Box per eseguire il seeding di dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di Servizi di ripristino.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672957"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup di Azure backup offline con Azure Data Box

È possibile usare [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) per eseguire il seeding dei backup iniziali di Microsoft Azure Recovery Services (MARS) di grandi dimensioni offline (senza usare la rete) in un insieme di credenziali di Servizi di ripristino. Questo processo consente di risparmiare tempo e larghezza di banda di rete che altrimenti verrebbero utilizzate spostando grandi quantità di dati di backup online su una rete ad alta latenza. Questo miglioramento è attualmente in anteprima. Il backup offline basato su Azure Data Box offre due vantaggi distinti rispetto [al backup offline basato sul servizio Importazione/Esportazione](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)di Azure:Offline backup based on Azure Data Box provides two distinct advantages over offline backup based on Azure Import/Export service :

* Non è necessario procurarsi i propri dischi e connettori compatibili con Azure.There's no need to procure your own Azure-compatible disks and connectors. Azure Data Box invie i dischi associati allo [SKU della casella di dati](https://azure.microsoft.com/services/databox/data/)selezionata.
* Backup di Azure (agente MARS) può scrivere direttamente i dati di backup negli SKU supportati di Azure Data Box.Azure Backup (MARS Agent) can directly write backup data on to the supported SKUs of Azure Data Box. Questa funzionalità elimina la necessità di eseguire il provisioning di un percorso di gestione temporanea per i dati di backup iniziali. Inoltre, non sono necessarie utilità per formattare e copiare i dati nei dischi.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box with the MARS Agent

Questo articolo illustra come usare Azure Data Box per eseguire il seeding dei dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali dei servizi di ripristino.

## <a name="supported-platforms"></a>Piattaforme supportate

The process to seed data from the MARS Agent by using Azure Data Box is supported on the following Windows SKUs.

| **Os**                                 | **Sku**                                                      |
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
| Windows Server 2008 SP2 a 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU della casella di dati supportati

| Dimensioni dei dati di backup (post-compressione da parte di MARS) per server | SKU di Azure Data Box supportato                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <DA 7,2 TB                                                    | [Disco Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB e <USD                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

I tassi di compressione tipici variano tra il 10% e il 20%. <br>
Se si prevede di disporre di più di 80 TB di [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)dati di backup iniziali per un singolo server MARS, contattare .

>[!IMPORTANT]
>I dati di backup iniziali da un singolo server devono essere contenuti in una singola istanza di Azure Data Box o in un disco di Azure Data Box e non possono essere condivisi tra più dispositivi dello stesso SKU o di un disco diverso. Tuttavia, un dispositivo Azure Data Box può contenere backup iniziali da più server.

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarieAzure subscription and required permissions

* Il processo richiede una sottoscrizione di Azure.The process requires an Azure subscription.
* Il processo richiede che l'utente designato per eseguire i criteri di backup offline sia proprietario della sottoscrizione di Azure.The process requires that the user designated to perform the offline backup policy is an owner of the Azure subscription.
* Il processo Data Box e l'insieme di credenziali di Servizi di ripristino (a cui devono essere esati i dati) devono essere presenti nelle stesse sottoscrizioni.
* È consigliabile che l'account di archiviazione di destinazione associato al processo di Azure Data Box e l'insieme di credenziali dei servizi di ripristino si trovino nella stessa area. Tuttavia, questo non è necessario.

### <a name="get-azure-powershell-370"></a>Ottenere Azure PowerShell 3.7.0Get Azure PowerShell 3.7.0

*Questo è il prerequisito più importante per il processo.* Prima di installare Azure PowerShell, versione 3.7.0, eseguire i controlli seguenti.

#### <a name="step-1-check-the-powershell-version"></a>Passaggio 1: Controllare la versione di PowerShellStep 1: Check the PowerShell version

1. Aprire Windows PowerShell ed eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Se l'output visualizza una versione superiore alla 3.7.0, eseguire la procedura "Passaggio 2". In caso contrario, andare al "Passaggio 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Passaggio 2: Disinstallare la versione di PowerShellStep 2: Uninstall the PowerShell version

Disinstallare la versione corrente di PowerShell.

1. Rimuovere i moduli dipendenti eseguendo il comando seguente in PowerShell:Remove the dependent modules by running the following command in PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Per garantire la corretta eliminazione di tutti i moduli dipendenti, eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passaggio 3: Installare PowerShell versione 3.7.0Step 3: Install PowerShell version 3.7.0

Dopo aver verificato che non sono presenti moduli di AzureRM, installare la versione 3.7.0 usando uno dei metodi seguenti:After you've verified that no AzureRM modules are present, install version 3.7.0 by using one of the following methods:

* Da GitHub, utilizzare [questo collegamento](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

In alternativa, è possibile:

* Eseguire il comando seguente nella finestra di PowerShell:Run the following command in the PowerShell window:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell potrebbe essere stato installato anche usando un file msi. Per rimuoverlo, disinstallarlo utilizzando l'opzione **Disinstalla programmi** nel Pannello di controllo.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Il processo di backup offline che usa MARS e Azure Data Box richiede che i dispositivi Data Box siano in stato Recapitato prima di attivare il backup offline tramite l'agente MARS. Per ordinare lo SKU più adatto alle proprie esigenze, vedere Dimensioni dei dati di [backup e SKU data box supportati](#backup-data-size-and-supported-data-box-skus). Seguire i passaggi descritti in [Esercitazione: Ordinare un disco](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) di Azure Data Box per ordinare e ricevere i dispositivi Data Box.Follow the steps in Tutorial: Order an Azure Data Box disk to order and receive your Data Box devices.

> [!IMPORTANT]
> Non selezionare *BlobStorage* per il **tipo Account**. L'agente MARS richiede un account che supporti i BLOB di pagine, che non è supportato quando è selezionato *BlobStorage.The* MARS Agent requires an account that supports page blobs, which isn't supported when BlobStorage is selected. Selezionare Archiviazione V2 (uso generale v2) come **tipo di account** quando si crea l'account di archiviazione di destinazione per il processo di Azure Data Box.Select Storage **V2 (general purpose v2)** as the Account kind when you create the target storage account for your Azure Data Box job.

![Scegliere il tipo di account nei dettagli dell'istanza](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installare e configurare l'agente MARS

1. Assicurarsi di disinstallare tutte le installazioni precedenti dell'agente MARS.
1. Scarica l'ultimo agente MARS da [questo sito web](https://aka.ms/azurebackup_agent).
1. Eseguire *MARSAgentInstaller.exe*ed eseguire *solo* la procedura per installare [e registrare l'agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) nell'insieme di credenziali dei servizi di ripristino in cui si desidera archiviare i backup.

   > [!NOTE]
   > L'insieme di credenziali di Servizi di ripristino deve trovarsi nella stessa sottoscrizione del processo di Azure Data Box.The Recovery Services vault must be in the same subscription as the Azure Data Box job.

   Dopo aver registrato l'agente nell'insieme di credenziali dei servizi di ripristino, seguire i passaggi nelle sezioni successive.

## <a name="set-up-azure-data-box-devices"></a>Configurare i dispositivi Azure Data BoxSet up Azure Data Box devices

A seconda dello SKU di Azure Data Box ordinato, eseguire i passaggi illustrati nelle sezioni appropriate seguenti. La procedura mostra come configurare e preparare i dispositivi Data Box affinché l'agente MARS identifichi e trasferisca i dati di backup iniziali.

### <a name="set-up-azure-data-box-disks"></a>Configurare i dischi di Azure Data BoxSet up Azure Data Box disks

Se sono stati ordinati uno o più dischi di Azure Data Box (fino a 8 TB ciascuno), seguire i passaggi indicati qui per [decomprimere, connettersi e sbloccare il disco di Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>È possibile che il server con l'agente MARS non disponga di una porta USB. In questo caso, è possibile connettere il disco di Azure Data Box a un altro server o client ed esporre la radice del dispositivo come condivisione di rete.

### <a name="set-up-azure-data-box"></a>Configurare Azure Data Box

Se è stata ordinata un'istanza di Azure Data Box (fino a 100 TB), seguire i passaggi seguenti [per configurare l'istanza](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)di Data Box .

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montare l'istanza di Azure Data Box come sistema localeMount your Azure Data Box instance as a Local System

L'agente MARS opera nel contesto del sistema locale, pertanto richiede lo stesso livello di privilegio da fornire al percorso di montaggio in cui è connessa l'istanza di Azure Data Box.The MARS Agent operates in the Local System context, so it requires the same level of privilege to be provided to the mount path where the Azure Data Box instance is connected. 

Per assicurarsi di poter montare il dispositivo Data Box come sistema locale utilizzando il protocollo NFS:

1. Abilitare il client per la funzionalità NFS sul server Windows in cui è installato l'agente MARS. Specificare l'origine alternativa *WIM:D:*
1. Scaricare PSExec <https://download.sysinternals.com/files/PSTools.zip> dal server con l'agente MARS installato.
1. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory che contiene *PSExec.exe* come directory corrente.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   La finestra di comando che si apre come risultato del comando precedente si trova nel contesto del sistema locale. Usare questa finestra di comando per eseguire la procedura per montare la condivisione BLOB di pagine di Azure come unità di rete nel server Windows.Use this command window to execute the steps to mount the Azure page blob share as a network drive on your Windows server.
1. Seguire i passaggi descritti in [Connect to Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) per connettere il server all'agente MARS al dispositivo Data Box tramite NFS. Eseguire il comando seguente nel prompt dei comandi del sistema locale per montare la condivisione BLOB di pagine di Azure.Run the following command on the Local System command prompt to mount the Azure page blobs share.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Dopo aver montato la condivisione, verificare se è possibile accedere a X: dal server. Se possibile, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferire i dati di backup iniziali nei dispositivi Azure Data BoxTransfer initial backup data to Azure Data Box devices

1. Aprire l'applicazione Backup di **Microsoft Azure** nel server.
1. Nel riquadro **Azioni** selezionare **Pianifica backup**.

    ![Selezionare Pianifica backup](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Seguire i passaggi della **Pianificazione guidata backup**.

1. Aggiungere elementi selezionando il pulsante **Aggiungi elementi.** Mantenere le dimensioni totali degli elementi entro i limiti di [dimensione supportati dallo SKU](#backup-data-size-and-supported-data-box-skus) di Azure Data Box ordinato e ricevuto.

    ![Aggiungere elementi al backup](./media/offline-backup-azure-data-box/add-items.png)

1. Selezionare la pianificazione e i criteri di conservazione appropriati per **File e cartelle** e Stato del **sistema**. Lo stato del sistema è applicabile solo per i server Windows e non per i client Windows.
1. Nella pagina **Scegli tipo di backup iniziale (file e cartelle)** della procedura guidata selezionare l'opzione **Trasferisci utilizzando i dischi** di Microsoft Azure Data Box e scegliere **Avanti**.

    ![Scegliere il tipo di backup iniziale](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Accedere ad Azure quando richiesto usando le credenziali utente con accesso proprietario nella sottoscrizione di Azure.Sign in to Azure when prompted by using the user credentials that have Owner access on the Azure subscription. Dopo aver avuto successo in questo modo, si dovrebbe vedere una pagina che assomiglia a questo.

    ![Creare risorse e applicare le autorizzazioni necessarie](./media/offline-backup-azure-data-box/creating-resources.png)

   L'agente MARS recupera quindi i processi data box nella sottoscrizione che si trovano nello stato Consegnato.

    ![Recupera processi casella dati per l'ID sottoscrizione](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selezionare l'ordine corretto della data box per il quale è stato decompresso, collegato e sbloccato il disco Data Box. Fare clic su **Avanti**.

    ![Seleziona ordini Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selezionare **Rileva dispositivo** nella pagina **Rilevamento dispositivo casella dati.** Questa azione consente all'agente MARS di eseguire l'analisi dei dischi di Azure Data Box collegati localmente e di rilevarli.

    ![Rilevamento dispositivo casella dati](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se l'istanza di Azure Data Box è stata connessa come condivisione di rete (a causa dell'indisponibilità delle porte USB o perché è stato ordinato e montato il dispositivo Casella dati da 100 TB), il rilevamento non riesce prima. È possibile immettere il percorso di rete del dispositivo Data Box.

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Specificare il percorso di rete della directory radice del disco azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob*.
    >
    >![Directory radice del disco di Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ad esempio, se il percorso `\\mydomain\myserver\disk1\` del disco è e *disco1* contiene una directory denominata `\\mydomain\myserver\disk1\` *PageBlob*, il percorso immesso nella pagina della procedura guidata MARS Agent è .
    >
    >Se si [configura un dispositivo Azure Data Box 100 TB,](#set-up-azure-data-box-devices)immettere `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` il percorso di rete del dispositivo.

1. Selezionare Avanti e quindi Fine nella pagina successiva per salvare i criteri di backup e conservazione con la configurazione del backup offline tramite Casella dati di Azure.Select **Next**, and select **Finish** on the next page to save the backup and retention policy with the configuration of offline backup by using Azure Data Box.

   La pagina seguente conferma che il criterio viene salvato correttamente.

    ![Il criterio viene salvato correttamente](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selezionare **Chiudi** nella pagina precedente.

1. Selezionare **Esegui backup ora** nel riquadro **Azioni** della console dell'agente MARS. Selezionare **Backup** nella pagina della procedura guidata.

    ![Eseguire il backup guidato](./media/offline-backup-azure-data-box/backup-now.png)

L'agente MARS avvia il backup dei dati selezionati nel dispositivo Azure Data Box.The MARS Agent starts backing the data you selected to the Azure Data Box device. Questo processo potrebbe richiedere da diverse ore a pochi giorni. La quantità di tempo dipende dal numero di file e dalla velocità di connessione tra il server con l'agente MARS e il disco di Azure Data Box.

Al termine del backup dei dati, verrà visualizzata una pagina sull'agente MARS simile a questa.

![Avanzamento backup visualizzato](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passaggi di post-backup

In questa sezione vengono illustrati i passaggi da eseguire dopo il completamento del backup dei dati nel disco Azure Data Box.

* Seguire i passaggi descritti in questo articolo per [spedire il disco di Azure Data Box in Azure.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) Se è stato usato un dispositivo Azure Data Box 100 TB, seguire questi passaggi per [spedire il dispositivo Azure Data Box ad Azure.If you](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)used an Azure Data Box 100-TB device, follow these steps to ship the Azure Data Box device to Azure.

* [Monitorare il processo di data box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) nel portale di Azure.Monitor the Data Box job in the Azure portal. Al termine del processo di Azure Data Box, l'agente MARS sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali dei servizi di ripristino al momento del successivo backup pianificato. Il processo di backup viene quindi *contrassegnato* come processo completato se un punto di ripristino viene creato correttamente.

    >[!NOTE]
    >L'agente MARS attiva i backup negli orari pianificati durante la creazione dei criteri. Questi processi contrassegnano "In attesa del completamento del processo di Azure Data Box" fino al termine del processo.

* Dopo che l'agente MARS crea correttamente un punto di ripristino che corrisponde al backup iniziale, è possibile eliminare l'account di archiviazione o contenuti specifici associati al processo di Azure Data Box.After the MARS Agent successfully creates a recovery point that corresponds to the initial backup, you can delete the storage account or specific contents associated with the Azure Data Box job.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'agente di Backup di Microsoft Azure (MAB) crea automaticamente un'applicazione Azure Active Directory (Azure AD) nel tenant. Questa applicazione richiede un certificato per l'autenticazione che viene creato e caricato quando si configura un criterio di seeding offline. Utilizziamo Azure PowerShell per creare e caricare il certificato nell'applicazione Azure AD.

### <a name="problem"></a>Problema

Quando si configura il backup offline, è possibile che si verifichi un problema a causa di un bug nel cmdlet PowerShell di Azure.When you configure offline backup, you might face a problem because of a bug in the Azure PowerShell cmdlet. Potrebbe non essere possibile aggiungere più certificati alla stessa applicazione Azure AD creata dall'agente MAB. Questo problema si ripermerà se è stato configurato un criterio di seeding non in linea per lo stesso server o un server diverso.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verificare se il problema è causato da questa causa principale specifica

Per verificare se il problema è lo stesso descritto in precedenza, eseguire una delle operazioni seguenti.

#### <a name="step-1"></a>Passaggio 1

Verificare se nella console MAB viene visualizzato il seguente messaggio di errore quando è stato configurato il backup offline.

![Impossibile creare i criteri di backup non in linea per l'account Azure corrente](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Passaggio 2

1. Aprire la cartella **Temp** nel percorso di installazione. Il percorso predefinito della cartella temporanea è *C:.* Cercare il file *CBUICurr* e aprire il file.

1. Nel file *CBUICurr* scorrere fino all'ultima riga e verificare se il problema è `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`lo stesso di quello in questo messaggio di errore: .

### <a name="workaround"></a>Soluzione alternativa

Per risolvere il problema, eseguire la procedura seguente e ripetere la configurazione dei criteri.

#### <a name="step-1"></a>Passaggio 1

Accedere a PowerShell che viene visualizzato nell'interfaccia utente di MAB utilizzando un account diverso con accesso amministrativo nella sottoscrizione in cui verrà creato il processo di importazione o esportazione.

#### <a name="step-2"></a>Passaggio 2

Se nessun altro server ha configurato il seeding `AzureOfflineBackup_<Azure User Id>` offline e nessun altro server dipende dall'applicazione, eliminare l'applicazione. Selezionare**Registrazioni**app**di Azure Active Directory** > del **portale** > di Azure .

>[!NOTE]
> Verificare se `AzureOfflineBackup_<Azure User Id>` l'applicazione non dispone di altri seeding offline configurati e anche se nessun altro server dipende da questa applicazione. Passare a **Chiavi impostazioni** > **Keys** nella sezione **Chiavi pubbliche.** Non deve essere aggiunta altre chiavi pubbliche. Vedere la schermata seguente per riferimento.
>
>![Chiavi pubbliche](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Passaggio 3

Dal server che si sta tentando di configurare per il backup offline, eseguire le azioni seguenti.

1. Passare alla scheda **Personale dell'applicazione** > **Personal** Gestisci certificati di `CB_AzureADCertforOfflineSeeding_<ResourceId>`computer e cercare il certificato con il nome .

2. Selezionare il certificato, fare clic con il pulsante destro del mouse su **Tutte le attività**e selezionare **Esporta** senza una chiave privata nel formato CER.

3. Passare all'applicazione di backup offline di Azure menzionata nel passaggio 2.Go to the Azure offline backup application mentioned in step 2. Selezionare **Impostazioni** > **tasti Carica** > **chiave pubblica**. Caricare il certificato esportato nel passaggio precedente.

    ![Carica chiave pubblica](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Nel server, aprire il Registro di sistema immettendo **regedit** nella finestra Esegui.

5. Passare al Computer del Registro di sistema *computer HKEY_LOCAL_MACHINE SOFTWARE Microsoft Windows Azure Backup Config CloudBackupProvider.* Fare clic con il pulsante destro del mouse `AzureADAppCertThumbprint_<Azure User Id>`su **CloudBackupProvider**e aggiungere un nuovo valore stringa con il nome .

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una delle azioni seguenti:To get the Azure user ID, perform one of these actions:
    >
    >* Da PowerShell connesso ad Azure `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` eseguire il comando.
    > * Passare al percorso del Registro di sistema *Computer, HKEY_LOCAL_MACHINE SOFTWARE, Microsoft Windows Azure Backup, DbgSettings, OnlineBackup* con il nome *CurrentUserId*.

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **Modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel passaggio 2. Selezionare **OK**.

7. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato. Selezionare la scheda **Dettagli** e scorrere verso il basso fino a visualizzare il campo di identificazione personale. Selezionare **Identificazione personale**e copiare il valore.

    ![Campo di identificazione personale del certificato](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Domande

Per qualsiasi domanda o chiarimenti su eventuali [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)problemi che hai affrontato, contatta .
