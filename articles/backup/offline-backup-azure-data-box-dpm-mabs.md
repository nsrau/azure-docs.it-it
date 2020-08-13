---
title: Backup offline con Azure Data Box per DPM e MAB
description: È possibile utilizzare Azure Data Box per inizializzare i dati di backup iniziali offline da DPM e da MAB.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 8b585dc46eb2bdd54e48950ca861f0edc8f0a7ed
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88187043"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Seeding offline con Azure Data Box per DPM e MAB (anteprima)

> [!NOTE]
> Questa funzionalità è applicabile per Data Protection Manager (DPM) 2019 UR2 e versioni successive.<br><br>
> Questa funzionalità è attualmente disponibile in anteprima per Backup di Microsoft Azure Server (MAB). Se si è interessati all'uso di Azure Data Box per il seeding offline con MAB, contattare Microsoft all'indirizzo [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Questo articolo illustra come usare Azure Data Box per inizializzare i dati di backup iniziali offline da DPM e da MAB a un insieme di credenziali di servizi di ripristino di Azure.

È possibile usare [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) per inizializzare i backup DPM/MAB iniziali di grandi dimensioni offline (senza usare la rete) in un insieme di credenziali di servizi di ripristino. Questo processo consente di risparmiare tempo e larghezza di banda di rete che altrimenti verrebbero utilizzati per lo trasferimento di grandi quantità di dati di backup online su una rete ad alta latenza. Questa funzionalità è attualmente in anteprima.

Il backup offline basato su Azure Data Box offre due vantaggi distinti rispetto [al backup offline basato sul servizio importazione/esportazione di Azure](backup-azure-backup-server-import-export.md):

- Non è necessario acquistare dischi e connettori compatibili con Azure. Azure Data Box fornisce i dischi associati allo [SKU data box](https://azure.microsoft.com/services/databox/data/)selezionato.

- Backup di Azure (agente MARS) può scrivere direttamente i dati di backup sugli SKU supportati di Azure Data Box. Questa funzionalità Elimina la necessità di effettuare il provisioning di un percorso di gestione temporanea per i dati di backup iniziali. Non sono inoltre necessarie utilità per formattare e copiare i dati sui dischi.

## <a name="supported-platforms"></a>Piattaforme supportate

Sono supportate le piattaforme seguenti:

- Windows Server 2019 64 bit (standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Dimensioni dei dati di backup e SKU Data Box supportati

Sono supportati gli SKU Data Box seguenti:

| Dimensioni dei dati di backup (dopo la compressione da Marte) \* per server | SKU Azure Data Box supportati |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB e <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*I tassi di compressione tipici variano tra il 10-20% <br>
\*\*Contattare [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) se si prevede di avere più di 80 TB di dati di backup iniziali per una singola origine dati.

> [!IMPORTANT]
> I dati di backup iniziali di una singola origine dati devono essere contenuti all'interno di un singolo Azure Data Box o Azure Data Box disco e non possono essere condivisi tra più dispositivi dello stesso o di SKU diversi. Tuttavia, un Azure Data Box può contenere backup iniziali da più origini dati.

## <a name="before-you-begin"></a>Prima di iniziare

L'agente MARS in esecuzione su DPM/MAB deve essere aggiornato alla [versione più recente](https://aka.ms/azurebackup_agent) (2.0.9171.0 o versione successiva).

Verificare quanto segue:

### <a name="azure-subscription-and-required-permissions"></a>Sottoscrizione di Azure e autorizzazioni necessarie

- Una sottoscrizione di Azure valida.
- L'utente progettato per eseguire i criteri di backup non in linea deve essere un proprietario della sottoscrizione di Azure.
- Il processo Data Box e l'insieme di credenziali dei servizi di ripristino in cui è necessario eseguire il seeding dei dati devono essere disponibili nelle stesse sottoscrizioni.
    > [!NOTE]
    > È consigliabile che l'account di archiviazione di destinazione e l'insieme di credenziali di servizi di ripristino si trovino nella stessa area. Tuttavia, questa operazione non è obbligatoria.

### <a name="order-and-receive-the-data-box-device"></a>Ordinare e ricevere il dispositivo Data Box

Verificare che i dispositivi Data Box richiesti siano in stato *recapitato* prima di attivare il backup offline. Vedere le [dimensioni dei dati di backup e gli sku data box supportati](#backup-data-size-and-supported-data-box-skus) per ordinare lo SKU più adatto ai propri requisiti. Seguire la procedura descritta in [questo articolo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) per ordinare e ricevere i dispositivi data box.

> [!IMPORTANT]
> Non selezionare *BlobStorage* per il **tipo di account**. Il server DPM/MAB richiede un account che supporti i BLOB di pagine non supportati quando si seleziona *BlobStorage* . Selezionare **storage V2 (utilizzo generico v2)** come **tipo di account** quando si crea l'account di archiviazione di destinazione per il processo di Azure Data Box.

![Configurare Azure databox](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Configurare dispositivi Azure Data Box

Una volta ricevuto il dispositivo Azure Data Box, a seconda dello SKU Azure Data Box ordinato, eseguire i passaggi nelle sezioni appropriate di seguito per configurare e preparare i dispositivi Data Box per il server DPM/MAB per identificare e trasferire i dati di backup iniziali.

### <a name="setup-azure-data-box-disk"></a>Configurare il disco Azure Data Box

Se è stato ordinato uno o più dischi Azure Data Box (fino a 8 TB ciascuno), seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up) per decomprimere, connettere e sbloccare il disco data box.

> [!NOTE]
> È possibile che il server DPM/MAB non disponga di una porta USB. In uno scenario di questo tipo, è possibile connettere il disco Azure Data Box a un altro server/client ed esporre la radice del dispositivo come una condivisione di rete.

## <a name="setup-azure-data-box"></a>Azure Data Box di installazione

Se è stato ordinato un Azure Data Box (fino a 100 TB), seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) per configurare la data box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Montare il Azure Data Box come sistema locale

Il server DPM/MAB funziona nel contesto di sistema e pertanto richiede lo stesso livello di privilegio da fornire al percorso di montaggio in cui è connessa la Azure Data Box. Attenersi alla procedura seguente per assicurarsi di poter montare il dispositivo Data Box come sistema locale usando il protocollo NFS.

1. Abilitare la funzionalità client per NFS nel server DPM/MAB.
Specificare un'origine alternativa: *Wim: D: \sources\install.wim: 4*
2. Scaricare **PsExec** da [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) al server DPM/MAB.
3. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente con la directory che contiene *PSExec.exe* come directory corrente.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. La finestra di comando visualizzata come risultato del comando precedente si trova nel contesto del sistema locale. Usare questa finestra di comando per eseguire i passaggi per montare la condivisione BLOB di pagine di Azure come unità di rete in Windows Server.
5. Seguire questa procedura per connettere il server DPM/MAB al dispositivo Data Box tramite NFS ed eseguire il comando seguente nel prompt dei [comandi di sistema](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) locale per montare la condivisione BLOB di pagine di Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Una volta montati, controllare se è possibile accedere a X: dal server. Se possibile, continuare con la sezione successiva di questo articolo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Trasferire i dati di backup iniziali a dispositivi Azure Data Box

1. Nel server DPM/MAB, attenersi alla procedura per [creare un nuovo gruppo protezione](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019)dati. Se si aggiunge una protezione in linea al gruppo protezione dati esistente, fare clic con il pulsante destro del mouse sul gruppo protezione dati esistente, quindi scegliere **Aggiungi protezione dati online** e iniziare dal **passaggio 8**.
2. Nella pagina **Seleziona membri del gruppo** specificare i computer e le origini di cui si desidera eseguire il backup.
3. Nella pagina **Selezione metodo protezione dati** specificare come si desidera gestire il backup a breve e lungo termine. Assicurarsi di selezionare **protezione dati online.**

   ![Crea nuovo gruppo protezione dati](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Nella pagina **selezione obiettivi a breve termine** specificare come si vuole eseguire il backup sull'archiviazione a breve termine su disco.
5. Nella pagina **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.
6. Nella pagina **scelta del metodo** per la creazione della replica selezionare **automaticamente sulla rete.**
7. Nella pagina **Scegli opzioni di verifica coerenza** selezionare come si desidera automatizzare le verifiche della coerenza.
8. Nella pagina **specificare i dati per la protezione dati online** selezionare il membro che si desidera abilitare la protezione online.

    ![Specifica i dati da proteggere online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Nella pagina **specificare la pianificazione dei backup online** specificare la frequenza con cui devono essere eseguiti i backup incrementali in Azure.
10. Nella pagina **specificare i criteri di conservazione online** specificare come vengono conservati in Azure i punti di ripristino creati dai backup giornalieri/settimanali/mensili/annuali.
11. Nella schermata **scegliere la replica online** della procedura guidata scegliere l'opzione per il **trasferimento tramite dischi di proprietà di Microsoft** e selezionare **Avanti**.

    ![Scegliere la replica online iniziale](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > L'opzione per selezionare il **trasferimento tramite dischi di proprietà di Microsoft** non è disponibile per MAB V3 perché la funzionalità è in anteprima. [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)Se si vuole usare questa funzionalità per MAB V3, contattare Microsoft.

12. Accedere ad Azure quando richiesto, usando le credenziali utente con accesso proprietario nella sottoscrizione di Azure. Dopo aver eseguito l'accesso, viene visualizzata la schermata seguente:

    ![Dopo l'accesso riuscito](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     Il server DPM/MAB recupererà quindi i processi di Data Box nella sottoscrizione che si trovano nello stato *recapitato* .

     > [!NOTE]
     > Il primo accesso richiede più tempo del solito. Il modulo Azure PowerShell viene installato in background e anche l'applicazione Azure AD è registrata.
     >
     >  - Sono installati i moduli di PowerShell seguenti:<br>
          -AzureRM. profile *5.8.3*<br>
          -AzureRM. Resources *6.7.3*<br>
          -AzureRM. storage *5.2.0*<br>
          -Azure. storage *4.6.1*<br>
     >  - L'applicazione Azure AD viene registrata come *AzureOfflineBackup_ \<object GUID of the user> *.

13. Selezionare l'ordine di data box corretto per il quale è stato decompresso, connesso e sbloccato il disco Data Box. Selezionare **Avanti**.

    ![Selezionare il databox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Nella schermata **rileva il databox** immettere il percorso del dispositivo data box, quindi selezionare **rileva dispositivo**.

    ![Immettere il percorso di rete](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Consente di specificare il percorso di rete della directory radice del disco Azure Data Box. Questa directory deve contenere una directory con il nome *PageBlob* , come illustrato di seguito:
    >
    > ![Unità USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Ad esempio, se il percorso del disco è `\\mydomain\myserver\disk1\` e *disk1* contiene una directory denominata *PageBlob*, il percorso da fornire nella procedura guidata del server DPM/MAB è `\\mydomain\myserver\disk1\` .
    > Se si [configura un dispositivo Azure Data Box 100 TB](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box), fornire quanto segue come percorso di rete al dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Selezionare **Avanti**. Nella pagina **Riepilogo** verificare le impostazioni e selezionare **Crea gruppo**.

    ![Rileva databox](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    La schermata seguente conferma che il gruppo protezione dati è stato creato correttamente.

    ![Gruppo protezione dati creato](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Selezionare **Chiudi** nella schermata precedente.

    In questo modo, la replica iniziale dei dati viene eseguita nel disco di DPM/MAB. Al termine della protezione, lo stato del gruppo indicherà lo stato di protezione dati su **OK** nella pagina **protezione** .

17. Per avviare la copia di backup offline nel dispositivo Azure Data Box, fare clic con il pulsante destro del mouse sul **gruppo protezione**dati, quindi scegliere l'opzione **Crea punto di ripristino** . Scegliere quindi l'opzione **Protezione dati online**.

    ![Crea punto di ripristino](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Punto di ripristino](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   Il server DPM/MAB avvierà il backup dei dati selezionati nel dispositivo Azure Data Box. Questa operazione potrebbe richiedere da diverse ore a pochi giorni, a seconda delle dimensioni dei dati e della velocità di connessione tra il server DPM/MAB e il Azure Data Box Disk.

   È possibile monitorare lo stato del processo nel riquadro **monitoraggio** . Al termine del backup dei dati, verrà visualizzata una schermata simile a quella riportata di seguito:

   ![Console di amministrazione](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Passaggi successivi al backup

Eseguire questi passaggi una volta che il backup dei dati per il Azure Data Box Disk ha avuto esito positivo.

- Seguire la procedura descritta in [questo articolo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) per distribuire il disco Azure Data Box ad Azure. Se è stato usato un dispositivo Azure Data Box 100 TB, seguire [questa procedura](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) per spedire il Azure Data Box ad Azure.
- [Monitorare il processo di data box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) nel portale di Azure. Al *termine*del processo di Azure Data Box, il server DPM/MAB sposta automaticamente i dati dall'account di archiviazione all'insieme di credenziali di servizi di ripristino al momento del backup pianificato successivo. Il processo di backup verrà quindi contrassegnato come *processo completato* se un punto di ripristino viene creato correttamente.

  > [!NOTE]
  > Il server DPM/MAB attiva i backup nelle ore pianificate durante la creazione del gruppo protezione dati. Tuttavia, questi processi verranno flag *in attesa del completamento di Azure Data Box processo* fino al completamento del processo.

- Al termine della creazione di un punto di ripristino corrispondente al backup iniziale da parte del server DPM/MAB, è possibile eliminare l'account di archiviazione (o il contenuto specifico) associato al processo di Azure Data Box.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'agente di Backup di Microsoft Azure (MAB) nel server DPM crea automaticamente un'applicazione Azure AD nel tenant. Questa applicazione richiede un certificato per l'autenticazione che viene creato e caricato quando si configurano i criteri di seeding offline.

Per la creazione e il caricamento del certificato nell'applicazione Azure AD vengono usati Azure PowerShell.

### <a name="issue"></a>Problema

Al momento della configurazione del backup offline, a causa di un difetto del codice noto nel cmdlet Azure PowerShell non è possibile aggiungere più certificati alla stessa applicazione Azure AD creata dall'agente MAB. Ciò avrà un effetto se è stato configurato un criterio di seeding offline per lo stesso server o per un server diverso.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Verificare se il problema è causato da questa specifica causa radice

Per assicurarsi che l'errore sia dovuto al [problema](#issue) precedente, eseguire uno dei passaggi seguenti:

#### <a name="step-1"></a>Passaggio 1

Controllare se viene visualizzato il messaggio di errore seguente nella console DPM/MAB al momento della configurazione del backup offline:

![Agente di servizi di ripristino di Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Passaggio 2

1. Aprire la cartella **Temp** nel percorso di installazione (il percorso predefinito della cartella temporanea è *C:\Programmi\Microsoft Azure Recovery Services Agent\Temp*. Cercare il file *CBUICurr* e aprire il file.
2. Nel file *CBUICurr* scorrere fino all'ultima riga e verificare se l'errore è dovuto a "non è possibile creare una Azure ad credenziali dell'applicazione nell'account del cliente. Eccezione: l'aggiornamento alle credenziali esistenti con KeyId \<some guid> non è consentito ".

### <a name="workaround"></a>Soluzione alternativa

Per risolvere questo problema, eseguire la procedura seguente e riprovare la configurazione dei criteri.

1. Accedere alla pagina di accesso di Azure visualizzata nell'interfaccia utente del server DPM/MAB usando un account diverso con accesso amministrativo alla sottoscrizione in cui verrà creato il processo di importazione/esportazione.
2. Se nessun altro server dispone di un seeding offline configurato e nessun altro server dipende dall' `AzureOfflineBackup_<Azure User Id>` applicazione, eliminare l'applicazione da **portale di Azure > Azure Active Directory > registrazioni app**.

   > [!NOTE]
   > Controllare se per l'applicazione `AzureOfflineBackup_<Azure User Id>` non sono configurati altri seeding offline e anche nessun altro server dipende da questa applicazione. Passare a **impostazioni > chiavi** sotto la sezione chiavi pubbliche per aggiungere altre **chiavi pubbliche** . Per informazioni di riferimento, vedere la schermata seguente:
   >
   > ![Chiavi pubbliche](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Passaggio 3

Dal server DPM/MAB si sta tentando di configurare il backup offline, eseguire le azioni seguenti:

1. Aprire la scheda **Gestisci applicazione certificato computer**  >  **personale** e cercare il certificato con il nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Selezionare il certificato precedente, fare clic con il pulsante destro del mouse su **tutte le attività** e su **Esporta** senza chiave privata nel formato CER.
3. Passare all'applicazione di backup offline di Azure indicata nel **punto 2**. In **Impostazioni**  >  **chiavi**  >  **carica chiave pubblica** caricare il certificato esportato nel passaggio precedente.

   ![Carica chiavi pubbliche](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Nel server aprire il registro di sistema digitando **Regedit** nella finestra **Esegui** .
5. Passare al registro di sistema *Computer\HKEY \_ Local \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Fare clic con il pulsante destro del mouse su **CloudBackupProvider** e aggiungere un nuovo valore stringa con nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Per ottenere l'ID utente di Azure, eseguire una delle operazioni seguenti:
    >
    >- Da PowerShell connesso ad Azure, eseguire il `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` comando.
    > - Passare al percorso del registro di sistema `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` con il nome *CurrentUserId*.

6. Fare clic con il pulsante destro del mouse sulla stringa aggiunta nel passaggio precedente e scegliere **modifica**. Nel valore specificare l'identificazione personale del certificato esportato nel **punto 2** e selezionare **OK**.
7. Per ottenere il valore di identificazione personale, fare doppio clic sul certificato, quindi selezionare **Details (dettagli** ) e scorrere verso il basso fino a visualizzare il campo identificazione personale. Selezionare **identificazione personale** e copiare il valore.

   ![Certificato](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Passaggi successivi

- [Seeding offline usando un disco personalizzato (tramite il servizio importazione/esportazione di Azure)](backup-azure-backup-server-import-export.md)
