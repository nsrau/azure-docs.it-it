<properties 
	pageTitle="Configurare i Servizi di backup di Azure per eseguire il backup di Windows Server in modo semplice e rapido" 
	description="Usare questa esercitazione per apprendere come usare il servizio di backup nell'offerta cloud di Microsoft Azure per eseguire il backup di Windows Server nel cloud." 
	services="backup" 
	documentationCenter="" 
	authors="markgalioto" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="markgal"/>

#Configurare il servizio Backup di Azure per il backup semplice e rapido di Windows Server

Per completare l'esercitazione, è necessario un account Azure. Questa esercitazione illustra la procedura per abilitare la funzionalità Backup di Azure.
>[AZURE.NOTE]In precedenza era necessario creare o acquisire un certificato X.509 v3 per registrare il server di backup. I certificati sono ancora supportati, ma per facilitare la registrazione dell'insieme di credenziali di Azure con un server è possibile generare una credenziale di insieme direttamente dalla pagina Avvio rapido. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

Per eseguire il backup di file e dati da Windows Server ad Azure, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui archiviare i dati. In questa esercitazione verranno illustrate:

- la creazione dell'insieme di credenziali utilizzare per archiviare i backup
- download di una credenziale di insieme di credenziali
- l'installazione di un agente di backup
- una panoramica delle attività di gestione di backup disponibili tramite il portale di gestione

##Creare un insieme di credenziali per il backup

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **nuova** > **Data Services** > **servizi di ripristino** > **insieme di credenziali di Backup** > **Quick Create**.
3. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali di backup.
4. In **Region** selezionare l'area geografica per l'insieme di credenziali per il backup.

    ![New backup vault](./media/backup-configure-vault/RS_newbackupvault.png)

5. Fare clic su **Crea insieme di credenziali**.

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Dopo aver creato l'archivio di backup, un messaggio indicherà l'insieme di credenziali è stato creato correttamente e verrà elencato nelle risorse per i servizi di ripristino come **Active**.

    ![Creazione dell'insieme di credenziali per il backup](./media/backup-configure-vault/RS_backupvaultcreation.png)

6. Se sono disponibili più sottoscrizioni associate al proprio account aziendale, scegliere l'account corretto per associare l'insieme di credenziali per il backup.

##Scaricare un insieme di credenziali

Le credenziali di insieme sostituiscono i certificati come metodo di registrazione del servizio Azure con il proprio server. È ancora possibile usare i certificati, ma le credenziali di insieme sono più facili da usare perché vengono generate e scaricate dal portale di Azure.

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup che si desidera registrare con un server. Viene visualizzata la pagina Avvio rapido per quell'insieme di credenziali per il backup.
3. Nel **pagina Quick Start**, fare clic su **credenziali archivio Download** per richiedere il portale per generare e scaricare le credenziali dell'insieme di credenziali si utilizzerà per registrare il server con credenziali per il backup.

    Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale.

4. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella Download dell'account locale. In alternativa, scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per le credenziali di insieme. Poiché non è possibile modificare le credenziali di insieme, non vi sono motivi per fare clic su Apri. Una volta scaricate le credenziali, verrà richiesto se si vuole aprire la cartella. Fare clic su **x** per chiudere il menu.

##Scaricare e installare un agente di backup

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Servizi di ripristino**, quindi selezionare un insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.
3. Nella pagina Avvio rapido selezionare il tipo di agente che si desidera scaricare. È possibile scegliere **Download Azure Backup Agent**, **Windows Server and System Center Data Protection Manager** o **Windows Server Essentials**. Per altre informazioni, vedere:

	* [Installare l'agente Azure Backup per Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/library/hh831761.aspx#BKMK_installagent)
	* [Installare l'agente Azure Backup per Windows Server 2012 Essentials](http://technet.microsoft.com/library/jj884318.aspx)

Dopo l'installazione dell'agente sarà possibile usare l'interfaccia di gestione locale appropriata, ad esempio lo snap-in di Microsoft Management Console, la console System Center Data Protection Manager o il dashboard di Windows Server Essentials, per configurare i criteri di backup per il server.

##Gestire server e insiemi di credenziali per il backup

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Servizi di ripristino**, quindi fare clic sul nome dell'insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.
3. Fare clic su **Dashboard** per visualizzare la panoramica sull'utilizzo del server. Nella parte inferiore del dashboard è possibile eseguire le operazioni seguenti:

    - **Manage certificate**. Se per registrare un server è stato usato un certificato, eseguire questa azione per aggiornare il certificato corrente. Se si usano le credenziali di insieme, non usare la funzionalità **Gestisci certificato**.
    - **Delete**. Consente di eliminare l'insieme di credenziali per il backup corrente. Se un insieme di credenziali per il backup non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. L'opzione di eliminazione viene abilitata solo dopo l'eliminazione di tutti i server registrati dall'insieme di credenziali.
    - **Vault credentials**. Usare questa voce del menu di riepilogo rapido per configurare le credenziali di insieme.

4. Fare clic su **Protected Items** per visualizzare gli elementi di cui è stato eseguito il backup dai server. Questo elenco è solo a scopo informativo.

    ![Elementi protetti](./media/backup-configure-vault/RS_protecteditems.png)

5. Fare clic su **Servers** per visualizzare i nomi dei server registrati nell'insieme di credenziali. Da questo punto è possibile eseguire le attività seguenti:

    - **Consenti ripetizione della registrazione**. Quando questa opzione è selezionata per un server, è possibile utilizzare la procedura di registrazione guidata nell'agente per registrare nuovamente il server con l'insieme di credenziali per il backup. La ri-registrazione di un server può essere necessaria per un errore nel certificato, oppure se è stato necessario ricreare il server. La ri-registrazione è consentita una sola volta per nome server.
    - **Delete**. Consente di eliminare un server dall'insieme di credenziali per il backup. Tutti i dati archiviati associati al server verranno eliminati immediatamente.

        ![Deleted Server](./media/backup-configure-vault/RS_deletedserver.png)

##Passaggi successivi

- Per ulteriori informazioni sul servizio Backup di Azure, vedere [Panoramica di Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425). 
- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=GIT-SubDir-->