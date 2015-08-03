<properties
   pageTitle="Backup di Azure: Creare un insieme di credenziali per il backup e specificare la ridondanza di archiviazione"
   description="Informazioni su come creare un insieme di credenziali per il backup e specificare le opzioni di ridondanza di archiviazione in Backup di Azure"
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

# Creare un insieme di credenziali per il backup
Per eseguire in Azure il backup di file e dati da Windows Server o da System Center Data Protection Manager (SCDPM) oppure il backup di macchine virtuali IaaS, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui si desidera archiviare i dati.

Questa esercitazione illustra la creazione dell'insieme di credenziali da usare per archiviare i backup.

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).
2. Fare clic su **Nuovo** -> **Servizi dati** -> **Servizi di ripristino** -> **Insieme di credenziali per il backup** e scegliere **Creazione rapida**. <br/> ![Crea insieme di credenziali][1]

3. Per il parametro **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup. È necessario che il nome sia univoco per ogni sottoscrizione.

4. Per il parametro **Area** selezionare l'area geografica per l'insieme di credenziali per il backup. La scelta determina l'area geografica a cui vengono inviati i dati di backup. Scegliendo un'area geografica vicina alla propria posizione, è possibile ridurre la latenza di rete durante il backup in Azure.

5. Fare clic su **Crea insieme di credenziali** per completare il flusso di lavoro. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. <br/> ![Creazione dell'insieme di credenziali][2]

6. Dopo la creazione dell'insieme di credenziali per il backup, verrà visualizzato un messaggio di conferma. L'insieme di credenziali risulterà elencato come **Attivo** nelle risorse di Servizi di ripristino. <br/> ![Creazione dello stato dell'insieme di credenziali][3]


## Backup di Azure: opzioni di ridondanza di archiviazione

È consigliabile identificare l'opzione di ridondanza di archiviazione subito dopo la creazione dell'insieme di credenziali e prima che i computer vengano registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

La ridondanza di archiviazione dell'archiviazione back-end di Backup di Azure è determinata in base alle esigenze aziendali. Se si usa Azure come endpoint primario di archiviazione dei backup (se ad esempio si esegue il backup in Azure da Windows Server), è consigliabile scegliere l'opzione predefinita di archiviazione con ridondanza geografica. Questa opzione viene visualizzata nella sezione **Configura** dell'insieme di credenziali per il backup. <br/> ![Archiviazione con ridondanza geografica][4]

### Archiviazione con ridondanza geografica (GRS)
Con tale tipo di archiviazione vengono conservate sei copie dei dati. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, grazie all'archiviazione con ridondanza geografica, Backup di Azure assicura la durabilità dei dati in due aree distinte.

### Archiviazione con ridondanza locale (LRS)
Con l'archiviazione con ridondanza locale vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di una singola struttura di una singola area. Questo tipo di archiviazione protegge i dati dai comuni errori hardware, ma non dagli errori di un'intera struttura Azure.

Se si usa Azure come endpoint terziario di archiviazione dei backup (se ad esempio si usa SCDPM per avere una copia locale del backup e si usa Azure per la conservazione a lungo termine), è consigliabile scegliere l'archiviazione con ridondanza locale nell'opzione **Configura** dell'insieme di credenziali per il backup. In questo modo vengono ridotti i costi di archiviazione dei dati in Azure e viene offerta una durabilità dei dati inferiore che può essere accettabile per le copie terziarie. <br/> ![Archiviazione con ridondanza locale][5]




## Nota

+ A partire da marzo 2015, i clienti non dispongono di una modalità di creazione di un insieme di credenziali per il backup a livello di codice, ad esempio PowerShell

+ La ridondanza di archiviazione deve essere selezionata subito dopo la creazione dell'insieme di credenziali e prima che i computer vengono registrati nell'insieme di credenziali. Dopo la registrazione di un elemento nell'insieme di credenziali, l'opzione di ridondanza di archiviazione è bloccata e non può essere modificata.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png
 

<!---HONumber=July15_HO4-->