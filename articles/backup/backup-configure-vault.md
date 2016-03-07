<properties
	pageTitle="Preparare l'ambiente per il backup di un computer client o server Windows | Microsoft Azure"
	description="Preparare l'ambiente di backup di Windows creando un insieme di credenziali per il backup, scaricando le credenziale e installando l'agente di backup."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="insieme di credenziali per il backup; agente di backup; backup di Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Preparare l'ambiente per il backup di computer Windows in Azure
Questo articolo elenca le operazioni da effettuare per preparare l'ambiente per il backup di un computer Windows in Azure.

| Passaggio | Nome | Dettagli |
| :------: | ---- | ------- |
| 1 | [Creare un insieme di credenziali](#create-a-backup-vault) | Creare un insieme di credenziali nel [portale di gestione di Backup di Azure](http://manage.windowsazure.com) |
| 2 | [Scaricare le credenziali dell'insieme di credenziali](#download-the-vault-credential-file) | Scaricare le credenziali dell'insieme di credenziali che verranno usate per registrare il computer Windows con l'insieme di credenziali per il backup |
| 3 | [Installare l'agente di Backup di Azure ](#download-install-and-register-the-azure-backup-agent) | Installare l'agente e registrare il server nell'insieme di credenziali per il backup usando le credenziali dell'insieme di credenziali |

Se tutti i passaggi generali descritti sopra sono già stati eseguiti, è possibile avviare il [backup dei computer Windows](backup-azure-backup-windows-server.md). In caso contrario, seguire questa procedura dettagliata per assicurarsi che l'ambiente sia pronto.

## Prima di iniziare
Per preparare l'ambiente per il backup di computer Windows, è necessario un account Azure. Se non si ha un account, è possibile creare un [account di valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## Creare un insieme di credenziali per il backup
Per eseguire in Azure il backup di file e dati da un computer Windows o da Data Protection Manager oppure il backup di macchine virtuali IaaS, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui si vogliono archiviare i dati.

**Per creare un insieme di credenziali per il backup:**

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).

2. Fare clic su **Nuovo** -> **Servizi dati** -> **Servizi di ripristino** -> **Insieme di credenziali per il backup** e scegliere **Creazione rapida**.

    ![Crea insieme di credenziali](./media/backup-configure-vault/createvault1.png)

3. Per il parametro **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup. È necessario che il nome sia univoco per ogni sottoscrizione.

    Per il parametro **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. La scelta determina l'area geografica in cui vengono inviati i dati di backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

    Fare clic su **Crea insieme di credenziali** per completare il flusso di lavoro.

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale.

    ![Creazione dell'insieme di credenziali](./media/backup-configure-vault/creatingvault1.png)

    Dopo avere creato l'archivio di backup, verrà visualizzato un messaggio che indica che l'insieme di credenziali è stato creato correttamente. L'insieme di credenziali viene anche elencato nelle risorse di Servizi di ripristino come **Attivo**. ![Creazione dello stato dell'insieme di credenziali](./media/backup-configure-vault/backupvaultstatus1.png)

> [AZURE.IMPORTANT] È consigliabile identificare l'opzione per la ridondanza dell'archiviazione subito dopo la creazione dell'insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.
>
> **Per altre informazioni sulla scelta delle opzioni per la ridondanza dell'archiviazione, vedere questa [panoramica](backup-azure-storage-redundancy-options.md).**

## Scaricare il file delle credenziali di insieme
Per poter eseguire il backup dei dati in Azure, è necessario autenticare il server locale (Windows client, Windows Server o server SCDPM) con un insieme di credenziali per il backup. L'autenticazione viene eseguita mediante le "credenziali di insieme". Il file delle credenziali dell'insieme di credenziali viene scaricato tramite un canale sicuro dal Portale di Azure e il servizio Backup di Azure non è a conoscenza della chiave privata del certificato, che non è persistente nel portale o nel servizio.

Sono disponibili altre informazioni sull'[uso delle credenziali dell'insieme di credenziali per l'autenticazione con il servizio Backup di Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

**Per scaricare le credenziali dell'insieme di credenziali in un computer locale:**

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).

2. Fare clic su **Servizi di ripristino** nel riquadro di spostamento sinistro e selezionare l'insieme di credenziali per il backup creato.

3.  Fare clic sull'icona del cloud per aprire la visualizzazione *Avvio rapido* dell'insieme di credenziali per il backup.

    ![Anteprima](./media/backup-configure-vault/quickview.png)

4. Nella pagina **Avvio rapido** fare clic su **Scarica credenziali dell'insieme di credenziali**.

    ![Download](./media/backup-configure-vault/downloadvc.png)

    Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale. Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

    Il file delle credenziali dell'insieme di credenziali può essere scaricato dal portale.

5. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella Download dell'account locale. In alternativa, scegliere **Salva con nome** dal menu *Salva* per specificare un percorso per le credenziali di insieme.

    Non è necessario aprire ora le credenziali dell'insieme di credenziali.

    Assicurarsi che le credenziali dell'insieme di credenziali vengano salvate in un percorso accessibile dal computer. Se vengano archiviate in una condivisione file/SMB, verificare le autorizzazioni di accesso.

## Scaricare, installare e registrare l'agente backup di Azure
Dopo aver creato l'insieme di credenziali di Backup di Azure, è necessario installare un agente su tutti i computer Windows (Windows Server, client Windows, server System Center Data Protection Manager o computer del server di Backup di Azure) per eseguire il backup dei dati e delle applicazioni in Azure.

**Per scaricare, installare e registrare l'agente:**

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).

2. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup da registrare con un server.

3. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows > Salva**.

    ![Salva agente](./media/backup-configure-vault/agent.png)

4. Al termine del download di *MARSagentinstaller.exe*, fare clic su **Esegui** (o fare doppio clic su **MARSAgentInstaller.exe** dal percorso in cui è stato salvato). Scegliere la *cartella di installazione* e la *cartella della cache* necessarie per l'agente e fare clic su **Avanti**.

    Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.

    ![Cartella della cache](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password e fare clic su **Avanti**.

    Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non è già installato).

6. Dopo avere installato l'agente, fare clic su **Procedi alla registrazione** per continuare con il flusso di lavoro.

    ![Registra](./media/backup-configure-vault/register.png)

7. Nella schermata **Identificazione insieme di credenziali** trovare e selezionare il *file delle credenziali dell'insieme di credenziali* scaricato prima.

    ![Credenziali di insieme](./media/backup-configure-vault/vc.png)

    Il file delle credenziali dell'insieme di credenziali è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali dell'insieme di credenziali specificato è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.

    Verificare che il file delle credenziali dell'insieme di credenziali sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

    Se si verifica un errore di credenziali dell'insieme di credenziali non valide, ad esempio "Le credenziali dell'insieme di credenziali specificate non sono valide", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore in genere si verifica se l'utente fa clic sull'opzione *Scarica credenziali dell'insieme di credenziali* in rapida successione. In questo caso è valido solo l'ultimo file delle credenziali dell'insieme di credenziali.

8. Nella schermata **Impostazione crittografia** è possibile *generare* una passphrase o *fornire* una passphrase (almeno 16 caratteri). Ricordarsi di salvare la passphrase in un luogo sicuro.

    ![Crittografia](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.

9. Fare clic su **Finish**.

    Il computer ora risulta registrato nell'insieme di credenziali ed è possibile avviare il backup in Microsoft Azure.

## Passaggi successivi
- Iscriversi a una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/)
- [Eseguire il backup di un computer client o server Windows](backup-azure-backup-windows-server.md).
- In caso di domande, vedere [Domande frequenti su Backup di Azure](backup-azure-backup-faq.md).
- Visitare il [forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0224_2016-->