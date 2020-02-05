---
title: Backup offline con Azure Data Box
description: Informazioni su come usare Azure Data Box per inizializzare i dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di servizi di ripristino di Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027030"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Backup di Azure offline: backup con Azure Data Box

È possibile usare il servizio [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) per eseguire il seeding dei backup Mars iniziali di grandi dimensioni offline (senza usare la rete) in un insieme di credenziali di servizi di ripristino di Azure.  Questo consente di risparmiare tempo e larghezza di banda di rete, che altrimenti verrebbero utilizzati per lo trasferimento di grandi quantità di dati di backup online su una rete ad alta latenza. Questa funzionalità avanzata è attualmente in fase di anteprima. Il backup offline basato su Azure Data Box offre due vantaggi distinti rispetto al [backup offline basato sul servizio importazione/esportazione di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Non è necessario procurarsi dischi e connettori compatibili con Azure. Azure Data Box servizio fornisce i dischi associati allo [SKU data box](https://azure.microsoft.com/services/databox/data/) selezionato

2. Backup di Azure (agente MARS) può scrivere direttamente i dati di backup sugli SKU supportati di Azure Data Box. In questo modo si elimina la necessità di eseguire il provisioning di un percorso di gestione temporanea per i dati di backup iniziali e la necessità di utilizzare le utilità per formattare e copiare i dati sui dischi.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box con l'agente MARS

Questo articolo illustra come usare Azure Data Box per inizializzare i dati di backup iniziali di grandi dimensioni offline dall'agente MARS a un insieme di credenziali di servizi di ripristino di Azure. L'articolo è suddiviso nelle parti seguenti:

* Piattaforme supportate
* SKU Data Box supportati
* Prerequisiti
* Configurare l'agente MARS
* Azure Data Box di installazione
* Trasferimento dati di backup a Azure Data Box
* Passaggi successivi al backup

## <a name="supported-platforms"></a>Piattaforme supportate

Il processo per eseguire il seeding dei dati dall'agente MARS usando Azure Data Box è supportato negli SKU di Windows seguenti:

| **Sistema operativo**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Workstation**                        |                                                              |
| Windows 10 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64 bit                    | Enterprise, Pro                                             |
| Windows 8 64 bit                      | Enterprise, Pro                                             |
| Windows 7 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bit            | Datacenter, Foundation, standard                            |
| Windows Storage Server 2016 64 bit    | Standard, gruppo di lavoro                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64 bit    | Standard, gruppo di lavoro                                         |
| Windows Server 2008 R2 SP1 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU Data Box supportati

| Dimensioni dei dati di backup (post-compressione da MARS) * per server | SKU Azure Data Box supportati                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB e < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* I normali tassi di compressione variano da 10-20% <br>
\* * Contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) se si prevede di avere più di 80 TB di dati di backup iniziali per un singolo server Mars.

>[!IMPORTANT]
>I dati di backup iniziali da un singolo server devono essere contenuti all'interno di una singola Azure Data Box o Azure Data Box Disk e non possono essere condivisi tra più dispositivi dello stesso o di SKU diversi. Tuttavia, un dispositivo Azure Data Box può contenere backup iniziali da più server.

## <a name="pre-requisites"></a>Prerequisiti

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarie

* Il processo richiede una sottoscrizione di Azure
* Il processo richiede che l'utente designato per l'esecuzione dei criteri di backup non in linea sia un "proprietario" della sottoscrizione di Azure
* Il processo Data Box e l'insieme di credenziali di servizi di ripristino (in cui è necessario eseguire il seeding dei dati) devono trovarsi nelle stesse sottoscrizioni.
* È consigliabile che l'account di archiviazione di destinazione associato al processo di Azure Data Box e l'insieme di credenziali di servizi di ripristino si trovino nella stessa area. Tuttavia, questa operazione non è necessaria.

### <a name="get-azure-powershell-370"></a>Ottenere Azure PowerShell 3.7.0

**Questo è il prerequisito più importante per il processo**. Prima di installare Azure PowerShell (ver. 3.7.0), eseguire i controlli seguenti * *

#### <a name="step-1-check-powershell-version"></a>Passaggio 1: controllare la versione di PowerShell

* Aprire Windows PowerShell ed eseguire il comando seguente:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Se l'output visualizza una versione superiore a **3.7.0**, eseguire il passaggio 2 riportato di seguito. In caso contrario, andare direttamente al passaggio 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Passaggio 2: disinstallare la versione di PowerShell

Disinstallare la versione corrente di PowerShell eseguendo le azioni seguenti:

* Rimuovere i moduli dipendenti eseguendo il comando seguente in PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Eseguire il comando seguente per assicurarsi che l'eliminazione di tutti i moduli dipendenti sia riuscita:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passaggio 3: installare la versione di PowerShell 3.7.0

Dopo aver verificato che non siano presenti moduli AzureRM, installare la versione 3.7.0 usando uno dei metodi seguenti:

* Da GitHub, [collegamento](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OPPURE

* Eseguire il comando seguente nella finestra di PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell potrebbe essere stato installato anche usando un file MSI. Per rimuoverlo, disinstallarlo usando l'opzione Disinstalla programmi nel pannello di controllo.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Per il processo di backup non in linea che usa MARS e Azure Data Box è necessario che i dispositivi Data Box richiesti siano nello stato "recapitato" prima di attivare il backup offline usando l'agente MARS. Per ordinare lo SKU più adatto ai propri requisiti, vedere le [dimensioni dei dati di backup e gli sku data box supportati](#backup-data-size-and-supported-data-box-skus) . Seguire la procedura descritta in [questo articolo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) per ordinare e ricevere i dispositivi data box.

>[!IMPORTANT]
>Non selezionare BlobStorage per il tipo di account. Per l'agente MARS è necessario un account che supporti i BLOB di pagine non supportati quando si seleziona BlobStorage. Quando si crea l'account di archiviazione di destinazione per il processo di Azure Data Box, è consigliabile selezionare *storage V2* (*utilizzo generico V2*) come tipo di account.

![Scegliere il tipo di account nei dettagli dell'istanza](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Installare e configurare l'agente MARS

* Assicurarsi di disinstallare le installazioni precedenti dell'agente MARS.

* Scaricare la versione più recente dell'agente MARS da [qui](https://aka.ms/azurebackup_agent).

* Eseguire *MARSAgentInstaller. exe* ed eseguire ***solo** i passaggi per [installare e registrare l'agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) nell'insieme di credenziali dei servizi di ripristino in cui si desidera archiviare i backup.

  >[!NOTE]
  > L'insieme di credenziali di servizi di ripristino deve trovarsi nella stessa sottoscrizione del processo di Azure Data Box.

* Dopo che l'agente è stato registrato nell'insieme di credenziali di servizi di ripristino, seguire i passaggi nelle sezioni riportate di seguito.

## <a name="setup-azure-data-box-devices"></a>Configurare Azure Data Box dispositivo/i

A seconda della Azure Data Box SKU ordinata, eseguire i passaggi descritti nelle sezioni appropriate seguenti per configurare e preparare i dispositivi Data Box per l'agente MARS per identificare e trasferire i dati di backup iniziali.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box Disk di installazione

Se è stato ordinato uno o più dischi Azure Data Box (fino a 8 TB ciascuno), seguire i passaggi indicati qui per [decomprimere, connettere e sbloccare il data box disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>È possibile che il server con l'agente MARS non disponga di una porta USB. In questa situazione è possibile connettere il Azure Data Box Disk a un altro server/client ed esporre la radice del dispositivo come una condivisione di rete.

### <a name="setup-azure-data-box"></a>Azure Data Box di installazione

Se è stato ordinato un Azure Data Box (fino a 100 TB), seguire i passaggi indicati qui [per configurare la data box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Montare il Azure Data Box come sistema locale

L'agente MARS opera nel contesto del sistema locale e pertanto richiede lo stesso livello di privilegio da fornire al percorso di montaggio in cui è connessa la Azure Data Box. Attenersi alla procedura seguente per assicurarsi di poter montare il dispositivo Data Box come sistema locale usando il protocollo NFS:

* Abilitare la funzionalità client per NFS nel server Windows in cui è installato l'agente MARS.<br>
  Specificare un'origine alternativa: *Wim: D: \sources\install.wim: 4*

* Scaricare PSExec dal <https://download.sysinternals.com/files/PSTools.zip> al server con l'agente MARS installato.

* Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory contenente PSExec. exe come directory corrente:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* La finestra di comando visualizzata come risultato del comando precedente si trova nel contesto del sistema locale. Usare questa finestra di comando per eseguire i passaggi per montare la condivisione BLOB di pagine di Azure come unità di rete in Windows Server.

* Seguire questa procedura per connettere il server con l'agente MARS al dispositivo Data Box tramite NFS ed eseguire il comando seguente nel prompt dei [comandi di sistema](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) locale per montare la condivisione BLOB di pagine di Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Una volta montati, controllare se è possibile accedere a X: dal server. In caso affermativo, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferisce i dati di backup iniziali a Azure Data Box dispositivo/i

* Aprire l'applicazione **backup di Microsoft Azure** nel server.

* Fare clic su **Pianifica backup** nel riquadro **azioni** .

    ![Fare clic su Pianifica backup](./media/offline-backup-azure-data-box/schedule-backup.png)

* Eseguire i passaggi della **pianificazione guidata backup**

* **Aggiungere elementi** in modo che la dimensione totale degli elementi rientri nei [limiti di dimensione supportati dal Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) ordinato e ricevuto.

    ![Aggiungi elementi al backup](./media/offline-backup-azure-data-box/add-items.png)

* Selezionare la pianificazione di backup e i criteri di conservazione appropriati per file e cartelle e lo stato del sistema (lo stato del sistema è applicabile solo per i server Windows e non per i client Windows)

* Nella schermata **scegliere il tipo di backup iniziale (file e cartelle)** della procedura guidata, scegliere l'opzione per il **trasferimento usando Microsoft Azure Data Box dischi** e fare clic su **Avanti** .

    ![Scegliere il tipo di backup iniziale](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Accedere ad Azure quando viene richiesto usando le credenziali utente con accesso proprietario nella sottoscrizione di Azure. Una volta completata questa operazione, verrà visualizzata una schermata simile a quella riportata di seguito:

    ![Creazione di risorse e applicazione delle autorizzazioni necessarie](./media/offline-backup-azure-data-box/creating-resources.png)

* L'agente MARS recupererà quindi i processi di Data Box nella sottoscrizione che si trovano nello stato "recapitato".

    ![Recupero dei processi databox per l'ID sottoscrizione](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Selezionare l'ordine di data box corretto per il quale è stato decompresso, connesso e sbloccato il disco Data Box. Fare clic su **Avanti**.

    ![Selezionare Data Box ordine/i](./media/offline-backup-azure-data-box/select-databox-order.png)

* Fare clic su **rileva dispositivo** nella schermata di **rilevamento del dispositivo data box** . Questo consente di eseguire l'analisi dell'agente MARS per i dischi Azure Data Box collegati localmente e di rilevarli come illustrato di seguito:

    ![Rilevamento del dispositivo Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se la Azure Data Box è stata connessa come una condivisione di rete (a causa della mancata disponibilità delle porte USB o perché è stato ordinato e montato 100 il dispositivo Data Box TB), il rilevamento avrà esito negativo, ma fornirà la possibilità di immettere il percorso di rete per il dispositivo Data Box hown di seguito:

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Consente di specificare il percorso di rete della directory radice del disco Azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob* , come illustrato di seguito:
    >
    >![Directory radice del disco Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Se, ad esempio, il percorso del disco è `\\mydomain\myserver\disk1\` e *disk1* contiene una directory denominata *PageBlob*, il percorso da fornire nella procedura guidata dell'agente Mars è `\\mydomain\myserver\disk1\`
    >
    >Se si [configura un dispositivo Azure Data Box 100 TB](#setup-azure-data-box), fornire quanto segue come percorso di rete al dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Fare clic su **Avanti** e fare clic su **fine** nella schermata successiva per salvare i criteri di backup e conservazione con la configurazione del backup offline usando Azure Data Box.

* La schermata seguente conferma che il criterio è stato salvato correttamente:

    ![Salvataggio dei criteri completato](./media/offline-backup-azure-data-box/policy-saved.png)

* Fare clic su **Chiudi** nella schermata precedente.

* Fare clic su ***Esegui backup ora** nel riquadro **Azioni** della console dell'agente Mars e fare clic su **backup** nella schermata della procedura guidata, come illustrato di seguito:

    ![Procedura guidata Esegui backup](./media/offline-backup-azure-data-box/backup-now.png)

* L'agente MARS avvierà il backup dei dati selezionati nel dispositivo Azure Data Box. Questa operazione potrebbe richiedere da diverse ore a pochi giorni, a seconda del numero di file e della velocità di connessione tra il server con l'agente MARS e il Azure Data Box Disk.

* Al termine del backup dei dati, verrà visualizzata una schermata nell'agente MARS simile a quella riportata di seguito:

    ![Stato di avanzamento del backup](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passaggi successivi al backup

In questa sezione vengono illustrati i passaggi da eseguire una volta che il backup dei dati nel Azure Data Box Disk ha avuto esito positivo.

* Seguire la procedura descritta in questo articolo per [distribuire il disco Azure Data Box ad Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se è stato usato un dispositivo Azure Data Box 100 TB, seguire questa procedura per [spedire il Azure Data Box ad Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitorare il processo di data box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) nel portale di Azure. Una volta completato il processo di Azure Data Box, l'agente MARS sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali di servizi di ripristino al momento del backup pianificato successivo. Il processo di backup verrà quindi contrassegnato come "processo completato" Se un punto di ripristino viene creato correttamente.

    >[!NOTE]
    >L'agente MARS attiverà i backup in orari pianificati durante la creazione dei criteri. Tuttavia, questi processi contrassegnano "in attesa del completamento del processo Azure Data Box" fino al completamento del processo.

* Quando l'agente MARS crea correttamente un punto di ripristino corrispondente al backup iniziale, è possibile eliminare l'account di archiviazione (o il contenuto specifico) associato al processo di Azure Data Box.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'agente di Backup di Microsoft Azure (MAB) crea automaticamente un'applicazione Azure AD nel tenant. Questa applicazione richiede un certificato per l'autenticazione che viene creato e caricato quando si configurano i criteri di seeding offline. Per la creazione e il caricamento del certificato nell'applicazione Azure AD vengono usati Azure PowerShell.

### <a name="issue"></a>Problema

Al momento della configurazione del backup offline è possibile che si verifichi un problema, dove a causa di un bug nel cmdlet Azure PowerShell non è possibile aggiungere più certificati alla stessa applicazione Azure AD creata dall'agente MAB. Ciò avrà un effetto se sono stati configurati criteri di seeding offline per lo stesso server o per un server diverso.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Come verificare se il problema è causato da questa specifica causa radice

Per assicurarsi che l'errore sia dovuto al problema precedente, eseguire uno dei passaggi seguenti:

#### <a name="step-1"></a>Passaggio 1

Controllare se viene visualizzato il messaggio di errore seguente nella console di MAB al momento della configurazione del backup offline:

![Non è possibile creare i criteri di backup offline per l'account Azure corrente](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Passaggio 2

* Aprire la cartella **Temp** nel percorso di installazione (il percorso predefinito della cartella temporanea è *C:\Programmi\Microsoft Azure Recovery Services Agent\Temp*). Cercare il file **CBUICurr** e aprire il file.

* Nel file **CBUICurr** scorrere fino all'ultima riga e verificare se l'errore è dovuto a `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Soluzione alternativa

Per risolvere il problema, eseguire la procedura seguente e ripetere la configurazione dei criteri.

#### <a name="first-step"></a>Primo passaggio

Accedere a PowerShell che viene visualizzato nell'interfaccia utente di MAB usando un account diverso con accesso amministrativo alla sottoscrizione in cui verrà creato il processo di importazione/esportazione.

#### <a name="second-step"></a>Secondo passaggio

Se nessun altro server dispone di un seeding offline configurato e nessun altro server dipende dall'applicazione `AzureOfflineBackup_<Azure User Id>`, eliminare questa applicazione da **portale di Azure** > **Azure Active Directory** > registrazioni app **.**

>[!NOTE]
> Verificare se per l'applicazione `AzureOfflineBackup_<Azure User Id>` non sono configurati altri seeding offline e anche nessun altro server dipende da questa applicazione. Passare a **impostazioni** > **chiavi** sotto la sezione **chiavi pubbliche** per aggiungere altre chiavi pubbliche. Per informazioni di riferimento, vedere la schermata seguente:
>
>![Chiavi pubbliche](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Terzo passaggio

Dal server che si sta tentando di configurare il backup offline, eseguire le azioni seguenti:

1. Aprire la scheda **Gestisci applicazione certificato computer** > **Personal** e cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Selezionare il certificato precedente, fare clic con il pulsante destro del mouse su **tutte le attività** e su **Esporta** senza chiave privata nel formato CER.

3. Passare all'applicazione di backup offline di Azure indicata nel **punto 2**. In **impostazioni** > **chiavi** > **carica chiave pubblica,** caricare il certificato esportato nel passaggio precedente.

    ![Carica chiave pubblica](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Nel server aprire il registro di sistema digitando **Regedit** nella finestra Esegui.

5. Accedere al computer del registro di sistema *\ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Fare clic con il pulsante destro del mouse su **CloudBackupProvider** e aggiungere un nuovo valore stringa con nome `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una delle azioni seguenti:
    >
    >1. Da PowerShell connesso ad Azure eseguire il comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Passare al percorso del registro di sistema: *computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Nome: *CurrentUserId*

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel **punto 2** e fare clic su **OK**.

7. Per ottenere il valore di identificazione personale, fare doppio clic sul certificato, quindi selezionare la scheda **Dettagli** e scorrere verso il basso fino a visualizzare il campo identificazione personale. Fare clic su **identificazione personale** e copiare il valore.

    ![Campo identificazione personale del certificato](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Domande?

Per eventuali domande o chiarimenti relativi a eventuali problemi riscontrati, contattare [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
