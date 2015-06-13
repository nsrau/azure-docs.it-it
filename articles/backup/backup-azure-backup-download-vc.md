<properties
	pageTitle="Scaricare le credenziali dell'insieme di credenziali in Azure Backup"
	description="Informazioni su come utilizzare le credenziali dell'insieme di credenziali per l'autenticazione computer con l'archivio di Backup e il servizio di Backup di Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Utilizzando le credenziali dell'insieme di credenziali per l'autenticazione con il servizio di Backup di Azure

Il server locale (Windows client o server Windows Server o SCDPM) deve essere autenticato con un archivio di backup prima che è possibile eseguire il backup di dati in Azure. L'autenticazione viene eseguita mediante "archivio credenziali". Il concetto di credenziali dell'insieme di credenziali è simile al concetto di un file "impostazioni di pubblicazione" che viene utilizzato in Azure PowerShell.

## Che cos'è il file delle credenziali dell'insieme?

Il file delle credenziali è un certificato generato nel portale per ogni insieme di credenziali di backup. Il portale quindi carica la chiave pubblica per il servizio di controllo di accesso (o ACS). La chiave privata del certificato viene reso disponibile per l'utente come parte del flusso di lavoro fornito come input per il flusso di lavoro di registrazione di computer. Consente di autenticare il computer per l'invio di dati di backup a un insieme di credenziali identificato nel servizio di Backup di Azure.

È importante evidenziare che le credenziali dell'insieme di credenziali viene utilizzata solo durante il flusso di lavoro di registrazione. È responsabilità dell'utente per garantire che il file delle credenziali non venga compromesso. Se si trova nelle mani di qualsiasi utente non autorizzato, il file delle credenziali può essere utilizzato per registrare altre macchine con lo stesso archivio. Tuttavia, come i dati di backup viene crittografati utilizzando una passphrase che appartiene al cliente, dati di backup esistenti non possono essere compromessa. Per attenuare questo problema, le credenziali dell'insieme di credenziali è impostato di scadenza in 48hrs. È possibile scaricare le credenziali dell'insieme di credenziali di un archivio di backup un numero qualsiasi di volte, ma è applicabile solo il file più recente della credenziale insieme di credenziali durante il flusso di lavoro di registrazione.

## Scaricare il file di archivio delle credenziali

Il file di archivio delle credenziali viene scaricato tramite un canale protetto dal portale di Azure. Il servizio di Backup di Azure non è a conoscenza della chiave privata del certificato e la chiave privata non è persistente nel portale di o il servizio. Utilizzare la procedura seguente per scaricare le credenziali dell'insieme di credenziali in un computer locale.

1.  Accedi al [portale di gestione](https://manage.windowsazure.com/)
2.  Fare clic su **servizi di ripristino** nel riquadro di spostamento a sinistra e selezionare l'archivio di backup che è stato creato. Fare clic sull'icona del cloud per ottenere la visualizzazione avvio rapido di credenziali per il backup. <br/> ![Visualizzazione rapida][1]

3.  Nella pagina Quick Start fare clic su **credenziali archivio Download**. Il portale genera il file delle credenziali insieme di credenziali che è disponibile per il download. <br/> ![Download][2]

4.  Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale. Fare clic su **salvare** per scaricare le credenziali dell'insieme di credenziali nella cartella download dell'account locale o selezionare Salva con nome dal menu Salva per specificare un percorso per le credenziali dell'insieme di credenziali.

## Nota
+ A partire da marzo 2015, utenti non dispongono di un livello di programmazione (ad esempio: PowerShell) modo di scaricare le credenziali dell'insieme di credenziali.

+ Assicurarsi che le credenziali dell'insieme di credenziali viene salvato in un percorso accessibile dal computer. Se viene archiviato in un condivisione di file/SMB, controllare le autorizzazioni di accesso.

+ Il file delle credenziali viene utilizzato solo durante il flusso di lavoro di registrazione.

+ Il file delle credenziali scade dopo 48hrs e può essere scaricato dal portale.

+ Fare riferimento a Azure Backup [domande frequenti su](backup-azure-backup-faq.md) per domande sul flusso di lavoro.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-vc/quickview.png
[2]: ./media/backup-azure-backup-download-vc/downloadvc.png

<!---HONumber=GIT-SubDir--> 