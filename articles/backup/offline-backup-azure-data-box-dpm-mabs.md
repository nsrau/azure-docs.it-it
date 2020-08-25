---
title: Backup offline con Azure Data Box per DPM e MAB
description: È possibile utilizzare Azure Data Box per inizializzare i dati di backup iniziali offline da DPM e da MAB.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 974be6d337c3376d10e09ba6211f7804c2c8cada
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824560"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Seeding offline con Azure Data Box per DPM e MAB (anteprima)

> [!NOTE]
> Questa funzionalità è applicabile per Data Protection Manager (DPM) 2019 UR2 e versioni successive.<br><br>
> Questa funzionalità è attualmente disponibile in anteprima per Backup di Microsoft Azure Server (MAB). Se si è interessati all'uso di Azure Data Box per il seeding offline con MAB, contattare Microsoft all'indirizzo [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Questo articolo illustra come usare Azure Data Box per inizializzare i dati di backup iniziali offline da DPM e da MAB a un insieme di credenziali di servizi di ripristino di Azure.

È possibile usare [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) per inizializzare i backup DPM/MAB iniziali di grandi dimensioni offline (senza usare la rete) in un insieme di credenziali di servizi di ripristino. Questo processo consente di risparmiare tempo e larghezza di banda di rete che altrimenti verrebbero usati per spostare grandi quantità di dati di backup online tramite una rete ad alta latenza. Questa funzionalità è attualmente in anteprima.

Il backup offline basato su Azure Data Box offre due vantaggi diversi rispetto al [backup offline basato sul servizio Importazione/Esportazione di Azure](backup-azure-backup-server-import-export.md):

- Non è necessario acquistare dischi e connettori compatibili con Azure. Azure Data Box include i dischi associati allo [SKU Data Box](https://azure.microsoft.com/services/databox/data/) selezionato.

- Backup di Azure (agente MARS) può scrivere direttamente i dati di backup negli SKU supportati di Azure Data Box. Questa funzionalità elimina la necessità di effettuare il provisioning di una posizione di staging per i dati di backup iniziali. Non sono neppure necessarie utilità per formattare e copiare i dati sui dischi.

## <a name="supported-platforms"></a>Piattaforme supportate

Sono supportate le piattaforme seguenti:

- Windows Server 2019 a 64 bit (Standard, Datacenter, Essentials)
- Windows Server 2016 a 64 bit (Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU Data Box supportati

Sono supportati gli SKU Data Box seguenti:

| Dimensioni dei dati di backup (dopo la compressione da Marte) \* per server | SKU Azure Data Box supportati |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB e <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*I tassi di compressione sono in genere compresi tra il 10% e il 20% <br>
\*\*Contattare [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) se si prevede che i dati di backup iniziali per una singola origine dati saranno superiori a 80 TB.

> [!IMPORTANT]
> I dati di backup iniziali di una singola origine dati devono essere contenuti all'interno di un singolo Azure Data Box o Azure Data Box disco e non possono essere condivisi tra più dispositivi dello stesso o di SKU diversi. Un'istanza di Azure Data Box può tuttavia contenere backup iniziali da più origini dati.

## <a name="before-you-begin"></a>Prima di iniziare

L'agente MARS in esecuzione su DPM/MAB deve essere aggiornato alla [versione più recente](https://aka.ms/azurebackup_agent) (2.0.9171.0 o versione successiva).

Verificare quanto segue:

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarie

- Una sottoscrizione di Azure valida.
- L'utente che dovrà eseguire i criteri di backup offline deve essere un proprietario della sottoscrizione di Azure.
- Il processo Data Box e l'insieme di credenziali dei servizi di ripristino in cui è necessario eseguire il seeding dei dati devono essere disponibili nelle stesse sottoscrizioni.
    > [!NOTE]
    > È consigliabile che l'account di archiviazione di destinazione e l'insieme di credenziali di servizi di ripristino si trovino nella stessa area. Tuttavia, questo non è obbligatorio.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Verificare che i dispositivi Data Box richiesti siano in stato *recapitato* prima di attivare il backup offline. Per ordinare lo SKU più adatto in base alle proprie esigenze, vedere [Dimensioni dei dati di backup e SKU Data Box supportati](#backup-data-size-and-supported-data-box-skus). Per ordinare e ricevere i dispositivi Data Box, seguire i passaggi di [questo articolo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered).

> [!IMPORTANT]
> Non selezionare *BlobStorage* per il **tipo di account**. Il server DPM/MAB richiede un account che supporti i BLOB di pagine che non sono supportati quando si seleziona *BlobStorage* . Selezionare  **storage V2 (utilizzo generico v2)** come **tipo di account** quando si crea l'account di archiviazione di destinazione per il processo di Azure Data Box.

![Configurare Azure Data Box](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Configurare dispositivi Azure Data Box

Una volta ricevuto il dispositivo Azure Data Box, a seconda dello SKU Azure Data Box ordinato, eseguire i passaggi nelle sezioni appropriate di seguito per configurare e preparare i dispositivi Data Box per il server DPM/MAB per identificare e trasferire i dati di backup iniziali.

### <a name="setup-azure-data-box-disk"></a>Configurare Azure Data Box Disk

Se è stato ordinato uno o più dischi Azure Data Box (fino a 8 TB ognuno), seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up) per decomprimere, connettere e sbloccare il disco Data Box.

> [!NOTE]
> È possibile che il server DPM/MAB non disponga di una porta USB. In questo caso, è possibile connettere il disco Azure Data Box a un altro server/client ed esporre la radice del dispositivo come condivisione di rete.

## <a name="setup-azure-data-box"></a>Configurare Azure Data Box

Se è stato ordinato un Azure Data Box (fino a 100 TB), seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) per configurare la data box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Montare Azure Data Box come sistema locale

Il server DPM/MAB funziona nel contesto di sistema e pertanto richiede lo stesso livello di privilegio da fornire al percorso di montaggio in cui è connessa la Azure Data Box. Attenersi alla procedura seguente per assicurarsi di poter montare il dispositivo Data Box come sistema locale usando il protocollo NFS.

1. Abilitare la funzionalità client per NFS nel server DPM/MAB.
Specificare l'origine alternativa: *WIM:D:\Sources\Install.wim:4*
2. Scaricare **PsExec** da [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) al server DPM/MAB.
3. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory contenente *PSExec.exe* come directory corrente.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. La finestra di comando visualizzata come risultato del comando precedente si trova nel contesto del sistema locale. Usare questa finestra di comando per eseguire i passaggi per montare la condivisione del BLOB di pagine di Azure come unità di rete in Windows Server.
5. Seguire questa procedura per connettere il server DPM/MAB al dispositivo Data Box tramite NFS ed eseguire il comando seguente nel prompt dei [comandi di sistema](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) locale per montare la condivisione BLOB di pagine di Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Dopo il montaggio, controllare se è possibile accedere a X: dal server. Se possibile, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferire i dati di backup iniziali a dispositivi Azure Data Box

1. Nel server DPM/MAB, attenersi alla procedura per [creare un nuovo gruppo protezione](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019)dati. Se si aggiunge una protezione in linea al gruppo protezione dati esistente, fare clic con il pulsante destro del mouse sul gruppo protezione dati esistente, quindi scegliere **Aggiungi protezione dati online** e iniziare dal **passaggio 8**.
2. Nella pagina **Selezione membri del gruppo** specificare i computer e le origini di cui si vuole eseguire il backup.
3. Nella pagina **Seleziona metodo protezione dati** specificare come si vuole gestire i backup a breve e lungo termine. Assicurarsi di selezionare **Protezione dati online**.

   ![Creare il nuovo gruppo protezione dati](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Nella pagina **Selezione obiettivi a breve termine** specificare come si vuole eseguire il backup di archiviazione a breve termine su disco.
5. Nella pagina **Verifica allocazione dischi** verificare lo spazio su disco per il pool di archiviazione allocato al gruppo protezione dati.
6. Nella pagina **Scelta del metodo per la creazione della replica** selezionare **Automaticamente tramite rete**.
7. Nella pagina **Scelta opzioni di verifica coerenza** scegliere in che modo automatizzare le verifiche coerenza.
8. Nella pagina **specificare i dati per la protezione dati online** selezionare il membro che si desidera abilitare la protezione online.

    ![Specifica i dati da proteggere online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Nella pagina **Specificare la pianificazione dei backup online** specificare la frequenza con cui eseguire i backup incrementali in Azure.
10. Nella pagina **Specificare i criteri di conservazione online** specificare come vengono mantenuti i punti di ripristino creati dalle copie di backup giornaliere/settimanali/mensili/annuali archiviate in Azure.
11. Nella schermata **scegliere la replica online** della procedura guidata scegliere l'opzione per il **trasferimento tramite dischi di proprietà di Microsoft** e selezionare **Avanti**.

    ![Scegliere la replica online iniziale](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > L'opzione per selezionare il **trasferimento tramite dischi di proprietà di Microsoft** non è disponibile per MAB V3 perché la funzionalità è in anteprima. [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)Se si vuole usare questa funzionalità per MAB V3, contattare Microsoft.

12. Quando viene richiesto, accedere ad Azure usando le credenziali utente con accesso di tipo proprietario alla sottoscrizione di Azure. Dopo aver eseguito l'accesso, viene visualizzata la schermata seguente:

    ![Dopo l'accesso riuscito](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     Il server DPM/MAB recupererà quindi i processi di Data Box nella sottoscrizione che si trovano nello stato *recapitato* .

     > [!NOTE]
     > Il primo accesso richiede più tempo del solito. Il modulo Azure PowerShell viene installato in background e l'applicazione Azure AD viene registrata.
     >
     >  - Vengono installati i moduli di PowerShell seguenti:<br>
          - AzureRM.Profile *5.8.3*<br>
          - AzureRM.Resources *6.7.3*<br>
          - AzureRM.Storage *5.2.0*<br>
          - Azure.Storage *4.6.1*<br>
     >  - L'applicazione Azure AD viene registrata come *AzureOfflineBackup_\<object GUID of the user>* .

13. Selezionare l'ordine di Data Box corretto per il quale è stato decompresso, connesso e sbloccato il disco Data Box. Selezionare **Avanti**.

    ![Selezionare l'istanza di Data Box](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Nella schermata **rileva il databox** immettere il percorso del dispositivo data box, quindi selezionare **rileva dispositivo**.

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Specificare il percorso di rete della directory radice del disco Azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob*, come illustrato di seguito:
    >
    > ![Unità USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Ad esempio, se il percorso del disco è `\\mydomain\myserver\disk1\` e *disk1* contiene una directory denominata *PageBlob*, il percorso da fornire nella procedura guidata del server DPM/MAB è `\\mydomain\myserver\disk1\` .
    > Se si [configura un dispositivo Azure Data Box da 100 TB](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box), specificare il percorso di rete seguente per il dispositivo: `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`.

15. Selezionare **Avanti**. Nella pagina **Riepilogo** verificare le impostazioni e selezionare **Crea gruppo**.

    ![Rilevare il DataBox](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    La schermata seguente conferma che il gruppo protezione dati è stato creato correttamente.

    ![Gruppo protezione dati creato](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Selezionare **Chiudi** nella schermata precedente.

    In questo modo, la replica iniziale dei dati viene eseguita nel disco di DPM/MAB. Al termine della protezione, lo stato del gruppo indicherà lo stato di protezione dati su **OK** nella pagina **protezione** .

17. Per avviare la copia di backup offline nel dispositivo Azure Data Box, fare clic con il pulsante destro del mouse sul **gruppo protezione**dati, quindi scegliere l'opzione **Crea punto di ripristino** . Scegliere quindi l'opzione **Protezione dati online**.

    ![Crea punto di ripristino](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Punto di ripristino](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   Il server DPM/MAB avvierà il backup dei dati selezionati nel dispositivo Azure Data Box. Questa operazione potrebbe richiedere da diverse ore a pochi giorni, a seconda delle dimensioni dei dati e della velocità di connessione tra il server DPM/MAB e il Azure Data Box Disk.

   È possibile monitorare lo stato del processo nel riquadro **Monitoraggio**. Al termine del backup dei dati, verrà visualizzata una schermata simile a quella riportata di seguito:

   ![Console di amministrazione](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Passaggi da eseguire dopo il backup

Al termine del backup dei dati in Azure Data Box Disk, seguire questa procedura.

- Per inviare Azure Data Box Disk ad Azure, seguire la procedura descritta in [questo articolo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se è stato usato un dispositivo Azure Data Box da 100 TB, seguire [questi passaggi](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) per inviare Azure Data Box ad Azure.
- [Monitorare il processo Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) nel portale di Azure. Al *termine*del processo di Azure Data Box, il server DPM/MAB sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali di servizi di ripristino al momento del backup pianificato successivo. Contrassegna quindi il processo di backup come *Processo completato* se viene creato un punto di ripristino.

  > [!NOTE]
  > Il server DPM/MAB attiva i backup nelle ore pianificate durante la creazione del gruppo protezione dati. Questi processi risulteranno tuttavia *in attesa del completamento del processo Azure Data Box* fino al termine del processo.

- Al termine della creazione di un punto di ripristino corrispondente al backup iniziale da parte del server DPM/MAB, è possibile eliminare l'account di archiviazione (o il contenuto specifico) associato al processo di Azure Data Box.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'agente Backup di Microsoft Azure nel server DPM crea automaticamente un'applicazione Azure AD nel tenant. Questa applicazione richiede un certificato per l'autenticazione, che viene creato e caricato quando si configurano i criteri di seeding offline.

Per creare e caricare il certificato nell'applicazione Azure AD, viene usato Azure PowerShell.

### <a name="issue"></a>Problema

Al momento della configurazione del backup offline, a causa di un difetto del codice noto nel cmdlet Azure PowerShell non è possibile aggiungere più certificati alla stessa applicazione Azure AD creata dall'agente MAB. Ciò avrà un effetto se è stato configurato un criterio di seeding offline per lo stesso server o per un server diverso.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Verificare se il problema è dovuto a questa specifica causa radice

Per assicurarsi che l'errore sia causato dal [problema](#issue) precedente, eseguire uno dei passaggi seguenti:

#### <a name="step-1"></a>Passaggio 1

Controllare se viene visualizzato il messaggio di errore seguente nella console DPM/MAB al momento della configurazione del backup offline:

![Agente di Servizi di ripristino di Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Passaggio 2

1. Aprire la cartella **Temp** nel percorso di installazione (il percorso predefinito della cartella temporanea è *C:\Programmi\Agente servizi di ripristino di Microsoft Azure\Temp*. Cercare il file *CBUICurr* e aprirlo.
2. Nel file *CBUICurr* scorrere fino all'ultima riga e controllare se l'errore è "Unable to create an Azure AD application credential in customer's account. Eccezione: l'aggiornamento alle credenziali esistenti con KeyId \<some guid> non è consentito ".

### <a name="workaround"></a>Soluzione alternativa

Per risolvere questo problema, seguire questa procedura e riprovare a configurare i criteri.

1. Accedere alla pagina di accesso di Azure visualizzata nell'interfaccia utente del server DPM/MAB usando un account diverso con accesso amministrativo alla sottoscrizione in cui verrà creato il processo di importazione/esportazione.
2. Se nessun altro server ha il seeding offline configurato e nessun altro server dipende dall'applicazione `AzureOfflineBackup_<Azure User Id>`, eliminare questa applicazione dal **portale di Azure > Azure Active Directory > Registrazioni app**.

   > [!NOTE]
   > Controllare se nell'applicazione `AzureOfflineBackup_<Azure User Id>` non sono configurati altri seeding offline e anche nessun altro server dipende da questa applicazione. Passare a **impostazioni > chiavi** nella sezione chiavi pubbliche. Non devono essere aggiunte altre **chiavi pubbliche** . Come riferimento, vedere la schermata seguente:
   >
   > ![Chiavi pubbliche](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Passaggio 3

Dal server DPM/MAB si sta tentando di configurare il backup offline, eseguire le azioni seguenti:

1. Aprire la scheda **Manage computer certificate application** > **Personal** (Gestisci l'applicazione del certificato del computer > Personale) e cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`.
2. Selezionare il certificato precedente, fare clic con il pulsante destro del mouse su **tutte le attività** e su **Esporta** senza chiave privata nel formato CER.
3. Passare all'applicazione Backup offline di Azure citata nel **punto 2**. In **Impostazioni** > **Chiavi** > **Carica la chiave pubblica** caricare il certificato esportato nel passaggio precedente.

   ![Caricare le chiavi pubbliche](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Nel server aprire il registro di sistema digitando **Regedit** nella finestra **Esegui** .
5. Passare alla chiave *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* del Registro di sistema. Fare clic con il pulsante destro del mouse su **CloudBackupProvider** e aggiungere un nuovo valore stringa con il nome `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una di queste azioni:
    >
    >- In PowerShell connesso ad Azure eseguire il comando `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`.
    > - Passare al percorso del Registro di sistema `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` con il nome *CurrentUserId*.

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **Modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel **punto 2** e selezionare **OK**.
7. Per ottenere il valore dell'identificazione personale, fare doppio clic sul certificato, quindi selezionare **Dettagli** e scorrere verso il basso fino a visualizzare il campo Identificazione personale. Selezionare **identificazione personale** e copiare il valore.

   ![Valore identificazione personale](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Passaggi successivi

- [Seeding offline con disco personalizzato (uso del servizio Importazione/Esportazione di Azure)](backup-azure-backup-server-import-export.md)
