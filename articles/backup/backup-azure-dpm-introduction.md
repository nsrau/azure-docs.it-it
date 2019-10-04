---
title: Preparare il server DPM per eseguire il backup dei carichi di lavoro in Azure
description: Introduzione al backup dei dati DPM in un insieme di credenziali di servizi di ripristino di Azure.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 71070a778e54e51cdb528041f746489bb64e979c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954718"
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

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) esegue il backup dei dati di file e applicazioni. DPM interagisce con Backup di Azure nei modi seguenti:

* **Se DPM viene eseguito in un server fisico o in una macchina virtuale locale**, è possibile eseguire il backup dei dati in un insieme di credenziali di backup in Azure, oltre al backup su disco e nastro.
* **Se DPM viene eseguito in una macchina virtuale di Azure**, da System Center 2012 R2 con aggiornamento 3 o successivo, è possibile distribuire DPM in una macchina virtuale di Azure. È possibile eseguire il backup dei dati nei dischi di Azure collegati alla macchina virtuale oppure usare Backup di Azure per eseguire il backup dei dati in un insieme di credenziali di backup.

Di seguito sono elencati i vantaggi aziendali derivanti dal backup dei server DPM in Azure:

* Per DPM locale, Backup di Azure offre un'alternativa alla distribuzione a lungo termine su nastro.
* Per DPM in esecuzione in una macchina virtuale di Azure, Backup di Azure consente l'offload dell'archiviazione dal disco di Azure. L'archiviazione dei dati meno recenti nell'insieme di credenziali di backup consente di aumentare le prestazioni dell'azienda archiviando i nuovi dati su disco.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limitazioni

**Impostazione** | **Requisito**
--- | ---
DPM in una macchina virtuale di Azure | System Center 2012 R2 con aggiornamento cumulativo 3 o successivo di DPM 2012 R2.
DPM in un server fisico | System Center 2012 SP1 o versione successiva, System Center 2012 R2.
DPM su una macchina virtuale Hyper-V | System Center 2012 SP1 o versione successiva, System Center 2012 R2.
DPM in una macchina virtuale VMware | System Center 2012 R2 con aggiornamento cumulativo 5 o successivo.
Componenti | Nel server DPM devono essere installati Windows PowerShell e .NET Framework 4,5.
App supportate | [Informazioni](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) relative agli elementi di cui DPM può eseguire il backup.
Tipi di file supportati | Questi tipi di file possono essere sottoposti a backup con Backup di Azure: crittografati, solo backup completi; compressi, backup incrementali supportati; sparse, backup incrementali supportati; compressi e sparse considerati come sparse.
Tipi di file non supportati | Server nei file system che distinguono tra maiuscole e minuscole; collegamenti reali, ignorati; reparse point, ignorati; crittografati e compressi, ignorati; crittografati e sparse, ignorati; flusso compresso; analisi di flusso.
Archiviazione locale | Le dimensioni dello spazio di archiviazione disponibile di ogni computer di cui si desidera eseguire il backup devono essere almeno il 5% di quelle dei dati sottoposti a backup. Se, ad esempio, si esegue il backup di 100 GB di dati, è necessario un minimo di 5 GB di spazio disponibile nello spazio di lavoro.
Archiviazione dell'insieme di credenziali | Non esistono limiti relativi alla quantità di dati che è possibile includere nel backup in un insieme di credenziali di Backup di Azure, tuttavia le dimensioni dell'origine dati, ad esempio, di un database o una macchina virtuale, non devono superare 54.400 GB.
Azure ExpressRoute | Se Azure ExpressRoute è configurato con peering privato o Microsoft, non può essere usato per eseguire il backup dei dati in Azure.<br/><br/> Se Azure ExpressRoute è configurato con il peering pubblico, può essere usato per eseguire il backup dei dati in Azure.<br/><br/> **Nota:** Il peering pubblico è deprecato per i nuovi circuiti.
Agente di Backup di Azure | Se DPM è in esecuzione in System Center 2012 SP1, installare il rollup 2 o successivo per DPM SP1. È necessario per l'installazione dell'agente.<br/><br/> Questo articolo descrive come distribuire la versione più recente dell'agente di Backup di Azure, noto anche come agente del servizio di ripristino di Microsoft Azure. Se è stata distribuita una versione precedente, eseguire l'aggiornamento alla versione più recente per garantire che il backup funzioni come previsto.

Prima di iniziare è necessario disporre di un account di Azure su cui è abilitata la funzionalità Backup di Azure. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificare le impostazioni di archiviazione

È possibile scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale.

- Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica.
- Se l'insieme di credenziali è il backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, usare la procedura seguente per configurare l'archiviazione con ridondanza locale.
- Informazioni sulle opzioni di archiviazione [di Azure](../storage/common/storage-redundancy.md), [con ridondanza geografica](../storage/common/storage-redundancy-grs.md) e [con ridondanza locale](../storage/common/storage-redundancy-lrs.md).
- Modificare le impostazioni di archiviazione prima del backup iniziale. Se già stato eseguito il backup di un elemento, arrestare l'esecuzione del backup nell'insieme di credenziali prima di modificare le impostazioni di archiviazione.

Per modificare le impostazioni di replica di archiviazione:

1. Aprire il dashboard dell'insieme di credenziali.

2. In **Gestione** fare clic su **Infrastruttura di backup**.

