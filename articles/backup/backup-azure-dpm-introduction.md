---
title: Usare DPM per il backup dei carichi di lavoro in Azure
description: Introduzione al backup dei dati DPM in un insieme di credenziali di servizi di ripristino di Azure.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, data protection manager, backup di dpm
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 2da5b04f56a5746fb77de6bc954bb5971eb4664b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885171"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparazione del backup dei carichi di lavoro in Azure con DPM
> [!div class="op_single_selector"]
> * [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Questo articolo illustra come eseguire il backup dei dati di System Center Data Protection Manager (DPM) in Azure. tra cui:

* Come eseguire il backup dei dati del server DPM in Azure
* I prerequisiti per eseguire un backup senza problemi
* Gli errori tipici rilevati e come gestirli
* Scenari supportati

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) esegue il backup dei dati di file e applicazioni. Altre informazioni sui carichi di lavoro supportati sono disponibili [qui](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). I dati sottoposti a backup su DPM possono essere archiviati su nastro, disco oppure sottoposti a backup in Azure usando il servizio Backup di Microsoft Azure. DPM interagisce con Backup di Azure nei modi seguenti:

* **DPM distribuito come server fisico o come macchina virtuale locale**: DPM distribuito come server fisico o come macchina virtuale Hyper-V locale, esegue il backup dei dati in un insieme di credenziali dei Servizi di ripristino oltre al backup su disco e su nastro.
* **DPM distribuito come macchina virtuale di Azure**: a partire dalla versione di System Center 2012 R2 con aggiornamento 3, è possibile distribuire DPM in una macchina virtuale di Azure. Se DPM è distribuito come macchina virtuale di Azure, è possibile eseguire il backup dei dati nei dischi di Azure associati alla VM oppure è possibile eseguire l'offload della risorsa di archiviazione dei dati eseguendo il backup in un insieme di credenziali di Servizi di ripristino.

## <a name="why-back-up-dpm-to-azure"></a>Perché eseguire il backup di DPM in Azure
Di seguito sono elencati i vantaggi aziendali derivanti dal backup dei server DPM in Azure:

* Per la distribuzione DPM locale, usare Azure come alternativa alla distribuzione a lungo termine su nastro.
* Per la distribuzione di DPM in una macchina virtuale in Azure, eseguire l'offload della risorsa di archiviazione dal disco di Azure. L'archiviazione dei dati meno recenti nell'insieme di credenziali di Servizi di ripristino consente di aumentare le prestazioni dell'azienda archiviando i nuovi dati su disco.

## <a name="prerequisites"></a>Prerequisiti
Di seguito viene descritto come preparare il servizio Backup di Azure all'esecuzione del backup dei dati DPM:

1. **Creare un insieme di credenziali di Servizi di ripristino** : creare un insieme di credenziali nel portale di Azure.
2. **Scaricare le credenziali dell'insieme di credenziali**: scaricare le credenziali usate per registrare il server DPM con l'insieme di credenziali di Servizi di ripristino.
3. **Installare l'agente di Backup di Azure**: installare l'agente in ogni server DPM.
4. **Registrare il server**: registrare il server DPM con l'insieme di credenziali di Servizi di ripristino.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Definizioni chiave
Ecco alcune definizioni chiave del backup in Azure per DPM:

1. **Credenziale dell'insieme di credenziali**: necessaria per eseguire l'autenticazione del computer per l'invio dei dati di backup a un insieme di credenziali identificato nel servizio Backup di Azure. Può essere scaricata dall'insieme di credenziali ed è valida per 48 ore.
2. **Passphrase**: viene usata per crittografare i backup nel cloud. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.
3. **PIN di sicurezza**: se le [Impostazioni di protezione](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) dell'insieme di credenziali sono state attivate, per eseguire le operazioni di backup critiche è necessario il PIN di sicurezza. Questa autenticazione a più fattori aggiunge un altro livello di sicurezza. 
4. **Cartella di ripristino**: è la posizione in cui vengono scaricati temporaneamente i backup dal cloud durante i ripristini cloud. Le dimensioni devono essere quasi uguali a quelle degli elementi di backup da ripristinare in parallelo.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Modifica della replica di archiviazione

La replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se l'insieme di credenziali è il backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, usare la procedura seguente per configurare l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/common/storage-redundancy-grs.md) e con [ridondanza locale](../storage/common/storage-redundancy-lrs.md), vedere la panoramica [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

> [!NOTE] 
> Configurare la replica archiviazione prima di attivare il backup iniziale. Se si decide di modificare la configurazione della replica archiviazione dopo il backup dell'elemento protetto, è necessario arrestare la protezione nell'insieme di credenziali prima di cambiare la configurazione della risorsa di archiviazione.
>  

