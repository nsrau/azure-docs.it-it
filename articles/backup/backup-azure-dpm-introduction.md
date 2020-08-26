---
title: Preparare il server DPM per il backup dei carichi di lavoro
description: Questo articolo illustra come preparare i backup di System Center Data Protection Manager (DPM) in Azure usando il servizio backup di Azure.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 96eadb4d600b6aa842cdbded2a906d70f27cf55e
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890808"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Preparare il backup dei carichi di lavoro in Azure con System Center DPM

Questo articolo illustra come preparare i backup di System Center Data Protection Manager, ovvero DPM, in Azure usando il servizio Backup di Azure.

Questo articolo include:

- Una panoramica della distribuzione DPM con Backup di Azure.
- Prerequisiti e limitazioni per l'uso di Backup di Azure con DPM.
- Procedura per la preparazione di Azure, che include la configurazione di un insieme di credenziali di backup di Servizi di ripristino e, facoltativamente, la modifica del tipo di archiviazione di Azure per l'insieme di credenziali.
- Procedura per la preparazione del server DPM, che include il download delle credenziali dell'insieme di credenziali, l'installazione dell'agente di Backup di Azure e la registrazione del server DPM nell'insieme di credenziali.
- Suggerimenti per la risoluzione di errori comuni.

## <a name="why-back-up-dpm-to-azure"></a>Perché eseguire il backup di DPM in Azure

[System Center DPM](/system-center/dpm/dpm-overview) esegue il backup dei dati di file e applicazioni. DPM interagisce con Backup di Azure nei modi seguenti:

- **DPM in esecuzione su un server fisico o una macchina virtuale locale** : è possibile eseguire il backup dei dati in un insieme di credenziali per il backup in Azure, oltre al backup su disco e su nastro.
- **DPM in esecuzione in una macchina virtuale di Azure** : da System Center 2012 R2 con aggiornamento 3 o versione successiva, è possibile distribuire DPM in una macchina virtuale di Azure. È possibile eseguire il backup dei dati nei dischi di Azure collegati alla macchina virtuale oppure usare Backup di Azure per eseguire il backup dei dati in un insieme di credenziali di backup.

Di seguito sono elencati i vantaggi aziendali derivanti dal backup dei server DPM in Azure:

- Per DPM locale, Backup di Azure offre un'alternativa alla distribuzione a lungo termine su nastro.
- Per DPM in esecuzione in una macchina virtuale di Azure, Backup di Azure consente l'offload dell'archiviazione dal disco di Azure. L'archiviazione dei dati meno recenti nell'insieme di credenziali di backup consente di aumentare le prestazioni dell'azienda archiviando i nuovi dati su disco.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

**Impostazione** | **Requisito**
--- | ---
DPM in una macchina virtuale di Azure | System Center 2012 R2 con aggiornamento cumulativo 3 o successivo di DPM 2012 R2.
DPM in un server fisico | System Center 2012 SP1 o versione successiva, System Center 2012 R2.
DPM su una macchina virtuale Hyper-V | System Center 2012 SP1 o versione successiva, System Center 2012 R2.
DPM in una macchina virtuale VMware | System Center 2012 R2 con aggiornamento cumulativo 5 o successivo.
Componenti | Nel server DPM devono essere installati Windows PowerShell e .NET Framework 4,5.
App supportate | [Informazioni](/system-center/dpm/dpm-protection-matrix) relative agli elementi di cui DPM può eseguire il backup.
Tipi di file supportati | Questi tipi di file possono essere sottoposti a backup con Backup di Azure:<br> <li>Crittografati (solo backup completi)<li> Compressi (backup incrementali supportati) <li> Sparse (backup incrementali supportati)<li> Compressi e sparse (considerati come sparse)
Tipi di file non supportati | <li>Server in file System con distinzione tra maiuscole e minuscole<li> collegamenti reali (ignorati)<li> reparse point (ignorati)<li> crittografati e compressi (ignorati)<li> crittografati e sparse (ignorati)<li> Flusso compresso<li> flusso di analisi
Archiviazione locale | Ogni computer di cui si desidera eseguire il backup deve disporre di spazio di archiviazione locale disponibile almeno il 5% delle dimensioni dei dati di cui viene eseguito il backup. Se, ad esempio, si esegue il backup di 100 GB di dati, è necessario un minimo di 5 GB di spazio disponibile nello spazio di lavoro.
Archiviazione dell'insieme di credenziali | Non esiste alcun limite alla quantità di dati di cui è possibile eseguire il backup in un insieme di credenziali di backup di Azure, ma le dimensioni di un'origine dati (ad esempio una macchina virtuale o un database) non devono superare 54.400 GB.
Azure ExpressRoute | È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.<br/><br/> **Con peering pubblico**: garantire l'accesso ai seguenti domini/indirizzi:<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **Con il peering Microsoft**selezionare i servizi/le aree e i valori della community pertinenti seguenti:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure area (in base alla posizione dell'insieme di credenziali di servizi di ripristino)<br/><br/>-Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)<br/><br/>Per ulteriori informazioni, vedere [ExpressRoute routing requirements](../expressroute/expressroute-routing.md).<br/><br/>**Nota**: il peering pubblico è deprecato per i nuovi circuiti.
Agente di Backup di Azure | Se DPM è in esecuzione in System Center 2012 SP1, installare il rollup 2 o successivo per DPM SP1. È necessario per l'installazione dell'agente.<br/><br/> Questo articolo descrive come distribuire la versione più recente dell'agente di Backup di Azure, noto anche come agente del servizio di ripristino di Microsoft Azure. Se è stata distribuita una versione precedente, eseguire l'aggiornamento alla versione più recente per garantire che il backup funzioni come previsto.

