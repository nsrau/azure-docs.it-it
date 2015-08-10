<properties
   pageTitle="Scaricare, installare e registrare l'agente Backup di Azure | Microsoft Azure"
   description="Informazioni su come e dove scaricare l'agente Backup di Azure, sulla procedura di installazione e su come registrare l'agente Backup di Azure usando le credenziali di insieme"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt\_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Scaricare, installare e registrare l'agente Backup di Azure
Dopo aver creato l'insieme di credenziali di Backup di Azure, è necessario installare un agente in ogni server locale (Windows Server, client Windows o server System Center Data Protection Manager) per eseguire il backup dei dati e dell'applicazione in Azure. Questo articolo illustra i passaggi necessari per configurare l'agente Backup di Azure in un computer che esegue Windows Server o un client Windows.

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).

2. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup da registrare con un server. Viene visualizzata la pagina Avvio rapido per quell'insieme di credenziali per il backup.

    ![Avvio rapido](./media/backup-azure-backup-download-register/quickstart.png)

3. Nella pagina Avvio rapido fare clic su **Per Windows Server, System Center Data Protection Manager o client Windows** in **Scarica agente**. Fare clic su **Salva** per copiarlo nel computer locale.

    ![Salva agente](./media/backup-azure-backup-download-register/agent.png)

4. Dopo aver installato l'agente, fare doppio clic su MARSAgentInstaller.exe per avviare l'installazione dell'agente Backup di Azure.

5. Scegliere la cartella di installazione e la cartella Scratch necessarie per l'agente. Il percorso della cache specificato deve disporre di uno spazio libero pari almeno al 5% dei dati di backup.

6.	Se si usa un server proxy per connettersi a Internet, nella schermata **Configurazione proxy** immettere i dettagli del server proxy. Se si usa un proxy autenticato, immettere il nome utente e la password in questa schermata.

7. Per completare l'installazione, l'agente Backup di Azure installerà .NET Framework 4.5 e Windows PowerShell (se non è già disponibile).

8.	Dopo aver installato l'agente, fare clic sul pulsante **Procedi alla registrazione** per continuare con il flusso di lavoro.

    ![Registra](./media/backup-azure-backup-download-register/register.png)

9. Nella schermata relativa alle credenziali di insieme individuare e selezionare il file delle credenziali di insieme scaricato in precedenza.

    ![Credenziali di insieme](./media/backup-azure-backup-download-register/vc.png)

    > [AZURE.NOTE]Il file delle credenziali di insieme è valido solo per 48 ore dopo che è stato scaricato dal portale. Se si verifica un errore in questa schermata, ad esempio "Il file delle credenziali di insieme fornito è scaduto", accedere al portale di Azure e scaricare nuovamente il file delle credenziali di insieme.

    Verificare che il file delle credenziali di insieme sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nel computer e ripetere l'operazione.

    Se si verifica un errore relativo alle credenziali di insieme non valide, ad esempio "Le credenziali di insieme fornite non sono valide". Il file è danneggiato o non dispone delle credenziali più recenti associate al servizio di ripristino", ripetere l'operazione dopo avere scaricato un nuovo file delle credenziali di insieme dal portale. Questo errore in genere si verifica se l'utente fa clic sull'opzione Scarica credenziali di insieme nel portale di Azure in rapida successione. In questo caso è valido solo il secondo file delle credenziali di insieme.

10. Nella schermata **Impostazioni crittografia** è possibile a generare o fornire una passphrase di almeno 16 caratteri, che dovrà essere salvata in un luogo sicuro.

    ![Crittografia](./media/backup-azure-backup-download-register/encryption.png)

    > [AZURE.WARNING]Se la passphrase viene persa o dimenticata, Microsoft non potrà fornire assistenza per il recupero dei dati di backup. L'utente finale possiede la passphrase di crittografia, che non è visibile a Microsoft. Salvare il file in un luogo sicuro, in quanto potrebbe essere necessario durante un'operazione di ripristino.

11. Dopo aver selezionato il pulsante **Fine**, il computer sarà registrato nell'insieme di credenziali e sarà possibile avviare il backup in Microsoft Azure. È possibile modificare le impostazioni specificate durante la registrazione del flusso di lavoro facendo clic sull'opzione **Modifica proprietà** nello snap-in di MMC di Backup di Azure.

    ![Modifica proprietà](./media/backup-azure-backup-download-register/change.png)

## Passaggi successivi
- Vedere[pianificare backup](backup-azure-backup-and-recover.md)per informazioni sul backup dei dati e applicazioni in Azure.

<!---HONumber=July15_HO5-->