<properties
	pageTitle="Backup di Azure - creare un archivio di backup e specificare la ridondanza dell'archiviazione"
	description="Informazioni su come creare un archivio di backup, specificare le opzioni di ridondanza di archiviazione in Azure Backup"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/08/2015"
	 ms.author="prvijay"/>

# Creare un archivio di Backup
Per eseguire il backup di file e dati dal Server di Windows o System Center Data Protection Manager (SCDPM) in Azure o durante il backup di macchine virtuali IaaS di Azure, è necessario creare un archivio di backup nell'area geografica in cui si desidera archiviare i dati.

In questa esercitazione verrà illustrata la creazione dell'insieme di credenziali che si utilizzerà per archiviare i backup.

1. Accedi al [portale di gestione](https://manage.windowsazure.com/)
2. Fare clic su **nuovo** -> **Data Services** -> **servizi di ripristino** -> **Backup Vault** e scegliere **Creazione rapida** <br/> ![Creare un archivio][1]

3. Per il **nome** parametro, immettere un nome descrittivo per identificare le credenziali per il backup. Questo deve essere univoco per ogni sottoscrizione.

4. Per il **area** parametro, selezionare l'area geografica per l'archivio di backup. La scelta determina geografica a cui viene inviati i dati di backup. Scegliendo un geograficamente vicino alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

5. Fare clic su **Crea insieme di credenziali** per completare il flusso di lavoro. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per controllare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. <br/> ![Creazione di archivio][2]

6. Dopo aver creato l'archivio di backup, un messaggio indicherà l'insieme di credenziali è stato creato correttamente e verrà elencato nelle risorse per i servizi di ripristino come **Active**. <br/> ![Creazione dello stato dell'insieme di credenziali][3]


## Backup di Azure - opzioni di ridondanza di archiviazione

Il momento migliore per identificare l'opzione di ridondanza di archiviazione è subito dopo la creazione dell'insieme di credenziali e prima che tutti i computer vengono registrati nell'insieme di credenziali. Una volta che un elemento è stato registrato nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificato.

Le esigenze aziendali determina la ridondanza di archiviazione di archiviazione Azure Backup back-end. Se si utilizza Azure come endpoint di archiviazione del backup primario (ad esempio, esegue il backup in Azure da un Server Windows), è necessario considerare di prelievo (impostazione predefinita) l'opzione di archiviazione con ridondanza geografica. Questo viene visualizzato sotto il **Configura** opzione dell'insieme di credenziali per il Backup. <br/> ![ARCHIVIAZIONE CON RIDONDANZA GEOGRAFICA][4]

### Archiviazione con ridondanza geografica (GRS)
Con tale tipo di archiviazione vengono conservate sei copie dei dati. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, archiviando i dati nell'archiviazione, Backup di Azure garantisce che i dati sono durevoli in due aree distinte.

### Archiviazione con ridondanza locale (LRS)
Con l'archiviazione con ridondanza locale vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di una singola struttura di una singola area. Questa opzione di replica protegge i dati dai normali errori hardware, ma non dagli errori di un'intera unità Azure.

Se si utilizza Azure come endpoint di archiviazione dei backup terziario (ad esempio, si utilizza SCDPM per disporre di un backup locale copia locale e l'utilizzo di Azure per la conservazione a lungo termine deve), è consigliabile scegliere l'archiviazione con ridondanza locale dal **Configura** opzione dell'insieme di credenziali per il Backup. In questo modo i costi di archiviazione dei dati in Azure, fornendo un livello inferiore di durata per i dati che possono essere accettabili per le copie terziarie. <br/> ![ARCHIVIAZIONE CON RIDONDANZA LOCALE][5]




## Nota

+ A partire da marzo 2015, i clienti non è un livello di programmazione (ad esempio: PowerShell) modalità di creazione di un archivio di backup.

+ La ridondanza di archiviazione deve essere selezionata subito dopo la creazione dell'insieme di credenziali e prima che tutti i computer vengono registrati nell'insieme di credenziali. Una volta che un elemento è stato registrato nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificato.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png

<!---HONumber=GIT-SubDir--> 