1. Selezionare l'insieme di credenziali e aprire il relativo dashboard.

2. Nella sezione **Gestisci** fare clic su **Infrastruttura di backup**.

3. Nel menu **Configurazione di backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la macchina virtuale all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## <a name="download-vault-credentials"></a>Scaricare le credenziali dell’insieme di credenziali
Il file delle credenziali di insieme è un certificato generato dal portale per ogni insieme di credenziali per il backup. Il portale carica quindi la chiave pubblica nel Servizio di controllo di accesso (o ACS). Durante il flusso di lavoro di registrazione del computer, la chiave privata del certificato viene resa disponibile per l'utente, che autentica il computer. In base all'autenticazione, il servizio Backup di Azure invia i dati all'insieme di credenziali identificato.

Il file delle credenziali di insieme viene usato solo durante il flusso di lavoro di registrazione. È responsabilità dell'utente garantire che il file delle credenziali dell'insieme di credenziali non venga danneggiato. Se si perde il controllo delle credenziali, le credenziali dell'insieme di credenziali possono essere usate per registrare altri computer nell'insieme di credenziali. Poiché i dati di backup sono crittografati tramite una passphrase appartenente al cliente, i dati di backup esistenti non possono tuttavia essere compromessi. Per attenuare questo problema, le credenziali dell'insieme di credenziali scadono dopo 48 ore. Scaricare le nuove credenziali dell'insieme di credenziali ogni volta che è necessario. Tuttavia, durante il flusso di lavoro di registrazione, è possibile usare solo il file di credenziali dell'insieme di credenziali più recente.

Il file delle credenziali di insieme viene scaricato dal portale di Azure tramite un canale sicuro. Il servizio Backup di Azure non è a conoscenza della chiave privata del certificato e la chiave privata non è disponibile nel portale o nel servizio. Usare la procedura seguente per scaricare le credenziali di insieme in un computer locale.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Aprire l'insieme di credenziali di Servizi di ripristino che si vuole registrare in un server DPM.

