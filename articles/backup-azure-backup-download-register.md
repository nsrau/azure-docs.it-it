<properties
	pageTitle="Scaricare, installare e registrare Azure Backup agent"
	description="Informazioni su come e dove per scaricare l'agente Azure Backup, passaggi di installazione e come registrare l'agente Azure Backup utilizzando le credenziali dell'insieme di credenziali"
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
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Scaricare, installare e registrare l'agente Azure Backup

Dopo aver creato l'archivio di Backup di Azure, deve essere installato un agente in ogni server locale (Windows Server, Windows client o server di System Center Data Protection Manager) che consente di eseguire il backup dei dati e l'applicazione in Azure. In questo articolo vengono illustrati i passaggi necessari per configurare l'agente Azure Backup su un Server di Windows o un computer client Windows.

1. Accedi al [portale di gestione](https://manage.windowsazure.com/)

2. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup che si desidera registrare con un server. Verrà visualizzata la pagina avvio rapido per tale archivio di backup. <br/> ![Avvio rapido][1]

3. Nella pagina Quick Start fare clic su **per Windows Server o client di System Center Data Protection Manager o Windows** in **Scarica agente** opzione. Fare clic su Salva per copiarlo nel computer locale. <br/> ![Salvare agente][2]

4. Una volta installato l'agente, fare doppio clic su MARSAgentInstaller.exe per avviare l'installazione dell'agente di Backup di Azure. Scegliere la cartella di installazione e la cartella file temporaneo necessari per l'agente. Il percorso della cache specificato deve disporre di spazio libero almeno il 5% dei dati di backup.

5.	Se si utilizza un server proxy per connettersi a internet, nel **configurazione Proxy** immettere i dettagli del server proxy. Se si utilizza un proxy autenticato, immettere i dettagli di nome e la password dell'utente in questa schermata.

6.	L'agente Azure Backup installazione di .NET Framework 4.5 e Windows PowerShell (se non è già disponibile) per completare l'installazione.

7.	Una volta installato l'agente, fare clic sui **procedere alla registrazione** per continuare con il flusso di lavoro. <br/> ![Registra][3]

8. Nella schermata credenziali insieme di credenziali, individuare e selezionare il file delle credenziali che è stato scaricato in precedenza. <br/> ![Credenziali dell'insieme di credenziali][4] <br/> <br/>
> [AZURE.NOTE]Il file delle credenziali è valido solo per 48 ore (dopo che è stato scaricato dal portale). Se si verifica un errore in questo account di accesso (ad esempio "file delle credenziali fornito è scaduto"), schermata al portale di Azure e scarica nuovamente il file delle credenziali.
>
> Assicurarsi che il file delle credenziali è disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori correlati di accesso, copiare il file delle credenziali in un percorso temporaneo nel computer e ripetere l'operazione.
>
> Se si verifica un errore di credenziali dell'insieme di credenziali non valido (ad esempio "fornite le credenziali insieme di credenziali non valido". Il file è danneggiato o non sono quelle più recenti associato il servizio di ripristino", ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore si verifica in genere se l'utente fa clic sull'opzione Download archivio credenziali nel portale di Azure, in rapida successione. In questo caso, solo il secondo file di credenziali di insieme di credenziali è valido.

9. Nel **impostazione crittografia** dello schermo, è possibile a generare una passphrase o fornire una passphrase (almeno 16 caratteri) e ricordare di salvare la passphrase in un luogo sicuro. <br/> ![Crittografia][5] <br/> <br/>
> [AZURE.WARNING]Se la passphrase viene persa o dimenticata; Microsoft non consente di recuperare i dati di backup. L'utente finale possiede la passphrase di crittografia e Microsoft non dispone di qualsiasi visibilità la passphrase utilizzata dall'utente finale. Salvare il file in un luogo sicuro come sarebbe necessario durante un'operazione di ripristino.

10. Dopo aver selezionato il **Fine** pulsante, il computer è registrato correttamente nell'insieme di credenziali ed è ora pronta per l'avvio del backup in Microsoft Azure. È possibile modificare le impostazioni specificate durante il flusso di lavoro di registrazione facendo clic sui **modificare le proprietà** opzione in Azure Backup mmc snap in <br/> ![Modifica delle proprietà][6]

## Passaggi successivi
+ Fare riferimento a installazione e configurazione di Azure Backup [domande frequenti su](backup-azure-backup-faq.md) per domande sul flusso di lavoro.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir-->