Prima di iniziare è necessario disporre di un account di Azure su cui è abilitata la funzionalità Backup di Azure. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificare le impostazioni di archiviazione

È possibile scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale.

- Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica.
- Se l'insieme di credenziali è il backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, usare la procedura seguente per configurare l'archiviazione con ridondanza locale.
- Informazioni sulle opzioni di archiviazione [di Azure](../storage/common/storage-redundancy.md), [con ridondanza geografica](../storage/common/storage-redundancy.md) e [con ridondanza locale](../storage/common/storage-redundancy.md).
- Modificare le impostazioni di archiviazione prima del backup iniziale. Se già stato eseguito il backup di un elemento, arrestare l'esecuzione del backup nell'insieme di credenziali prima di modificare le impostazioni di archiviazione.

Per modificare le impostazioni di replica di archiviazione:

1. Aprire il dashboard dell'insieme di credenziali.

2. In **Gestisci**selezionare **infrastruttura di backup**.

3. Nel menu **Configurazione di backup** selezionare un'opzione di archiviazione opzione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Scaricare le credenziali dell’insieme di credenziali

Usare le credenziali dell'insieme di credenziali quando si registra il server DPM nell'insieme di credenziali.

- Il file delle credenziali di insieme è un certificato generato dal portale per ogni insieme di credenziali per il backup.
- Il portale carica quindi la chiave pubblica nel Servizio di controllo di accesso (o ACS).
- Durante il flusso di lavoro di registrazione del computer, la chiave privata del certificato viene resa disponibile per l'utente, che autentica il computer.
- In base all'autenticazione, il servizio Backup di Azure invia i dati all'insieme di credenziali identificato.

### <a name="best-practices-for-vault-credentials"></a>Procedure consigliate per le credenziali dell'insieme di credenziali

Per ottenere le credenziali, scaricare il file dell'insieme di credenziali tramite un canale protetto dal portale di Azure:

- Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione.
- È responsabilità dell'utente garantire che il file delle credenziali dell'insieme di credenziali sia sicuro e non venga danneggiato.
  - Se si perde il controllo delle credenziali, le credenziali dell'insieme di credenziali possono essere usate per registrare altri computer nell'insieme di credenziali.
  - Tuttavia, i dati di backup vengono crittografati con una passphrase appartenente all'utente, quindi i dati di backup esistenti non possono essere compromessi.
- Verificare che il file venga salvato in un percorso accessibile dal server DPM. Se viene archiviato in una condivisione file/SMB, verificare le autorizzazioni di accesso.
- Le credenziali dell'insieme di credenziali scadono dopo 48 ore. È possibile scaricare le nuove credenziali dell'insieme di credenziali ogni volta che è necessario. Tuttavia, durante il flusso di lavoro di registrazione, è possibile usare solo il file di credenziali dell'insieme di credenziali più recente.
- Il servizio Backup di Azure non è a conoscenza della chiave privata del certificato e la chiave privata non è disponibile nel portale o nel servizio.