3. Per impostazione predefinita si apre il menu Impostazioni. Se è chiuso, fare clic su **Impostazioni** nel dashboard dell'insieme di credenziali per aprire il menu. Nel menu **Impostazioni** fare clic su **Proprietà**.

    ![Menu dell'insieme di credenziali aperto](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Nella pagina delle proprietà in **Credenziali di backup** fare clic su **Scarica**. Il portale genera il file delle credenziali di insieme, che diventa disponibile per il download.

    ![Download](./media/backup-azure-dpm-introduction/vault-credentials.png)

Il portale genera una credenziale dell'insieme di credenziali insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella di download dell'account locale o selezionare Salva con nome dal menu Salva per specificare un percorso per le credenziali. La generazione del file potrebbe impiegare fino a un minuto.

### <a name="note"></a>Note
* Assicurarsi che il file delle credenziali dell'insieme di credenziali venga salvato in un percorso accessibile dal computer. Se vengano archiviate in una condivisione file/SMB, verificare le autorizzazioni di accesso.
* Il file delle credenziali di insieme viene usato solo durante il flusso di lavoro di registrazione.
* Il file delle credenziali di insieme scade dopo 48 ore e può essere scaricato dal portale.

## <a name="install-backup-agent"></a>Installare un agente di Backup
Dopo aver creato l'insieme di credenziali di Backup di Azure, è necessario installare un agente su tutti i computer Windows (Windows Server, client Windows, server System Center Data Protection Manager o computer del server di Backup di Azure) per eseguire il backup dei dati e delle applicazioni in Azure.

1. Aprire l'insieme di credenziali di Servizi di ripristino a cui si vuole registrare la macchina DPM.
2. Per impostazione predefinita si apre il menu Impostazioni. Se non viene visualizzato, fare clic su **Impostazioni** per aprire il menu delle impostazioni. Nel menu Impostazioni fare clic su **Proprietà**.

    ![Menu dell'insieme di credenziali aperto](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Nella pagina Impostazioni fare clic su **Scarica** in **Agente di Backup di Azure**.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Dopo aver scaricato l'agente, eseguire il file MARSAgentInstaller.exe per avviare l'installazione dell'agente di Backup di Azure. Scegliere la cartella di installazione e la cartella Scratch necessarie per l'agente. Il percorso della cache specificato deve disporre di uno spazio libero pari almeno al 5% dei dati di backup.

4. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.

5. Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non è già disponibile).

6. Dopo aver installato l'agente, scegliere **Chiudi** per chiudere la finestra.

   ![Chiudi](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Per **registrare il server DPM** nell'insieme di credenziali, fare clic su **Online** nella scheda **Gestione**. Selezionare quindi **Registra**. Verrà visualizzata la Registrazione guidata server.

8. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.

    ![Configurazione proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. Nella schermata relativa alle credenziali di insieme individuare e selezionare il file delle credenziali di insieme scaricato in precedenza.

    ![Credenziali di insieme](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Il file delle credenziali di insieme è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.

    Verificare che il file delle credenziali di insieme sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

    Se si verifica un errore di credenziali dell'insieme di credenziali non valide, ad esempio "Le credenziali dell'insieme di credenziali specificate non sono valide", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore in genere si verifica se l'utente fa clic sull'opzione **Scarica credenziali di insieme** nel portale di Azure in rapida successione. In questo caso è valido solo il secondo file delle credenziali di insieme.

10. Per controllare l'utilizzo della larghezza di banda di rete durante le ore lavorative e non lavorative, nella schermata **Impostazione di limitazione** è possibile impostare i limiti di utilizzo della larghezza di banda e definire le ore lavorative e non lavorative.

    ![Impostazione di limitazione](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Nella schermata **Impostazioni cartella di ripristino** cercare la cartella in cui verranno inseriti temporaneamente i file scaricati da Azure.

    ![Impostazioni cartella di ripristino](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Nella schermata **impostazione crittografia** , è possibile generare una passphrase o fornire una passphrase (almeno 16 caratteri). Ricordarsi di salvare la passphrase in un luogo sicuro.

    ![Crittografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.
    >
    >

13. Dopo aver fatto clic sul pulsante **Registra** , il computer sarà registrato nell'insieme di credenziali e si potrà avviare il backup in Microsoft Azure.

14. Quando si usa Data Protection Manager, è possibile modificare le impostazioni specificate durante il flusso di lavoro di registrazione facendo clic sull'opzione **Configura** selezionando **Online** nella scheda **Gestione**.

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
* DPM può essere eseguito come server fisico o come macchina virtuale Hyper-V installata in System Center 2012 SP1 o System Center 2012 R2. Inoltre, è possibile eseguirlo come macchina virtuale di Azure in System Center 2012 R2 (con almeno l'aggiornamento cumulativo 3 di DPM 2012 R2) oppure come macchina virtuale di Windows in VMware con System Center 2012 R2 e almeno il relativo aggiornamento cumulativo 5.
* Se DPM viene eseguito con System Center 2012 SP1, è necessario installare l'aggiornamento cumulativo 2 per System Center Data Protection Manager SP1. Tale procedura è necessaria prima di poter installare Azure Backup Agent.
* È necessario che nel server DPM siano installati Windows PowerShell e .Net Framework 4.5.
* DPM può eseguire il backup della maggior parte dei carichi di lavoro nel servizio Backup di Azure. Per visualizzare un elenco completo degli elementi supportati, consultare gli elementi supportati di Backup di Azure riportati di seguito.
* Usando l'opzione "Copia su nastro", non è possibile ripristinare i dati memorizzati in Backup di Azure.
* È necessario disporre di un account di Azure su cui è abilitata la funzionalità Backup di Azure. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
* Per usare Backup di Azure è necessario che Azure Backup Agent sia installato sui server da sottoporre a backup. Le dimensioni di ogni server devono essere almeno il 5% di quelle dei dati sottoposti a backup. Spazio disponibile come archiviazione locale. Se, ad esempio, si esegue il backup di 100 GB di dati, è necessario un minimo di 5 GB di spazio disponibile nello spazio di lavoro.
* I dati verranno memorizzati nell'archiviazione relativa all'insieme di credenziali di Azure. Non esistono limiti relativi alla quantità di dati che è possibile sottoporre a backup in un insieme di credenziali di Backup di Azure, tuttavia la dimensione dell'origine dati (ad esempio, un database o una macchina virtuale) non deve superare i 54400 GB.

Il backup in Azure è supportato per i tipi di file seguenti:

* Crittografati (solo backup completi)
* Compressi (backup incrementali supportati)
* Sparse (backup incrementali supportati)
* Compressi e sparse (considerati come sparse)

Questi tipi di file non sono supportati:

* I server nei file system che distinguono fra minuscole e maiuscole non sono supportati.
* Collegamenti reali (ignorati)
* Reparse point (ignorati)
* Crittografati e compressi (ignorati)
* Crittografati e sparse (ignorati)
* Flusso compresso
* Flusso di tipo sparse

> [!NOTE]
> A partire da System Center DPM 2012 con SP1 e versioni successive, è possibile eseguire il backup di carichi di lavoro protetti in Azure.
>
>