3. Nel menu **Configurazione di backup** selezionare un'opzione di archiviazione opzione per l'insieme di credenziali.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Scarica credenziali dell'insieme di credenziali

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
    - Poiché i dati di backup sono crittografati tramite una passphrase appartenente al cliente, i dati di backup esistenti non possono tuttavia essere compromessi.
- Verificare che il file venga salvato in un percorso accessibile dal server DPM. Se vengano archiviate in una condivisione file/SMB, verificare le autorizzazioni di accesso.
- Le credenziali dell'insieme di credenziali scadono dopo 48 ore. È possibile scaricare le nuove credenziali dell'insieme di credenziali ogni volta che è necessario. Tuttavia, durante il flusso di lavoro di registrazione, è possibile usare solo il file di credenziali dell'insieme di credenziali più recente.
- Il servizio Backup di Azure non è a conoscenza della chiave privata del certificato e la chiave privata non è disponibile nel portale o nel servizio.

Scaricare le credenziali dell'insieme di credenziali in un computer locale come segue:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'insieme di credenziali in cui si desidera registrare il server DPM.
3. In **Impostazioni** fare clic su **Proprietà**.

    ![Menu dell'insieme di credenziali aperto](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. In **Proprietà** > **Credenziali di backup** fare clic su **Scarica**. Il portale genera il file delle credenziali dell'insieme di credenziali usando una combinazione del nome dell'insieme di credenziali e della data corrente e lo rende disponibile per il download.

    ![Download](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Fare clic su **Salva** per scaricare le credenziali dell'insieme di credenziali nella cartella o su **Salva con nome** per specificare un percorso. La generazione del file potrebbe impiegare fino a un minuto.


## <a name="install-the-backup-agent"></a>Installare l'agente di backup

In ogni computer di cui viene eseguito il backup con Backup di Azure deve essere installato l'agente di backup, noto anche come agente del servizio di ripristino di Microsoft Azure (MARS). Installare l'agente nel server DPM come indicato di seguito:

1. Aprire l'insieme di credenziali in cui si desidera registrare il server DPM.
2. In **Impostazioni** fare clic su **Proprietà**.

    ![Menu dell'insieme di credenziali aperto](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Nella pagina **Proprietà** scaricare l'agente di Backup di Azure.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. Al termine del download eseguire MARSAgentInstaller.exe. per installare l'agente in un computer DPM.
5. Selezionare una cartella di installazione e una cartella della cache per l'agente. Lo spazio disponibile nel percorso della cache deve essere almeno il 5% dei dati di backup.
6. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.
7. Per completare l'installazione, l'agente di Backup di Azure installa .NET Framework 4.5 e Windows PowerShell, se non sono già installati.
8. Dopo aver installato l'agente, scegliere **Chiudi** per chiudere la finestra.

    ![Chiudi](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrare il server DPM nell'insieme di credenziali

1. Nella console dell'amministrazione DPM > **Gestione** fare clic su **Online**. Selezionare **Registra**. Verrà visualizzata la procedura guidata Registrazione del server.
2. In **Configurazione proxy** specificare le impostazioni proxy in base alle esigenze.

    ![Configurazione proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. In **Insieme di credenziali per il backup** individuare e selezionare il file delle credenziali dell'insieme di credenziali scaricato.

    ![Credenziali dell'insieme di credenziali](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. In **Impostazione di limitazione** è facoltativamente possibile abilitare la limitazione della larghezza di banda per i backup. È possibile impostare i limiti di velocità per specificare i giorni e le ore di lavoro.

    ![Impostazione di limitazione](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. In **Recovery Folder Setting** (Impostazioni cartella di ripristino) specificare un percorso che può essere usato durante il ripristino dei dati.

    - Backup di Azure usa questo percorso come area temporanea di conservazione dei dati ripristinati.
    - Al termine del ripristino dei dati, Backup di Azure cancellerà i dati in quest'area.
    - Il percorso deve avere spazio sufficiente per contenere gli elementi di cui si anticipa il ripristino in parallelo.

    ![Impostazioni cartella di ripristino](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. In **Impostazione crittografia** generare o specificare una passphrase.

    - La passphrase viene usata per crittografare i backup nel cloud.
    - Specificare almeno 16 caratteri.
    - Salvare il file in un percorso sicuro è necessario per il ripristino.

    ![Crittografia](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > L'utente è proprietario della passphrase di crittografia e Microsoft non può visualizzarla.
    > Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup.

13. Fare clic su **Registra** per registrare il server DPM nell'insieme di credenziali.

Dopo aver registrato correttamente il server nell'insieme di credenziali sarà possibile avviare il backup in Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Risolvere i problemi dell'insieme di credenziali

### <a name="expiration-error"></a>Errore di scadenza

Il file delle credenziali di insieme è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.

### <a name="access-error"></a>Errore di accesso

Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

### <a name="invalid-credentials-error"></a>Errore di credenziali non valide

Se si verifica un errore di credenziali dell'insieme di credenziali non valide, ad esempio "Le credenziali dell'insieme di credenziali specificate non sono valide", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino.

- Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale.
- Questo errore in genere si verifica quando l'utente fa clic sull'opzione **Download vault credential** (Scarica credenziali dell'insieme di credenziali) nel portale di Azure due volte di seguito. In questo caso è valido solo il secondo file delle credenziali di insieme.