Scaricare le credenziali dell'insieme di credenziali in un computer locale come segue:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'insieme di credenziali in cui si desidera registrare il server DPM.
3. In **Impostazioni**selezionare **Proprietà**.

    ![Menu dell'insieme di credenziali aperto](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. In **Properties**  >  **backup Credentials**selezionare **download**. Il portale genera il file delle credenziali dell'insieme di credenziali usando una combinazione del nome dell'insieme di credenziali e della data corrente e lo rende disponibile per il download.

    ![Scaricare le credenziali](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Selezionare **Salva** per scaricare le credenziali dell'insieme di credenziali nella cartella o **Salva con nome** e specificare un percorso. La generazione del file potrebbe impiegare fino a un minuto.

## <a name="install-the-backup-agent"></a>Installare l'agente di backup

In ogni computer di cui viene eseguito il backup con Backup di Azure deve essere installato l'agente di backup, noto anche come agente del servizio di ripristino di Microsoft Azure (MARS). Installare l'agente nel server DPM come indicato di seguito:

1. Aprire l'insieme di credenziali in cui si desidera registrare il server DPM.
2. In **Impostazioni**selezionare **Proprietà**.

    ![Apri impostazioni insieme di credenziali](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Nella pagina **Proprietà** scaricare l'agente di Backup di Azure.

    ![Scarica](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Al termine del download eseguire MARSAgentInstaller.exe. per installare l'agente in un computer DPM.
5. Selezionare una cartella di installazione e una cartella della cache per l'agente. Lo spazio disponibile nel percorso della cache deve essere almeno il 5% dei dati di backup.
6. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.
7. Per completare l'installazione, l'agente di Backup di Azure installa .NET Framework 4.5 e Windows PowerShell, se non sono già installati.
8. Dopo aver installato l'agente, scegliere **Chiudi** per chiudere la finestra.

    ![Chiudi](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrare il server DPM nell'insieme di credenziali

1. Nella console amministrazione DPM > **gestione**selezionare **online**. Selezionare **Registra**. Verrà visualizzata la procedura guidata Registrazione del server.
2. In **Configurazione proxy** specificare le impostazioni proxy in base alle esigenze.

    ![Configurazione proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. In **Insieme di credenziali per il backup** individuare e selezionare il file delle credenziali dell'insieme di credenziali scaricato.

    ![Credenziali di insieme](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. In **Impostazione di limitazione** è facoltativamente possibile abilitare la limitazione della larghezza di banda per i backup. È possibile impostare i limiti di velocità per specificare i giorni e le ore di lavoro.

    ![Impostazione di limitazione](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. In **Recovery Folder Setting** (Impostazioni cartella di ripristino) specificare un percorso che può essere usato durante il ripristino dei dati.

    - Backup di Azure usa questo percorso come area temporanea di conservazione dei dati ripristinati.
    - Al termine del ripristino dei dati, Backup di Azure cancellerà i dati in quest'area.
    - Il percorso deve disporre di spazio sufficiente per contenere gli elementi che si prevede di ripristinare in parallelo.

    ![Impostazioni cartella di ripristino](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. In **impostazione crittografia**generare o fornire una passphrase.

    - La passphrase viene usata per crittografare i backup nel cloud.
    - Specificare almeno 16 caratteri.
    - Salvare il file in un percorso sicuro è necessario per il ripristino.

    ![Crittografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Si è proprietari della passphrase di crittografia e Microsoft non ha visibilità.
    > Se la passphrase viene persa o dimenticata, Microsoft non può aiutare a recuperare i dati di backup.

7. Selezionare **Register (registra** ) per registrare il server DPM nell'insieme di credenziali.

Dopo che il server è stato registrato correttamente nell'insieme di credenziali, è ora possibile iniziare a eseguire il backup Microsoft Azure. È necessario configurare il gruppo protezione dati nella console DPM per eseguire il backup dei carichi di lavoro in Azure. [Informazioni su come](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) distribuire i gruppi protezione dati.

## <a name="troubleshoot-vault-credentials"></a>Risolvere i problemi dell'insieme di credenziali

### <a name="expiration-error"></a>Errore di scadenza

Il file delle credenziali dell'insieme di credenziali è valido solo per 48 ore (dopo che è stato scaricato dal portale). Se si verifica un errore in questa schermata, ad esempio "il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare di nuovo il file dell'insieme di credenziali.

### <a name="access-error"></a>Errore di accesso

Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

### <a name="invalid-credentials-error"></a>Errore di credenziali non valide

Se si verifica un errore di credenziali dell'insieme di credenziali non valido, ad esempio "credenziali di insieme di credenziali non valide fornite", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino.

- Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale.
- Questo errore viene in genere visualizzato quando si seleziona l'opzione per il **download delle credenziali** dell'insieme di credenziali nel portale di Azure, due volte in rapida successione. In questo caso è valido solo il secondo file delle credenziali di insieme.
